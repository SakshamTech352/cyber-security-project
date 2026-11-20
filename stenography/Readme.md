# StegoLab: Steganography Multi-Test Tool

A browser-based toolkit for hiding, recovering and detecting secret data in images and text. Built as a cyber security lab project. It is a single HTML file with no server, no dependencies and no uploads: everything runs locally in your browser.

![HTML](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)
![Status](https://img.shields.io/badge/status-educational-blue)

## Features

| Tab | What it does |
|---|---|
| **Hide in image** | Embeds a text message into an image using LSB (least significant bit) replacement. Choose 1 to 3 bits per channel, see capacity and image-quality (PSNR), and download the result as PNG. |
| **Extract from image** | Recovers a hidden message, automatically trying 1, 2 and 3 bits per channel. |
| **Invisible text** | Hides a secret inside ordinary text using zero-width Unicode characters, and scans pasted text to reveal it. |
| **Detect & analyze** | A steganalysis panel: header check, LSB balance, chi-square pair test, appended-data check, SHA-256 hash, an LSB-plane visualisation and an overall risk verdict. |

Extra features:
- Optional password encryption (AES-256-GCM) so only the intended recipient can read the message
- Built-in self-test button that checks hide and extract round trips
- Responsive layout for phones, tablets and desktops, with light and dark themes
- Five interface themes (Professional, Amber Terminal, Aurora Glass, Brutalist, Blueprint) that share the same HTML and logic

## How it works

### LSB image steganography
Each pixel's red, green and blue values are numbers from 0 to 255. Replacing the lowest 1 to 3 bits of each value with message bits changes a colour by at most a few units, which is not visible to the eye. The alpha channel is not used.

Embedded data layout:

```
| 4 bytes tag | 4 bytes length (big-endian) | payload |
   STG1 / STG2
```

- `STG1`: plain message (UTF-8)
- `STG2`: password-protected message. Payload = 16-byte salt + 12-byte IV + AES-GCM ciphertext and tag

Capacity in bytes = `floor(width × height × 3 × bitsPerChannel / 8) − 8`.

### Encryption
When a password is set, a key is derived with PBKDF2 (SHA-256, 200,000 iterations) and the message is encrypted with AES-256-GCM using the browser's Web Crypto API. A wrong password fails authentication, so the tool can report it clearly.

### Zero-width text steganography
The secret is converted to bits. U+200B (zero-width space) stands for 0, U+200C (zero-width non-joiner) stands for 1, and U+2060 (word joiner) marks the start and end. The block is inserted after the first character of the cover text.

### Steganalysis checks
1. **Header check:** looks for the `STG1` or `STG2` tag in the lowest 1 to 3 bits.
2. **LSB balance:** the share of ones in the lowest bits. Random hidden data pushes it close to 50%.
3. **Chi-square pair test:** compares the counts of paired values (2k, 2k+1). A value near 1 per degree of freedom suggests the lowest bits were overwritten with random data.
4. **Appended data:** looks for bytes after the PNG `IEND` chunk or the JPEG end marker.
5. **LSB plane:** draws only the lowest bit of every pixel, so embedded data shows up as noise.
6. **Risk score:** the findings add up to a score out of 7 and a verdict of clean, suspicious or hidden data detected.

## Getting started

No installation is needed.

1. Download or clone this repository.
2. Open `stegolab.html` in any modern browser (Chrome, Edge, Firefox or Safari).

To host it for free with GitHub Pages, rename `stegolab.html` to `index.html`, then enable Pages in **Settings > Pages**.

## Usage

**Hide a message for one person**
1. Open **Hide in image**, load a cover image and type your message.
2. Enter a password and choose the bits per channel.
3. Click **Hide message** and download the PNG.
4. Send the PNG as a file or document and share the password through a different channel.

**Read a message**
1. Open **Extract from image**, load the PNG and enter the password.
2. Click **Extract message**.

**Check an image for hidden data**
1. Open **Detect & analyze** and drop the image in.
2. Read the verdict, the individual checks and the LSB plane.

## Project structure

```
.
├── stegolab.html             # Professional theme (main version)
├── stegolab-amber.html       # Amber terminal theme
├── stegolab-aurora.html      # Glass theme
├── stegolab-brutal.html      # Brutalist theme
├── stegolab-blueprint.html   # Blueprint theme
└── README.md
```

All versions contain the same markup and JavaScript; only the CSS differs.

## Limitations

- **Use PNG only.** JPEG compression, screenshots and many chat apps recompress images and destroy LSB data.
- Anyone holding the image can see that it exists. The password only protects the content.
- Very short messages in large images may not shift the statistical tests, so the detector can miss them. The header check only recognises messages made by this tool.
- This is a learning project, not an audited security product. For high-risk situations, use established, reviewed tools.

## Responsible use

Use this tool only on files you own or are authorised to test. It was built for education and security practice.

## Possible improvements

- Public-key mode so a recipient can receive messages without sharing a password
- Hiding files, not just text
- Audio steganography
- RS and sample-pair steganalysis for better detection of short messages
- Exportable analysis report

## Author

Built by **Saksham Garg**, B.Tech Computer Science Engineering, as part of a cyber security course project.

## License

Released under the MIT License. 
