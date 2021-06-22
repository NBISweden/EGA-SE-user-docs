# Encryption of files for EGA-SE in Bianca

This document is a how-to-guide on encrypting files in the Bianca sensitive data
analysis system according to the
[crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/)
standard for submission to the SDA-SE service. The SDA provides permanent secure
archiving and sharing of all types of potentially identifiable human genetic and
phenotypic data.

## Login to UPPMAX

Bianca is part of UPPMAX, so start by logging in to UPPMAX. If you need a
refresher, here are some helpful links:

- [Uppmax Getting Started](https://www.uppmax.uu.se/support/getting-started/)

- [Uppmax User Guides](https://www.uppmax.uu.se/support/user-guides/)

- [Bianca User Guide](https://www.uppmax.uu.se/support/user-guides/bianca-user-guide/)

## Install crypt4gh on bianca

### Get the crypt4gh singularity image

Singularity is the recommended way to use crypt4gh on Bianca. Follow the general
[Singularity setup instructions](singularity.md) to get the `crypt4gh` tool that
you need to move to bianca for encryption, as well as the `s3cmd` tool that you
will need later for submission.

### Download the crypt4gh public key

For the SDA to be able to understand the encrypted files when they are uploaded,
they need to be encrypted with the correct public key. This key can be
downloaded from this repository with this command:
```bash
wget https://raw.githubusercontent.com/NBISweden/EGA-SE-user-docs/main/crypt4gh_key.pub
```

### Upload keys and image to the wharf

Bianca uses a system called the wharf to transfer files without having a direct
connection to the internet. You can upload files to this system from the outside
using sftp, or from uppmax by simply mounting the wharf drives. The guide for
transferring files from and to Bianca can be found
[here](https://www.uppmax.uu.se/support/user-guides/transit-user-guide/).

From a terminal window, run the following commands to upload your singularity
container and public key.

* Login to transit running:
  ```bash
  ssh [username]@transit.uppmax.uu.se
  ```

* Mount to the bianca project folder using the following command
  ```bash
  mount_wharf [project_name]
  ```
  where `[project_name]` is your bianca project, formatted like 'sens20XXX'.

  **Note** mounting the wharf follows the same procedure as logging in to
  Bianca, so you will need to provide your password along with your two-factor
  authentication code.

* Copy the image and public key files to the project drive in the wharf using the `cp` command:

  ```bash
  cp crypt4gh* ~/<project_name>/
  ```

### Move keys and image into Bianca

You can now log out from the wharf and log in to bianca to transfer the files
out of the wharf and in to your project.

* Login to bianca running:

  ```bash
  ssh -A [username]-[project_name]@bianca.uppmax.uu.se
  ```
  (Remember to use your two-factor authenticator along with your password)

* Copy the files from
  `/proj/[project_name]/nobackup/wharf/[username]/[username]-[project_name]`
  to the folder you want to store the image using the `cp` command:

  ```bash
  cp /proj/[project_name]/nobackup/wharf/[username]/[username]-[project_name]/crypt4gh* .
  ```

Running the `ls` command in this folder, you should be able to see the
`crypt4gh`, and `crypt4gh_key.pub` files.

## Encrypt your sensitive data

Now you have the public key, and the tools you need, so it's time to encrypt the
submission files. An encryption key will be created automatically by the tool,
but if you prefer to use a specific key, you can provide one using the `--sk`
argument.

* (optional) Create a key pair

  The creation of a key pair is very simple using `crypt4gh`. Run the following
  command, using any name instead of [my-key]:
​
   ```bash
   crypt4gh generate --name=[my-key]
   ```

   To verify that the key pair was created, run the `ls` command and make sure
   the keys you specified exist in the folder.

* Encrypt the files

   Now that you have the public key, and the tools you need, you can encrypt the
   submission files. An encryption key will be created automatically by the
   tool, but if you prefer to use a specific key, you can provide one using the
   `-s` argument.

   ```bash
   ./crypt4gh encrypt -p crypt4gh_key.pub -f [my-file]
   ```

   To encrypt with a given secret key use:

   ```bash
   ./crypt4gh encrypt -p crypt4gh_key.pub -f [my-file] -s [my-key].sec.pem
   ```

## Move encrypted files to the wharf for upload

Moving the files back uses the same steps as uploading, but "backwards"

1) On Bianca, move the encrypted files to your wharf directory:
   ```bash
   mv *.c4gh /proj/[project_name]/nobackup/wharf/[username]/[username]-[project_name]
   ```

2) Log back in to UPPMAX transit and mount the wharf drive again:
   ```bash
   ssh [username]@transit.uppmax.uu.se
   mount_wharf [project_name]
   ```

   The encrypted files should be secure enough to keep on UPPMAX while they are
   uploaded to the submission system. If you prefer to make the submission from
   a different system than UPPMAX, see the
   [Bianca user guide](https://www.uppmax.uu.se/support/user-guides/bianca-user-guide/)
   on how to download the encrypted files from the wharf using sftp.

Once the submission ready files are downloaded, head over to the
[submitting](README.md#submitting) guide to continue.
