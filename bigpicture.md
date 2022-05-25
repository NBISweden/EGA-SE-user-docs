# BigPicture-SE-user-docs

This repository contains user information on how to submit files to the Swedish
Big Picture node. This document serves as an outline of the submission process, and links to more detailed guides on how to perform the individual steps for creating a submission.

Currently there exist two ways to perform the submission process to the Swedish Big Picture node. 

# The automated way

The first and recommended one,uses the sda-cli tool developed by the Swedish Big Picture team. This solution can be used in any system with internet access and only requires downloading a binary file from [here](https://github.com/NBISweden/sda-cli/releases). This way is recommended for regular users, as well as for systems where the user lacks installation rights. Detailed instructions can be found [here](https://github.com/NBISweden/sda-cli/).

# The manual way

Alternatively, systems administrators and/or people who want to have more control over the data submission process can follow the manual steps. This requires the installation of two tools and the detailed instructions can be found in the following sections.

## Preparing the system

Submitting data requires two tools, [crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/) for encrypting the data, and [s3cmd](https://s3tools.org/s3cmd) for uploading to the Swedish Big Picture node.

To prepare the system follow the instructions [here](binaries.md). This is a **required step**
in order to setup your system with the required software.

## Encrypting

The first step of a submission is to encrypt the submission data files with the
`crypt4gh` encryption tool. This can be limited to these two or three steps:

 - Download the crypt4gh public key

   For the Swedish Big Picture node to be able to understand the encrypted files when they are uploaded, they need to be encrypted with the correct public key. This key can be downloaded from this repository with this command:
   ```bash
   wget https://raw.githubusercontent.com/NBISweden/EGA-SE-user-docs/main/crypt4gh_bp_key.pub
   ```

 - (Optional) Create a personal key pair

   The creation of a key pair is very simple using crypt4gh. Run the following command, replacing [my-key] with the name of the key (can be anything) and specifying a passphrase when requested:
   
   ```bash
   crypt4gh generate --name=[my-key]
   ```

   To verify that the key pair was created, run the `ls` command and make sure the keys you specified exist in the folder.

 - Encrypt the files

   Now that you have the public key, and the tools you need, you can encrypt the submission files. An encryption key will be created automatically by the tool, but if you prefer to use a specific key, you can provide one using the `-s` argument.

   ```bash
   ./crypt4gh encrypt -p crypt4gh_bp_key.pub -f [my-file] [-s [my-key].sec.pem]
   ```
   where `crypt4gh_bp_key.pub` is the crypt4gh public key you downloaded in the previous step.

## Submitting

Once your files are encrypted, you are almost ready to start submitting to the
Swedish Big Picture node. There is just one more thing that is needed; checksums for the files.

### Get checksums of the files

Once the files are uploaded, they need to be validated, which will require you to enter md5 and sha256 checksums for each file. The checksums will also be included in the metadata that will be submitted with the files.

To prepare for this later step, it's advised to create checksums for all files as you upload them.

Check our short guide on [Calculating checksums of files](checksums.md) for details.

### Get the configuration file

The s3cmd tool requires a configuration file with the relevant settings. You can get the configuration file by logging in with your account [here](https://login.bp.nbis.se/). In case you do not have an Elixir ID, feel free to create an account using one of the available options.

If you choose not to use the downloaded configuration file, we recommend setting the multipart chunk size significantly higher than the default 5 Mbyte. It can be set up to 2 Gbytes but values around 500 Mbyte are recommended.

**NOTE**: The following section requires the usage of `[username]` when uploading data.
The username refers to the value of the `secret_key` in the downloaded configuration file. 
Make sure to get it from the configuration file and use it in all `s3cmd` commands.

### Upload the file(s)

S3 allows for **optional** creation of folders. Folder creation is automatic when adding a directory name to a file upload.

For example, if `file1.c4gh` should be stored under the `experiment1` folder,
the command would look like:

```bash
./s3cmd-2.2.0/s3cmd -v -c /path/s3-config put [my-path-to-file1.c4gh] "s3://[username]/experiment1/file1.c4gh"
```

while in case no folder needs to be created, the command would look like:

```bash
./s3cmd-2.2.0/s3cmd -v -c /path/s3-config put [my-path-to-file1.c4gh] "s3://[username]/file1.c4gh"
```

Once the upload is finished, make sure the file was uploaded, by running the
following command:

```bash
./s3cmd-2.2.0/s3cmd -v -c [my-path-to-s3-config] ls "s3://[username]/[my-s3-path]/"
```

You should be able to see the file and potentially others stored in the same
location.

