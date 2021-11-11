# EGA-SE-user-docs

This repository contains user information on how to submit files to the swedish
Sensitive Data Archive (SDA) service. This document serves as an outline of the submission process, and links to more detailed guides on how to perform the individual steps for creating a submission.

## Preparing the data

There are currently no restrictions on what kinds of files are allowed to upload
to the SDA. Our recommendation is to still spend some time to see if there are
file types intended for archiving available for your data, and use those. For
example - for alignment data, use the cram format instead of sam/bam.

## Preparing the system

Submitting data requires two tools,
[crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/)
for encrypting the data, and [s3cmd](https://s3tools.org/s3cmd) for uploading to
the SDA.

**Note** If you are using Bianca, see the [Encrypting on Bianca](bianca.md)
instructions which cover both building the crypt4gh tool and transferring it to
bianca.

There are two ways to prepare the system:

1) [Using singularity](singularity.md)

2) [Installing natively](installing.md)

## Encrypting

The first step of a submission is to encrypt the submission data files with the
`crypt4gh` encryption tool. This is fairly straight forward in the general case,
but needs a few extra steps on Bianca as the system isn't directly
connected to the internet. This process is detailed in the
[Encrypting on Bianca](bianca.md) document, which covers the following steps:

  - [Login to uppmax](bianca.md#login-to-uppmax)

  - [Install crypt4gh on Bianca](bianca.md#install-crypt4gh-on-bianca)

  - [Encrypt your sensitive data](bianca.md#encrypt-your-sensitive-data)

  - [Move the encrypted files to the wharf](bianca.md#move-encrypted-files-to-the-wharf-for-upload)

Outside of Bianca, this can be limited to these two or three steps:

 - Download the crypt4gh public key

   For the SDA to be able to understand the encrypted files when they are
   uploaded, they need to be encrypted with the correct public key. This key can
   be downloaded from this repository with this command:
   ```bash
   wget https://raw.githubusercontent.com/NBISweden/EGA-SE-user-docs/main/crypt4gh_key.pub
   ```

 - (Optional) Create a personal key pair​

   The creation of a key pair is very simple using crypt4gh. Run the following
   command, replacing [my-key] with the name of the key (can be anything) and
   specifying a passphrase when requested:
​
   ```bash
   crypt4gh generate --name=[my-key]
   ```

   To verify that the key pair was created, run the `ls` command and make sure
   the keys you specified exist in the folder.

 - Encrypt the files

   Now that you have the public key, and the tools you need, you can encrypt the
   submission files. An encryption key will be created automatically by the
   tool, but if you prefer to use a specific key, you can provide one using the
   `-s` argument.

   ```bash
   ./crypt4gh encrypt -p crypt4gh_key.pub -f [my-file] [-s [my-key].sec.pem]
   ```

## Submitting

Once your files are encrypted you are almost ready to start submitting to the
SDA. There is just one more thing that is needed; checksums for the files.

### Get checksums of the files
​
Once the files are uploaded they need to be validated, which will require you to enter md5 and sha256 checksums for each file. To prepare for this later step, it's adviced to create checksums for all files as you upload them.

Check our short guide on [Calculating checksums of files](checksums.md) for details.
​
### Get the configuration file
​
The s3cmd tool requires a configuration file with the relevant settings. You
can get the configuration file by logging in with your Elixir ID
[here](https://login.sda.nbis.se/).

If you choose not to use the downloaded configuration file, we recommend
setting the multipart chunk size significantly higher than the default 5 Mbyte.
It can be set up to 2 Gbytes but values above 100 Mbyte probably do very little
to improve throughput.

### Upload the file(s)​

**NOTE: At the time of writing there is an issue with uppmax that prevents
singularity images from reading the wharf directory from the uppmax side. If you
are submitting data from bianca, you need to install s3cmd natively using
[this guide](installing.md) instead of using the singularity option.**

S3 allows for **optional** creation of folders. Folder creation is automatic
when adding a directory name to a file upload.
​
For example, if `file1.c4gh` should be stored under the `experiment1` folder,
the command would look like:
​
```bash
s3cmd -v -c /path/s3-config put file1.c4gh "s3://[username]/experiment1/file1.c4gh"
```
​
while in case no folder needs to be created, the command would look like:
​
```bash
s3cmd -v -c /path/s3-config put file1.c4gh "s3://[username]/file1.c4gh"
```
​
Once the upload is finished, make sure the file was uploaded, by running the
following command:
​
```bash
s3cmd -v -c [my-path-to-s3-config] ls "s3://[username]/[my-s3-path]/"
```
​
You should be able to see the file and potentially others stored in the same
location.
