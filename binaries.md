# Setting up with binaries

The crypt4gh and s3cmd tools are available as binaries, so you can download the tar
files, extract them and use the tools. Depending on your operating system, select
the appropriate version and download it from the command line with `wget`.

## Download the tools
The list of releases for `s3cmd` for Linux based systems can be found [here](https://github.com/s3tools/s3cmd/releases/tag/v2.2.0), for Windows systems [here](https://www.s3express.com/download.htm),
while for the `crypt4gh` [here](https://github.com/elixir-oslo/crypt4gh/releases/tag/v1.3.0).

For example, if you your operating system is Linux based, download the files using:
```bash
wget https://github.com/s3tools/s3cmd/releases/download/v2.2.0/s3cmd-2.2.0.tar.gz
wget https://github.com/elixir-oslo/crypt4gh/releases/download/v1.3.0/crypt4gh_linux_x86_64.tar.gz
```

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

Now you should be able to go on to
[encrypting your files](README.md#encrypting).