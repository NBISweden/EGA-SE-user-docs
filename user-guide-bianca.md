# Encryption of files for EGA-SE in Bianca
This document is a how-to-guide on encrypting files for the EGA-SE service . It is meant for submitters to EGA-SE service and it assumes the files are stored in Bianca. EGA provides permanent secure archiving and sharing of all types of potentially identifiable human genetic and phenotypic data. Submitted files need to be encrypted according to the [crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/) standard.

## Pull the images with singularity

### Pull images
* Login to Rackham running:
```bash
ssh <username>@rackham.uppmax.uu.se
```
Navigate to the folder you want to pull the images.
* To download the crypt4gh key generator image run the following command in the terminal:
```bash
singularity pull docker://nbisweden/ega-crypt4gh:crypt4gh-keygen-1.0.0
```
* To download the crypt4gh image run the following command in the terminal:
```bash
singularity pull docker://nbisweden/ega-crypt4gh:crypt4gh-1.0.0
```
Now you should be able to see two files with `.sif` extensions in the folder when running the `ls` command.

### Move the singularity images to Bianca
You can move the singularity images to Bianca using wharf. The guide for transferring files from and to Bianca can be found [here](https://www.uppmax.uu.se/support/user-guides/transit-user-guide/).

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
* Copy image files using the cp command and replacing to the correct locations:
```bash 
cp ega-crypt4gh_crypt4gh-1.0.0.sif <project_name>/
cp ega-crypt4gh_crypt4gh-keygen-1.0.0.sif <project_name>/
```

In another terminal
* Login to bianca running:
```bash
ssh -A <username>-<project_name>@bianca.uppmax.uu.se
```
* Copy image files from `/proj/<project_name>/nobackup/wharf/<username>/<username>-<project_name>` to the folder you want to store the images using the cp command and replacing to the correct locations:
```bash 
cp cp /proj/<project_name>/nobackup/wharf/<username>/<username>-<project_name>/ega-crypt4gh_crypt4gh-1.0.0.sif .
cp cp /proj/<project_name>/nobackup/wharf/<username>/<username>-<project_name>/ega-crypt4gh_crypt4gh-keygen-1.0.0.sif .
```
Running the `ls` command in this folder, you should be able to see the two `.sif` files.

## Key pair generation
In order to encrypt the file(s) with crypt4gh, you need both a personal key pair and the EGA-SE public encryption key.
### Create a personal key pair
The creation of a key pair is very simple using crypt4gh. Run the following command, in the folder containing the `.sif` files, replacing [my-key] with the name of the key (can be anything) and specifying a passphrase when requested:
```bash
singularity run ega-crypt4gh_crypt4gh-keygen-1.0.0.sif --pk <my-key>.pub --sk <my-key>.sec
```
To verify that the key pair was created, run the `ls` command and make sure the keys you specified exist in the folder
### Download the public key
Next, download the public key (crypt4gh_key.pub) from this repository and place it in the same location as the keys from the previous step. In order to do that, repeat the process from the `Move the singularity images to Bianca` section above.

### Encrypt the file(s)
Now you have all the keys and tools needed, you can run the following command in order to encrypt the file:

```bash
singularity run ega-crypt4gh_crypt4gh-keygen-1.0.0.sif --sk <my-key>.sec --recipient_pk ega-se.key < <my-file> > <my-encypted-file>.c4gh
```

