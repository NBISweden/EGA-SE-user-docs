

# Encryption and upload of files to EGA-SE
​
This document is a how-to-guide on encrypting and uploading files to
the EGA-SE service. It is meant for submitters to EGA-SE service and
it covers usage from different operating systems. EGA provides
permanent secure archiving and sharing of all types of potentially
identifiable human genetic and phenotypic data. In EGA-SE's case, an
[s3](https://en.wikipedia.org/wiki/Amazon_S3) storage backend is used.
Submitted files need to be encrypted according to the
[crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/)
standard. ​

# Requirements

Here we'll outline procedure for encrypting and uploading using tools such as
`crypt4gh` and `s3cmd`.​ 

There are different ways of getting this software and the way of
getting it easiest will depend. 


Here we'll document two cases where either container images are run
with `singularity` (this is probably easiest if your files are
available on a compute cluster). The other alternative would be
installing the tools "natively".


## Using singularity

If you have `singularity` installed and operational, you can download
ready-made images for [`crypt4gh`](https://github.com/NBISweden/EGA-SE-user-docs/releases/download/crypt4gh/crypt4gh-1.0.0.sif) and
[`s3cmd`](https://github.com/NBISweden/EGA-SE-user-docs/releases/download/2.1.0/s3cmd_latest.sif).

Once you've donwloaded those, you can rename them and use them as fix
the permission, allowing you to use them as normal commands. To do
this and amend `PATH` so the commands are available.

So:

```bash
wget https://github.com/NBISweden/EGA-SE-user-docs/releases/download/2.1.0/s3cmd_latest.sif
wget https://github.com/NBISweden/EGA-SE-user-docs/releases/download/crypt4gh/crypt4gh-1.0.0.sif
chmod a+x s3cmd_latest.sif crypt4gh-1.0.0.sif
mv s3cmd_latest.sif s3cmd
mv crypt4gh-1.0.0.sif crypt4gh
PATH=$PATH:$(pwd)
```

Now you should be able to go on to [encrypting your files](#encrypt).

## Install natively

To encrypt, we'll document the usage of the `crypt4gh` command
line tool. That tool requires **Python 3.6** or newer and
**virtualenv**. For uploading files we recommend **s3cmd** for linux
and osx based systems and **s3Express** for Windows systems (**s3cmd**
might work on windows as well). ​

### Check if Python is installed

To check whether Python is already installed in your machine, you can
run the following command in the terminal:

```bash
python --version
```

If case you have Python 2 installed as well (common in macOS), you can run:
 
 ```bash
python3 --version
```
The output of the command should signify the version of python, in case it is installed, like:

    Python 3.6.1
​

If the python is installed and the version is 3.6 or newer, you can
skip the next step.
 
### Install Python 3.6 or newer
​

The first step is to install python 3.6 or newer. The following links
provide guides for the various types of operating systems. If python
3.6 is already installed, you may skip this step.

* [Linux](https://docs.python-guide.org/starting/install3/linux/) (for apt-based systems)
​
* [macOS](https://docs.python-guide.org/starting/install3/osx/) (with homebrew)
​
* [Windows](https://www.python.org/downloads/windows/). 

Select the python executable suitable for your computer and make sure
pip is installed.

### Install virtualenv
​

Virtualenv simplifies the installation of the needed tools.
Instructions for installing virtualenv for the various types of
operating systems can be found
[here](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/).

In short: 
* Linux and macOS
```bash
python3 -m pip install --user --upgrade pip
python3 -m pip install --user virtualenv
```
​
* Windows
```bash
py -m pip install --upgrade pip
py -m pip install --user virtualenv
```
​
### Create a virtual environment
​
Once virtualenv is installed, create a virtual environment which
includes Python: ​

* Linux and macOS
​
```bash
python3 -m venv env
```
​
* Windows
​
```bash
py -m venv env
```

This will create a folder called *env* under the current directory. To
activate the environment run the following command from the terminal
(this command has to be run everytime a new terminal window is
opened):​

* Linux and macOS
​
```bash
source env/bin/activate
```
​
* Windows
​
```bash
.\env\Scripts\activate
```
​
### Installing the crypt4gh encryption tool
​
The next step is to install
[crypt4gh](https://github.com/EGA-archive/crypt4gh), by running the
following command:
​
```bash
pip install crypt4gh (potentially add specific version here)
```
​
To verify the installation run:
​
```bash
crypt4gh --version
```
​
which should return something like:
​
    GA4GH cryptographic utilities (version 1.4)
​
### Installing the s3 command line tool (linux/osx)
​
`s3cmd` is a command line client for interacting with an S3 backend
(the type of storage used in EGA-SE). A complete usage guide for
`s3cmd` can be found [here](https://s3tools.org/usage). Note that not
all commands are allowed when interacting with EGA-SE's S3. To install
`s3cmd` run the following command:

```bash
pip install s3cmd (potentially add specific version here)
```
​
To verify the installation run:
* macOS and Linux

```bash
s3cmd --version
```
​
which should return something like
​
    s3cmd version 2.1.0
​
* Windows

In order to be able to run the s3cmd on Windows, there is one
additional step. Navigate to the env\Scripts folder and copy the name
of the s3cmd file to s3cmd.py. That can be with the following command:

```bash
copy s3cmd s3cmd.py
```
Now you can execute s3cmd with python. Run:

```bash
python s3cmd.py --version
```
which should return something like
​
    s3cmd version 2.1.0
​

## Encrypt
​

In order to encrypt the file(s) with crypt4gh, you need the EGA-SE
public encryption key. Optionally, you can use a key of your own to
sign the file (allows proving it came from someone with control of ​the
key). If you don't provide a secret key when encrypting, `crypt4gh`
will generate one on the fly.


### (Optional) Create a personal key pair
​

**Note**: This step assumes that the previous ones have been followed
and that the virtual environment is activated or that `PATH` has been
amended with the path where the container is.

The creation of a key pair is very simple using crypt4gh. Run the
following command, replacing [my-key] with the name of the key (can be
anything) and specifying a passphrase when requested:
​
```bash
crypt4gh-keygen --pk <my-key>.pub --sk <my-key>.sec 
```

To verify that the key pair was created, run the `ls` command and make
sure the keys you specified exist in the folder

### Download the public key
​
Next, download the public key (crypt4gh_key.pub) from this repository
and place it in the same location as the keys from the previous step

### Encrypt the file(s)
​

Now you have all the keys and tools needed, you can run the following
command in order to encrypt the file (if you want to use a secret key 
you have):

​
```bash
crypt4gh encrypt --sk <my-key>.sec --recipient_pk ega-se.key < <my-file> > <my-encypted-file>.c4gh
```

If you don't have a secret key, just remove that part and run:

```bash
crypt4gh encrypt --recipient_pk ega-se.key < <my-file> > <my-encypted-file>.c4gh
```


### Get the md5 checksum of the file
​
The submission portal requires an `md5` checksum for uploading the
files. In order to get that run the following command, if you want the
md5 in the terminal:

​* Linux and macOS

```bash
md5sum <my-file> <my-encrypted-file>.c4gh
```
​
or this command if you want to store the md5 in a file:
​
```bash
md5sum <my-file> <my-encrypted-file>.c4gh > <my-md5>.md5
```

and the following command if you want the sha256 in the terminal:

```bash
shasum -a 256 <my-file> <my-encrypted-file>.c4gh
```

or this command if you want to store the sha256 in a file:
​
```bash
shasum -a 256 <my-file> <my-encrypted-file>.c4gh > <my-sha256>.sha256
```

​* Windows

To get the checksums using windows, first install the
[md5checker](https://pypi.org/project/md5checker/) running:

```bash
pip install md5checker
```

Then get the md5 checksum for each file with:

```bash
md5checker <my-file>
```

and the sha-256 checksum with:

```bash
md5checker <my-file> -a sha256
```
​
## Upload
​
**Note**: This step assumes that the previous ones have been followed
and that the virtual environment is activated
​
### Get the configuration file
​
The s3cmd tool requires a configuration file with the relevant
settings. You can get the configuration file by logging in with your
Elixir ID [here]() 

If you choose not to use the downloaded configuration file, we
recommend setting the multipart chunk size significantly higher than
the default 5 Mbyte. It can be set up to 2 Gbytes but values above 100
Mbyte probably do very little to improve throughput.

### Upload the file(s)​

S3 allows for **optional** creation of folders and that can be
achieved by defining the path in the my-s3-path variable

​
```bash
s3cmd -v -c <my-path-to-s3-config> put <my-encrypted-file>.c4gh "s3://<my-username>/<my-s3-path>/<my-encrypted-file>.c4gh"
```
​
for example, if the file should be stored under the "experiment1"
folder, the command would look like:
​
```bash
s3cmd -v -c /path/s3-config put file1.c4gh "s3://user/experiment1/file1.c4gh"
```
​
while in case no folder needs to be created, the command would look like:
​
```bash
s3cmd -v -c /path/s3-config put file1.c4gh "s3://user/file1.c4gh"
```
​
Once the upload is finished, make sure the file was uploaded, by
running the following command:
​
```bash
s3cmd -v -c <my-path-to-s3-config> ls "s3://<my-username>/<my-s3-path>/"
```
​
You should be able to see the file and potentially others stored in
the same location.

## Important notes

- To deactivate the virtualenv run the following command:
​
```bash
deactivate
```
​
