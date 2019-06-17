#	bgztail

Continuous tail Perl script (à la `tail -f`) for *bgzipped* files.

[bgzip](https://github.com/samtools/htslib/blob/develop/bgzip.c) compression format **is compatible with gzip** (uses [standard extensions in RFC 1952](https://tools.ietf.org/html/rfc1952#page-6) to zip in blocks, not in a continuos stream), so bgzipped files can be uncompressed with gunzip.

Nonetheless, `bgztail` can only tail bgzipped files, because gzip implies a complete file reading to reach the tail. This can be cumbersome with compressed files of various Gigabytes in size...

`bgztail` **does not** need the index file (as with `bgzip -i`) to be created.

Note that `bgztail` outputs warnings to *STDERR* and file contents to *STDIN*.

The script is compatible with Windows' Perl. A *bgztail.bat* demo file is provided in order to use *bgztail* command in MS-DOS: please, substitute complete bgztail file path inside it, and move *bgztail.bat* to a path on your *%PATH%*.

# Use

    bgztail [-#CfhilLv] FILE

  -#: a number: tail only that number of lines, and exit.

  -C: do not use colors on error/warning messages.

  -f: output appended data as the file grows (like `tail -f`).
      If FILE doesn't exist yet, bgztail waits for its creation.

  -h: show this help.

  -i: do not print incomplete bgzip blocks: instead wait until the bgzip block (<64KiB) is complete for printing.

  -l: show internal/verbose decompression processing logs.

  -L: do not show neither warnings nor errors (be careful!).

  -v: print version and exit.

  --: (two hyphens) to allow file names beginning with '-'.

# Examples

Usual case: use bgztail as a "*tail -f*" on a bgzip-compressed file:
  
    $ bgztail -f /var/log/my_log.gz

or tail just some lines of it and exit (by default 10 lines):

    $ bgztail /var/log/my_log.gz

If a *gzip* (not *bgzip*) file is selected, a warning is shown and the script exists. Nonetheless, *bgzip* compression format **is compatible with gzip**, so bgzipped files can be uncompressed with *gunzip*. But `bgztail` can only tail bgzipped files, because *gzip* implies a complete file reading to reach the tail.

    $ ./bgztail.pl my_file.gz

    bgztail
    "my_file.gz" :

    'my_file.gz' is not a valid bgzip file


To show only internals of bgzip format handling (not-contents output is always printed to *STDERR*):
  
    $ bgztail -l /var/log/my_log.gz > /dev/null

    bgztail -l
    "/var/log/my_log.gz" :

    file size = 2103546
    Searching header @2070778
    Header found @2081847
    STATE 0 0 ; Br = 0, Fr = 21699
    Reading @2081847, (18)
    STATE 2 0 ; Br = 21681, Fr = 21681
    Reading @2081865, (21681)
                Br = 0, Fr = 0, #blks = 1

    Starting decompression @2081847

    STATE 0 0 ; Br = 0, Fr = 21699
    Reading @2081847, (18)
    STATE 2 0 ; Br = 21681, Fr = 21681
    Reading @2081865, (21681)
                Br = 0, Fr = 0, #blks = 1
    waiting for input...

# Notes on implementation

* *bgzip* actually does not set the timestamp in gzip headers, so parsing them is quite simple for this reason (timestamp is always *0x0*).

* even incomplete (growing) *bgzip* blocks are supported. Even though incomplete blocks are unlikely to live growing *in the wild*, this can be useful to determine possible corruptions of static *bgzip* files (use *-l* parameter).

* bgzipped-file-to-tail may not exist when running: the script will patiently wait for its creation when `-f` is indicated.

# Author

[circulosmeos](https://github.com/circulosmeos)

# License

[GPL v3](http://www.gnu.org/licenses/gpl-3.0.html)
