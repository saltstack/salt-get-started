# SaltStack Get Started Guide Source

This repository contains the content source files for the [SaltStack Get Started Guide](https://docs.saltstack.com/en/getstarted/).

Contributions to fix typos and other errors are welcome.

## Source File Format

The source files are in [Pandoc's Markdown](http://pandoc.org/README.html#pandocs-markdown) with the `markdown+pipe_tables` & `backtick_code_blocks` extensions enabled.

Code samples that contain Jinja must be wrapped in Jinja `{% raw %}` and `{% endraw %}` statements since the source files are sent though Jinja when the site is generated.

## Preview

There are many tools that can generate a preview from markdown so you can verify formatting, URLs, and so on. If you have [pandoc](http://pandoc.org/) installed, you can quickly generate HTML using a command similar to:

``` bash
pandoc *.md > out.html
```

## YAML Frontmatter

The YAML frontmatter and processing directives can be safely ignored (processing directives are contained within `{: :}`). A few topics contain variables with content for modal windows in the front matter, so there is occasionally a need to make updates here.
