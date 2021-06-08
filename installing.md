# Installing dependencies natively

Installing the required tools locally takes a bit more work than running through
singularity, but thanks to python virtual environments it's still quite straight
forward.

Two tools will be needed for SDA submission:

 - [crypt4gh](https://www.ga4gh.org/news/crypt4gh-a-secure-method-for-sharing-human-genetic-data/)
   for encrypting files for storage in the secure archive,
 - [s3cmd](https://s3tools.org/s3cmd) for uploading the encrypted files (or
   [s3express](https://www.s3express.com/) on windows).

## Installing crypt4gh

The `crypt4gh` tool requires **Python 3.6** or newer, with **python3-venv**.

### Check if Python is installed

To check whether Python is already installed in your machine, you can run the
following command in the terminal:

```bash
python3 --version
```

The output of the command should signify the version of python, in case it is installed, like:

```bash
    Python 3.6.1
```

​
If python isn't installed, or that the version isn't 3.6 or newer, go to the
[Official python download page](https://www.python.org/downloads/) and follow
the instructions to install it.
​
### Create a virtual environment
​
The python virtual environment tools comes bundled with python since version
3.4, and ​the syntax is the same on all platforms.

```bash
python3 -m venv venv
```

This will create a folder called `venv` under the current directory. To activate
the environment run the following command from the terminal (this command has
to be run everytime a new terminal window is opened):​

* Linux and macOS
​
  ```bash
  source venv/bin/activate
  ```

* Windows

  ```PowerShell
  .\venv\Scripts\activate
  ```

  **Note** PowerShell doesn't normally allow script execution, and you may need
  to run
  ```PowerShell
  Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
  ```
  before you can activate the virtual environment.

### Installing crypt4gh
​
The next step is to install [crypt4gh](https://github.com/EGA-archive/crypt4gh).

There are two versions of crypt4gh that are functionally identical, but
implemented in different languages; a python version, and a golang version.

#### Installing the python version

The python version of crypt4gh is available through the python package manager
[pip](https://pypi.org/project/pip/), and can be installed in the virtualenv by
running the following command:
​
```bash
pip install crypt4gh
```
​
#### Installing the golang version

Instructions for installing the golang version of crypt4gh is available
[here](https://pkg.go.dev/github.com/elixir-oslo/crypt4gh#readme-installation)
from the golang package page. Binary releases can also be downloaded from the
[github releases page](https://github.com/elixir-oslo/crypt4gh/releases).

Once you have installed or downloaded the tool from either source, verify the
installation with:
​
```bash
crypt4gh --version
```
​
which should return something like:
​
```bash
    GA4GH cryptographic utilities (version 1.4)
```

## Installing the s3 command line tool
​
`s3cmd` is a command line client for interacting with an S3 backend
(the type of storage used in EGA-SE). A complete usage guide for
`s3cmd` can be found [here](https://s3tools.org/usage). Note that not
all commands are allowed when interacting with EGA-SE's S3. To install
`s3cmd` run the following command:

```bash
pip install s3cmd
```
​
To verify the installation run:
* macOS and Linux

  ```bash
  s3cmd --version
  ```

  which should return something like

  ```bash
  s3cmd version 2.1.0
  ```
​
* Windows

  In order to be able to run the s3cmd on Windows, there is one additional
  step. Navigate to the `env\Scripts` folder and copy the name of the s3cmd file
  to s3cmd.py. That can be with the following command:

  ```PowerShell
  copy s3cmd s3cmd.py
  ```
  Now you can execute s3cmd with python. Run:

  ```PowerShell
  python s3cmd.py --version
  ```
  which should return something like
​
  ```PowerShell
  s3cmd version 2.1.0
  ```
​