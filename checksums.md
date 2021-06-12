
# Calculating checksums of files

Checksums are short, fixed length strings that can be calculated from
arbitrarily large inputs. They are commonly used to validate the contents of
files, as the checksum will change considerably even with minimal changes to the
input. More information can be found on
[Wikipedia](https://en.wikipedia.org/wiki/Checksum).

## Linux and OS X

The commands for calculating checksums are very similar on Linux and OS X. They
use the same tool for calculating sha256 checksums, but linux use the `md5sum`
command for md5 checksums, while the OS X command is `md5`.

```bash
# Calculate md5 checksum on linux
md5sum <my-file> <my-encrypted-file>.c4gh
# ... and OS X
md5 <my-file> <my-encrypted-file>.c4gh
# The sha256 checksum command is the same on both platforms
shasum -a 256 <my-file> <my-encrypted-file>.c4gh
```
​
The results can be written to a file using the `>` operator:

ex.
```bash
md5sum <my-file> <my-encrypted-file>.c4gh > <my-files>.md5
```

## Windows

**Note** We couldn't find a windows computer to test on, so please tell us if
these instructions don't work anymore.

Installing on windows is done using Windows PowerShell. You can find PowerShell
by right-clicking on the windows menu button. The tool we recommend for windows
is [md5checker](https://pypi.org/project/md5checker/).

To run `pip` to install `md5checker` you need to run PowerShell as
Administrator (unless you run in the virtual environment), but once the tool is
installed, you should run `md5checker` in a normal non-Administrator shell for
security reasons.

```PowerShell
pip install md5checker
```
Then get the md5 checksum for each file with:

```PowerShell
md5checker <my-file>
```

and the sha-256 checksum with:

```PowerShell
md5checker <my-file> -a sha256
```
​
The `>` operator works the same in PowerShell as it does in linux and OS X, so
you can use the same syntax to write the output to a file.

```PowerShell
md5checker <my-file> > <my-file>.md5
```