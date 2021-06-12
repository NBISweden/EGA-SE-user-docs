# Setting up with singularity

The crypt4gh tool is available as a singularity container, so if you have
`singularity` installed and operational, you can download ready-made images for
[`crypt4gh`](https://github.com/NBISweden/EGA-SE-user-docs/releases/download/crypt4gh/crypt4gh-1.0.0.sif)
and
[`s3cmd`](https://github.com/NBISweden/EGA-SE-user-docs/releases/download/2.1.0/s3cmd_latest.sif)
. These can be downloaded from the commandline with `wget`:
```bash
wget https://github.com/NBISweden/EGA-SE-user-docs/releases/download/2.1.0/s3cmd_latest.sif
wget https://github.com/NBISweden/EGA-SE-user-docs/releases/download/crypt4gh/crypt4gh-1.0.0.sif
```

**Note** If you prefer, you can also build the crypt4gh image locally using:
```bash
singularity pull docker://nbisweden/ega-crypt4gh:crypt4gh-1.0.0
```
(This takes up to ten minutes and produces a singularity image called
`ega-crypt4gh_crypt4gh-1.0.0.sif` instead of `crypt4gh-1.0.0.sif`).

Once you've downloaded these, they can be used directly through singularity by
using the `singularity run` syntax,

ex.
```bash
singularity run crypt4gh.sif --help
```

By renaming the singularity images and setting the correct execute permissions
these can be made to look more like native programs as well. First give execute
permission to the files:
```bash
chmod a+x s3cmd_latest.sif crypt4gh-1.0.0.sif
```
This allows you to use the files like:
```bash
./crypt4gh-1.0.0.sif --help
```
instead of running through singularity.

You can also rename the files to have simpler filenames:
```bash
mv s3cmd_latest.sif s3cmd
mv crypt4gh-1.0.0.sif crypt4gh
```

Now you should be able to go on to
[encrypting your files](README.md#encrypting).