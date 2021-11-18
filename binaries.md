# Setting up with binaries

The crypt4gh and s3cmd tools are available as binaries, so you can download the tar
files, extract them and use the tools. Depending on your operating system, select
the appropriate version and download it from the command line with `wget`.

## Download the tools
The list of releases for `s3cmd` for Linux based systems can be found [here](https://github.com/s3tools/s3cmd/releases/tag/v2.2.0), for Windows systems [here](https://www.s3express.com/download.htm),
while for the `crypt4gh` [here](https://github.com/elixir-oslo/crypt4gh/releases/tag/v1.3.0).

For example, if your operating system is Linux based, download the files using:
```bash
wget https://github.com/s3tools/s3cmd/releases/download/v2.2.0/s3cmd-2.2.0.tar.gz
wget https://github.com/elixir-oslo/crypt4gh/releases/download/v1.3.0/crypt4gh_linux_x86_64.tar.gz
```

## Check the python version
The `s3cmd` tool requires python version higher than 3. Check that this is the case in your system using
```bash
python --version
```
If the version is `2.X.X`, follow the instructions in the next section to [create a virtual environment](binaries.md#create-a-virtual-environment)
with python 3. Otherwise, move to section [Extract the executables](binaries.md#extract-the-executables).

### Create a virtual environment
​
The python virtual environment tools come bundled with python since version
3.4, and ​the syntax is the same on all platforms.

```bash
python3 -m venv venv
```

This will create a folder called `venv` under the current directory. To activate
the environment run the following command from the terminal (this command has
to be run every time a new terminal window is opened):​

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
If the version is `2.X.X`, 

## Extract the executables
To extract the binary for the `crypt4gh` use the tar command 
```bash
tar -xvzf crypt4gh_linux_x86_64.tar.gz
```
The `crypt4gh` executable should be in the same directory. Make sure you have the correct version running
```bash
./crypt4gh --version
```

To extract the binary for the `s3cmd` use the tar command 
```bash
tar -xvzf s3cmd-2.2.0.tar.gz
```
The executable should be under the `s3cmd-2.2.0` directory. Make sure you have the correct version running
```bash
./s3cmd-2.2.0/s3cmd --version
```
If this command returns an error, check the [troubleshooting](binaries.md#troubleshooting) section for possible solutions

Now you should be able to go on to
[encrypting your files](README.md#encrypting).

## Troubleshooting

- If the `s3cmd` commands return the following error
    ```bash
    ImportError trying to import dateutil.parser.
    Please install the python dateutil module:
    $ sudo apt-get install python-dateutil
    or
    $ sudo yum install python-dateutil
    or
    $ pip install python-dateutil
    ```

    run the following command inside the environment and try again
    ```bash
    pip install python-dateutil
    ```