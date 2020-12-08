# dbd.py (drive-by-download)
Generate a post exploit script to download an arbitrary file using HTML5's Blob object (https://developer.mozilla.org/en-US/docs/Web/API/Blob).

# install

```shell
$ git clone https://github.com/demetrius-ford/dbd.git
$ cd dbd/ && pip3 install -r requirements.txt
```

# usage

```bash
Usage: dbd.py [OPTIONS]

  Generate a drive-by-download XSS payload.

Options:
  --file-type [doc|pdf|xls|docx|xlsx|xlsm]
  --payload FILE
  --help                          Show this message and exit. 
```
