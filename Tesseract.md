# Installation Tesseract

# Related Sources
- https://arnowelzel.de/en/full-text-search-in-nextcloud


## Installation

1. `sudo apt install tesseract-ocr tesseract-ocr-deu tesseract-ocr-eng`
2. `sudo systemctl daemon-reload`
3. `sudo systemctl restart elasticsearch`
4. check if the language files are in place: `sudo ls /usr/share/tesseract-ocr/4.00/tessdata/`
5. configure this app in the Full text search Admin panel
