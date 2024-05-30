---

copyright:
years: 2024, 2024
lastupdated: "2024-05-30"

keywords: DevSecOps, Cli,

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Adding new parsers for incident processing
{: #devsecops-cocoa-cli-parser}

When working with OnePipeline, the `cocoa incident process` command processes scan results and creates incident issues in a specified respository for each vulnerability that is found. You can extend the functionality of the `cocoa incident process` command by adding support for new parsers. By enabling new parsers, you can ensure the efficient tracking and resolution of any problems that are identified.

When you add a new parser, the system must support parsing the report in order to create incidents or issues for the failures that are found.
{: important}


## Before you begin
{: #devsecops-cocoa-cli-before}

Adding a new parser, involves updating the processing of the `cocoa incident process` command to interpret the failure report that is generated during the scan. The parser must be able to extract relevant information from the report, such as the type of failure, error messages, timestamps, affected components, and any other pertinent details.

Be sure that you understand how the issues are opened:

When working with the parser, there can be multiple JSON scan results. These files contribute to the creation of issues. If any of the files do not conform to the expected format, they will be skipped during the issue creation stage. When the parser has extracted all of the necessary information, the cocoa incident process command uses the data to create issues by populating fields such as title, description, severity level, assignee, and any other required fields specified by the tracking system. Additional information can be added to the issue as a comment to provide better context. Typically, it is preferred to have one issue per vulnerability as it allows for a finer level of control over the lifecycle of each vulnerability. However, whether to adopt this approach or group multiple vulnerabilities into a single issue depends on the specific tool and context.

Be sure that your scanner is able to product a JSON file that can be used as input for the parser with the following schema:

* Results: The JSON scan result is expected to have an array-like structure that allows for easy looping over the elements to create issues.
* Severity: The JSON scan result should include some indication of the severity level for each element. If the severity is not explicitly mentioned, it defaults to "High". Severity levels determine the due dates for the issues created.

To get started you will create a new parser file, integrate it with your code, and then test that everything is set up correctly.

## Creating a new parser file
{: #devsecops-cocoa-parser-file-creation}

To incorporate a new parser, you must first specify the changes for it in the `src/incident/parser` folder.

1. Define interfaces for the JSON to be given as input for the parser. Be sure to specify the expected type for each element.

   If the element types include objects, you might need to create multiple interfaces.
   {: tip}

   Use the following sample JSON to write an interface that defines the desired structure of the JSON input for the parser.

	```json
	{
		"id": "AAAA",
		"val": {
			"name": "BBB",
			"place": "Y"
		},
		"type": "CCCC"
	}
	```
	{: codeblock}


	For the above example JSON, define two interfaces, named Foo and Bar as shown. Notice that `Bar` is used as type of `val` in `Foo`

	```json
	interface Bar {
		name: string;
		place: string;
	}

	interface Foo {
		id: string;
		val: Bar;
		type: string;
	}
	```
	{: screen}

2. Define a function by using assert statements that verifies whether the given JSON file adheres to the expected format. Only the JSON files that satisfy all of the assert criteria will be processed by the parser. All other JSON values will be disregarded.

	Continuing in the example from the previous step, you might format your assert statements similar to the following example:

	```json
	function assertIsBar(data: unknown, path: string): asserts data is Bar{
		assertIsObject(data, path);

		assertHasProperty(data, 'name', path);
		assertIsString(data.name, '${path}.name);

		assertHasProperty(data, 'place', path);
		assertIsString(data.place, '${path}.place);
	}

	function assertIsFoo(data: unknown, path: string): asserts data is Foo {
		assertIsObject(data, path);

		assertHasProperty(data, 'id', path);
		assertIsString(data.id, '${path}.id);

		assertHasProperty(data, 'type', path);
		assertIsString(data.type, '${path}.type);
	}
	```
	{: screen}

3. Define the converter function that transforms the JSON that meets the checks outlined in the previous step by using the following codeblock as a formatting guide.

	```json
		interface IncidentContext {
			subject: string;
			drillDownUrl: string;
			type: ReportType | string;
		}

		interface Incident extends IncidentContext {
			id: string;
			description: string;
			exempt: boolean;
			gracePeriod: string;
			dueDate: string;
			severity: SeverityLevel;
			labels?: string[];
			comment?: string;
			assignee?: string[];
			pipelineRunUrl?: string;
			createDateProd?: string;
			createDateBeforeProd?: string;
			fixAvailable?: boolean;
			legacy?: boolean;
		}
	```
	{: codeblock}

	As an example, the following code snippet shows what the conversion of the Foo object to an incident might look like.

	```json
	const convertFooToIncident = (v: Foo, context: IncidentContext): Incident => ({
		id: getIdFromFoo(v), /// string   />
		severity: convertFooInfoToSeverityLevel(v),  /// SeverityLevel enum  />
		description: formatDescription(v),  /// string  />
		exempt: false,  /// boolean  />
		gracePeriod: 'n/a',  /// string  />
		dueDate: 'n/a',  /// string  />
		labels: getLabels(v),  /// string [] | undefined  />
		fixAvailable: getIsFixAvailable(v),  /// boolean | undefined  />
		... context,
	})
	```
	{: screen}

4. Write a function that parses the results by using the previously defined assert functions.

	If the assert criteria are met, the parsed data is passed as an input to the converter function, which generates a list of incidents. Each incident corresponds to a unique vulnerability. Finally the function returns an array containing these incidents. For an example, see the following code snippet.

	```json
	function assertIsFooArray(result: unknown): asserts result is Foo[] {
		assertIsArray(result, '.');
		for (const [index, foo] of Object.entries(result)) {
			const path= '.[${index}]';
			assertIsFoo(foo, path);
		}
	}

	const parseFooResult = (data: unknown, context: IncidentContext): Incident[] => {
		assertIsFooArray(data);
		const incidents = data.flatMap(v => convertFooToIncident(v, context));

		const seen = new Set ();
		const deduplicated = incidents.filter((incident) => {
			if (seen.has(incident.id)) return false;
			seen.add(incident.id);
			return true;
		});

		return deduplicated;
	}
	```
	{: screen}




## Integrating the parser
{: #devsecops-cocoa-parser-integrate}

After you create the parser, you must integrate into the workflow of the CLI. You can use the following steps as a guide to do the integration.

1. Include the new scan report type as the enum `ReportType` with the `src/index/index.ts` file. Additionally, update the `const formattedReportType` with the description of the new scan report type.

	```json
	export enum ReportType {
		CRA = 'cra',
		VA = 'va',
		XRAY = 'xray',
		PEER_REVIEW = 'peer-review',
		GOSEC = 'gosec',
		OWASP_ZAP = 'owasp-zap',
		OWASP_ZAP_UI = 'owasp-zap-ui',
		SONARQUBE = 'sonarqube',
		TWISTLOCK = 'twistlock',
		MEND = 'mend',
		CHECKOV = 'checkov',
		CRATF = 'cra-tf',
		TFSEC = 'tfsec',
		FIPS = 'fips-scanner',
		CONSTAST_SAST = 'constrast-sast',
		DETECT_SECRETS = 'detect-secrets',
		SYSDIG = 'sysdig',
		FOO = 'foo'
	}
	```
	{: screen}

2. Incorporate the newly introduced report type into the `SupportedTypes` defined within the file located at `src/commands/incident/process-command.ts`.

	```json
	eport const supportedTypes = [
		ReportType.CRA,
		ReportType.VA,
		ReportType.XRAY,
		ReportType.PEER_REVIEW,
		ReportType.GOSEC,
		ReportType.OWASP_ZAP,
		ReportType.OWASP_ZAP_UI,
		ReportType.SONARQUBE,
		ReportType.TWISTLOCK,
		ReportType.MEND,
		ReportType.CHECKOV,
		ReportType.CRATF,
		ReportType.TFSEC,
		ReportType.FIPS,
		ReportType.CONSTAST_SAST,
		ReportType.DETECT_SECRETS,
		ReportType.SYSDIG,
		ReportType.FOO
	]
	```
	{: screen}

3. In the existing switch statement for the recently added report type, include a fresh case. Invoke the parsing function for each entry, as shown in the following example.

```json
case ReportType.CRA:
	incidents = parseCRAResult(data, context);
	issueLabels.push('vulnerability');
	break;
...
case ReportType.FOO:
	incidents = parseFOOResult(data, context);
	issueLabels.push('vulnerability');
	break;
```
{: screen}

## Testing the new parser
{: #devsecops-cocoa-parser-testing}

To test the newly created parser, you can use the following steps as a guide.

1. Add sample JSON files in the `src/incident/parser/reports` folder for testing the functionality of the parser by creating two files - one where the parser
should success and the other where the parser should fail. For the failing JSON file, you can prefix its name with `bad_xxxx` to follow the existing naming convention.

2. Create a file name `parser.test.ts` in the `src/incident/parser` folder that contains the tests for the various functions that were exported by the new
parser. Use the sample JSON files added in the previous step to test the functionality of the new parser. For example, you might create a file called `Foo.test.ts` for
the file that tests the functions `assertIsFoo`, `assertIsBar`, `assertIsFooArray`, `convertFooToIncident`, and `parseFooResult`. Additionally, add tests for the `bad_` JSON file.

3. To facilitate result comparison, add the expected result to the `src/incident/parser/snapshots` folder by creating an empty file. During execution,
the expected output will be displayed. Verify the expected result and include it in the previously created empty file.


## Calling the parser
{: #devsecops-cocoa-parser-call}

The cocoa command which calls the parser is `cocoa incident process` command. The process command is responsible for calling the handler function that is defined
in the previously created file `src/commands/incident/process-command.ts.`. The `IncidentService` class that is defined in the `src/incident/service.ts` file is responsible for creating and updating issues that are based on teh incident array that is returned by the parser.

To call the parser, you can use the following command:

```bash
$ cocoa incident process   <options>   <path to result file>
```
{: codeblock}

| Flag  | Description | Required |
|:------|:------------|:---------|
| `--type` | The type of scan. Options include `cra` and `va`. | Required |
| `--subject` | The subject of the scans; whether a repo or image name | Required |
| `--drilldown-url` | The URL that points to where the incident was found. Potentially a pipeline run, a commit hash or an image URL. | Required |
| `--set-grace-period` | Whether there should be a grace period between incident issues. | Optional |
| `--git-provider` | Git service provider. By default this is set to `github`. | Optional |
| `--org` | The repository organization where the incident issue is opened. | Optional |
| `--repo` | The repository name where the incident issue is opened. | Optional |
| `--label` | The label or labels that you want to add to the incident issue. For example: `--label=foo`. | Optional |
| `--assignee` | The GitHub username of the person who should be assigned the incident issue. For example `--assignee=jane-doe`. | Optional |
| `--git-token-path` | The path of the GitHub token. | Optional |
| `--git-api-url` | The GitHub API URL. | Optional |
| `--custom-exempt-label` | Defines the custom label with which the incident issue has been marked as exempt. | Optional |
| `--custom-comment` | Additional text to be added to issue comments. | Optional |
{: caption="CLI Options for the cocoa incident process command" caption-side="top"}

An example call might look similar to the following codesnippet.

```bash
$ cocoa incident process   --type va   --subject us.icr.io/service-image   --drilldown-url us.icr.io/service-image@sha256:digest   path/to/scan-result.json
```
{: screen}

For more information around the `cocoa incident process` command, please refer to the following documentation:

* [IBM Cloud DevSecOps CLI - Incident Process](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#incident-process)
* [IBM Cloud DevSecOps CLI - Incident Process (Legacy)](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#incident-process-legacy)
* [IBM Cloud DevSecOps - Incident Issues](https://cloud.ibm.com/docs/devsecops?topic=devsecops-incident-issues)
* [IBM Cloud DevSecOps - Non-Incident Issues](https://cloud.ibm.com/docs/devsecops?topic=devsecops-non-incident-issues)
