

# 2020-07-21 Verifying Amazon Glacier backup from my Synology NAS
I backup my Synology NAS using Synologys builtin Glacier Backup utility. I wanted to verify that I could recover any backed up file **without using my Synology NAS**.   
These were the steps:
1. Download and analyze the index, which is an **sqlite** database file.
2. Choose a file in the index database, grab the archive id.
3. Download the archive with the chosen id. Rename it to the right filename.
4. Compare it to the original file on the NAS.

## Format and structure of Synology Glacier Backups
If you are familiar with Amazon Glacier, you know it is a long-term, slow-access data storage solution that has two main concepts: **Vaults**, which contain **Archives**. Synology Glacier Backup stores each backed up file as an Archive object, but gives it an id (128 random characters)...

```markdown
Syntax highlighted code block
```


## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/plengqui/plengqui.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
