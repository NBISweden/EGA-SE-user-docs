# Encryption of files for EGA-SE in Bianca

This document is a how-to-guide on encrypting files for the EGA-SE service . It is meant for submitters to EGA-SE service and it assumes the files are stored in Bianca. EGA provides permanent secure archiving and sharing of all types of potentially identifiable human genetic and phenotypic data. Submitted files need to be encrypted according to the [crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/) standard.


## Get images to bianca

### Pull images
* Login to Rackham running:
```bash
ssh <username>@rackham.uppmax.uu.se
```
Navigate to the folder you want to pull the images.
* To download the crypt4gh image run the following command in the terminal:
```bash
singularity pull docker://nbisweden/ega-crypt4gh:crypt4gh-1.0.0
```
Now you should be able to see a file with `.sif` extensions in the folder when running the `ls` command.

### Move the singularity image to Bianca
**Note**: If you already know how to move files to bianca, you can skip the deta
ils for this part
You can move the singularity image to Bianca using wharf. The guide for transferring files from and to Bianca can be found [here](https://www.uppmax.uu.se/support/user-guides/transit-user-guide/).

In summary, the steps needed are the following:
In a terminal window
* Login to transit running:
```bash
ssh <username>@transit.uppmax.uu.se
```
* Mount to the bianca project folder using the following command
```bash 
mount_wharf <project_name>
```
where [project_name] in the form of sens20XXX.
* Copy image files using the cp command and replacing to the correct location:
```bash 
cp ega-crypt4gh_crypt4gh-keygen-1.0.0.sif <project_name>/
```

In another terminal
* Login to bianca running:
```bash
ssh -A <username>-<project_name>@bianca.uppmax.uu.se
```
* Copy the image file from `/proj/<project_name>/nobackup/wharf/<username>/<username>-<project_name>` to the folder you want to store the image using the cp command and replacing to the correct locations:
```bash 
cp /proj/<project_name>/nobackup/wharf/<username>/<username>-<project_name>/ega-crypt4gh_crypt4gh-1.0.0.sif .
```
Running the `ls` command in this folder, you should be able to see the `.sif` file.

##  Download the public key
In order to encrypt the file(s) with crypt4gh, you need the EGA-SE public encryption key.

### Move EGA key to Bianca

Download the public key (crypt4gh_key.pub) from this repository and place it in the same location as the images from the previous step. 
* Download the key in rackham running:
```bash
wget https://raw.githubusercontent.com/NBISweden/EGA-SE-user-docs/main/crypt4gh_key.pub
```
* Repeat the process from the `Move the singularity image to Bianca` section above, this time copying the key instead of the image.

## Encrypt the file(s)

Now you have the key and tools needed, you can run the following command in order to encrypt the file:
**Warning:** Please make sure to use different names (at least different extensions) for [my-file] and [my-encrypted-file] to avoid destroying your original file!

```bash
singularity run ega-crypt4gh_crypt4gh-keygen-1.0.0.sif encrypt --recipient_pk crypt4gh_key.pub < <my-file> > <my-encypted-file>.c4gh
```

