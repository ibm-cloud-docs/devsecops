---

copyright:
  years: 2021, 2023
lastupdated: "2023-05-30"

keywords: DevSecOps, CI toolchain with multiple apps, multiple apps toolchain

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Decoding the GPG Key
{: #cd-devsecops-gpg-key}

Decoding a Base64 encoded GPG key can be accomplished using various methods. One of the common approaches is decoding from a file.
{: shortdesc}

## Decoding from a File
{: #multiple-apps-toolchain-customization}

To decode the GPG key from a file, follow these steps:

1. **Copy the Encoded Key to a File**: Create a file named `key.txt` and paste the Base64 encoded GPG key into it. Based on the encoder installed on your system, copy the required code snippet:

### base64:
{: #cd-encode-key-to-file-base64}

If you have base64 already installed in your system, using the following code snippet in your `key.txt` file:

````

# Define file paths
encoded_key_file="key.txt"
decoded_key_file="decoded_key.txt"

# Read the content of the file
encoded_key=$(<"$encoded_key_file")

# Decode the key using base64 command
decoded_key=$(echo "$encoded_key" | base64 -d)

# Save the decoded key to a file
echo $decoded_key > "$decoded_key_file"

# Import the decoded key into GPG
gpg --import "$decoded_key_file"

````

### If you have OpenSSL installed on your system:
{: #cd-encode-key-to-file-openssl}

If you have OpenSSL already installed in your system, using the following code snippet in your `key.txt` file:

```Define file paths
encoded_key_file="key.txt"
decoded_key_file="decoded_key.txt"

# Read the content of the file
encoded_key=$(<"$encoded_key_file")

# Decode the key using openssl command
decoded_key=$(echo "$encoded_key" | openssl base64 -d)

# Save the decoded key to a file
echo $decoded_key > "$decoded_key_file"

# Import the decoded key into GPG
gpg --import "$decoded_key_file" ```


1. Once executed, you will observe that the key has been successfully imported into your GPG key ring.
