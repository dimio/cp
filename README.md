[![Intro](https://github.com/dimio/dopaste/raw/images/dopaste-intro.png "See a DoPaste intro")][intro-img]

# What is DoPaste?

[DoPaste](https://dopaste.dimio.org) is an open-source website similar to Pastebin where you can store any piece of code, and generate links for easy sharing

However, what makes DoPaste special is that it works with **no database**, and **no back-end code**. Instead, the data is compressed and **stored entirely in the link** that you share, nowhere else!

> **Notes:**
> 
> This project is improved version of [Bokub's NoPaste](https://github.com/bokub/nopaste).
> 
> NoPaste is a copy of [Topaz's paste service][topaz-example], with a reworked design and a few additional features (syntax highlighting, line numbers, offline usage, embedding...)

### Because of this design:

-   🗑️ Your data **cannot be deleted** from DoPaste
-   🔞 Your data **cannot be censored**
-   👁️ The server hosting DoPaste (or any clone of it) **cannot read or access** your data
-   ⏳ Your data will be accessible **forever** (as long as you have the link)
-   🔀 You can access your data on **every NoPaste / DoPaste clone**, including [your own](https://github.com/bokub/nopaste/wiki/Deploy-your-own-version-of-NoPaste)
-   🔍 Google **will not index** your data, even if your link is public

## How it works

When you click on "Generate Link", DoPaste compresses the whole text using the
[LZMA algorithm](https://en.wikipedia.org/wiki/Lempel%E2%80%93Ziv%E2%80%93Markov_chain_algorithm), encodes it in
[Base64](https://en.wikipedia.org/wiki/Base64), and puts it in the optional URL fragment, after the first `#` symbol: `dopaste.dimio.org/#<your data goes here>`

When you open a link, DoPaste reads, decodes, and decompresses whatever is after the `#`, and displays the result in the editor.

This process is done entirely **in your browser**, and the web server hosting DoPaste [never has access to the fragment](https://en.wikipedia.org/wiki/Fragment_identifier)

For example, [this is the CSS code used by DoPaste][example]

## Other features

### Embedded DoPaste snippets

You can include DoPaste code snippets into your own website by clicking the _Embed_ button and using the generated HTML code.

Here is an example of generated code and how it looks (click on the screenshot to see the interactive version)

```html
<iframe width="100%"
        height="155"
        style="border-style: none"
        src="https://dopaste.dimio.org/?l=shll#XQAAAQATAQAAAAAAAAAyG8pmm6wx5WsfNxloo0TeG5PR3eUzyCswaDhV8aq1yi9Yey0phmBft5O/dGwF1rFgo8Aha2QuANm2TNcv9bU61gShwcqwYW1BcgS0kfYyLxbrHQ9xTY6Q17jGH76RQZKX1D7XzaAtAYzSqKbv46nM36N0Dzu1HNyiGCyMM98yYSb611cPvfJRhAZVFD7mUZGQvmXx/Lxh7hLelmA5/eFjhTtNVVABhx/ZBol/6ZlXUsGu+uc2bXZZUzskfMwCmK4hxuY0TwIvmySQ+S///Q/qwA=="
></iframe>
```

Render preview (clickable):
[![iframe](https://github.com/dimio/dopaste/raw/images/dopaste-bash-function.png)](https://jsbin.com/gavihuhiyu/edit?html,output)

Feel free to edit the `height` and `width` attributes, so they suit your needs

### Offline usage

When you visit DoPaste for the first time, its code is saved in your browser cache. After that, every DoPaste link you open
will load really quick, even if your internet connexion is slow.

What if you have no internet connexion at all? No problem, DoPaste will still work perfectly!

### Editor features

-   Syntax highlighting (use the language selector)
-   Enable / disable line wrapping (use the button next to the language selector)
-   Delete line (`Ctrl+D`)
-   Multiple cursors (`Ctrl+Click`)
-   Usual keyboard shortuts (`Ctrl+A`, `Ctrl+Z`, `Ctrl+Y`...)

## Maximum sizes for links

DoPaste is great for sharing code snippets on various platforms.

These are the maximum link lengths on some apps and browsers.

| App     | Max length |
|:--------|:----------:|
| Reddit  |   10,000   |
| Twitter |   4,088    |
| Slack   |   4,000    |
| QR Code |   2,610    |
| Bit.ly  |   2,048    |
| Clck.ru |   4,050    |

DoPaste noty you when generated link is too long for each App.

![Link is too long](https://github.com/dimio/dopaste/raw/images/dopaste-link-length-tooltip.png)

| Browser         | Max length                | Notes                                   |
|:----------------| ------------------------- | --------------------------------------- |
| Google Chrome   | (win) 32,779 (mac) 10,000 | Will not display, but larger links work |
| Firefox         | >64,000                   |                                         |
| Microsoft IE 11 | 4,043                     | Will not show more than 2,083           |
| Microsoft Edge  | 2,083                     | Anything over 2083 will fail            |
| Android         | 8,192                     |                                         |
| Safari          | Lots                      |                                         |

## Generate DoPaste links

DoPaste links can be created easily from your system's command line:

```bash
# Linux
echo -n 'Hello World' | lzma | base64 -w0 | xargs -0 printf "https://dopaste.dimio.org/#%s\n"

# Mac
echo -n 'Hello World' | lzma | base64 | xargs -0 printf "https://dopaste.dimio.org/#%s\n"

# Windows / WSL / Linux
echo -n 'Hello World' | xz --format=lzma | base64 -w0 | printf "https://dopaste.dimio.org/#%s\n" "$(cat -)"

# Simple Bash function (read from file)
dopaste() {
	[[ -n "${2}" ]] && DOPASTE_URL="${2}" || DOPASTE_URL='https://dopaste.dimio.org'
	FILE_EXT=$(printf '%s' "${1}" | awk -F . '{if (NF>1) {print $NF}}')
	cat "${1}" | xz --format=lzma | base64 -w0 | printf "%s/?e=%s#%s\n" "${DOPASTE_URL}" "${FILE_EXT}" "$(cat -)"
}
echo 'echo "Hello World"' > ./helloworld.sh
dopaste helloworld.sh
```

## Deploy your own version of NoPaste / DoPaste

NoPaste / DoPaste is just a bunch of static files, making it really easy to deploy on any kind of file server.

Read [the wiki](https://github.com/bokub/nopaste/wiki/Deploy-your-own-version-of-NoPaste) to see how you can deploy your own version of NoPaste / DoPaste for free using GitHub Pages

[intro-img]: https://dopaste.dimio.org/?l=perl#XQAAAQBAAgAAAAAAAAAFDycpuYU/rT8WueRzlSn1hYNLGNzssUut9mAniPSITp0LdJhMBAAkliMHbVDHLI9w3YkjSeQfsMKDxmZhy6Ej/Q1TEuKyPcYNLSH62fskiXCZZ1Bs0z+2bYl9z+t0t64TxeeskJbhXL4B/On3w+eiYY1KUmSjLBBxa68gIh5ZYVa6alk4ApIAnmZPF+T/9baQgA==
[example]: https://dopaste.dimio.org/?l=css#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
[topaz-example]: https://topaz.github.io/paste/#XQAAAQAeAQAAAAAAAAAFFgvDUiqpf8dDPleMqfsqtbQYE28suCtDTB9iyFgGByXgSRMepMuokjoACV4UPgBzwM3qhDFDQBY5MuI5f4NDfFGuKj6cS83cM6ylthwJL5z7HU0WvW+GGcTVU6R5eC4uRXash0PEpmW+xHjdql4G0LIpySla1nzRlPGTatKEY1mLhvek5D11donmchKhxP1093ql4rOxgeQOybwOtV1WF8J+b2xKUSfEjLW+waP+jy/5fwHoaSnWRiEhumhlTFWxVkg64ozlHepN1BQ08AaWzUbq4vGn5r395mSI
