# Duplicate Files Generator

Python script be used to generate files of random size, with a certain ratio of duplicate files.

Refer to the following documentation for further information:

```shell
$ ./generate_duplicate_files.py --help
usage: generate_duplicate_files.py [-h] --file-count PATH [-p filename]
                                   [--directory-min-depth DIRECTORY_MIN_DEPTH]
                                   [--directory-max-depth DIRECTORY_MAX_DEPTH]
                                   [--duplicate-file-ratio DUPLICATE_FILE_RATIO]
                                   [--file-extensions FILE_EXTENSIONS]
                                   [--file-extension-min-length FILE_EXTENSION_MIN_LENGTH]
                                   [--file-extension-max-length FILE_EXTENSION_MAX_LENGTH]
                                   [--file-name-min-length FILE_NAME_MIN_LENGTH]
                                   [--file-name-max-length FILE_NAME_MAX_LENGTH]
                                   [--file-min-size FILE_MIN_SIZE]
                                   [--file-max-size FILE_MAX_SIZE]

Duplicate Files Generator

optional arguments:
  -h, --help            show this help message and exit
  --file-count PATH     specify the number of files to generate
  -p filename, --path filename
                        specify the absolute path where to generate files
  --directory-min-depth DIRECTORY_MIN_DEPTH
                        specify the maximum number of sub-directories to
                        generate a file from the specified root path
  --directory-max-depth DIRECTORY_MAX_DEPTH
                        specify the maximum number of sub-directories to
                        generate a file from the specified root path
  --duplicate-file-ratio DUPLICATE_FILE_RATIO
                        specify the ratio of duplicate files to be generated
  --file-extensions FILE_EXTENSIONS
                        specify a comma-separated values of file extension to
                        be used when generate files (e.g., "gif,jpg,mp3")
  --file-extension-min-length FILE_EXTENSION_MIN_LENGTH
                        specify the minimum length of a file extension to
                        randomly generate
  --file-extension-max-length FILE_EXTENSION_MAX_LENGTH
                        specify the maximum length of a file extension to
                        randomly generate
  --file-name-min-length FILE_NAME_MIN_LENGTH
                        specify the minimum length of a file name to randomly
                        generate
  --file-name-max-length FILE_NAME_MAX_LENGTH
                        specify the maximum length of a file name to randomly
                        generate
  --file-min-size FILE_MIN_SIZE
                        specify the minimum size of a file to randomly
                        generate
  --file-max-size FILE_MAX_SIZE
                        specify the maximum size of a file to randomly
                        generate

```
