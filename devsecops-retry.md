---

copyright:
  years: 2024, 2024
lastupdated: "2024-01-22"

keywords: DevSecOps, retry, script

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# retry() function
{: ##devsecops-retry}

The retry() function is designed to perform a given command with a specified number of retry attempts and a delay between each attempts.

## Parameters:
{: #retry-parameters}

- `max_attempts` : Maximum no of attempts to execute the command.
- `delay`: The delay between each attempts.
- `command`: The command to be executed.

## Function Implementation:
{: #retry-function-implemenatation}

- Set local variables:
      max_attempts
      delay
      attempt_number
      exit_code
- Execution of command
- Retry loop : loop that continues until it succeeds or run till the max_attempt.

```bash
# <max_attempts>: Maximum no of attempts to execute the command.
# <delay>: The delay between each attempts.
# <command>: The command to be executed.
retry <max_attempts> <delay>  # executes the given <command to execute>
retry <max_attempts> <delay> <command to execute>
```
{: codeblock}

## Return value:
{: #retry-return-value}

-  If command `succeeds` in the attempts provided then `retry` Function returns with status ‘0’.
-  If it `fails` after all attempts , function returns with exit code.

#### Example:
{: #retry-example}

```bash
source "${ONE_PIPELINE_PATH}"/tools/retry

# set value provided as argument
<max_attempts> = 5
<delay> = 10

retry 5 10 ibmcloud_region_set "$REGISTRY_REGION"
```
{: codeblock}
