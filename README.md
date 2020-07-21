

# 2020-07-21: Reverse-engineering Amazon Glacier Backup on Synology NAS
This brief post describes how Synology implements backup to Amazon Glacier, and how you can recover your files from Glacier without using any Synology product.  

I backup my Synology NAS using Synology's built-in Glacier Backup utility. To verify that I could recover my backups in the event that the NAS was stolen or destroyed, I wanted to perform a recovery **without using my Synology NAS**. After some googling, it was clear that there is no such utility outside the Synology products ecosystem. 

These were the steps to verify an Amazon Glacier Backup from my Synology NAS:
1. Download and analyze the mapping index, which is an **sqlite** database file.
2. Choose a file in the index database and grab the archive id.
3. Download the archive with the chosen id. Rename it to the right filename.
4. Compare it to the original file on the NAS.

## Format and structure of Synology Glacier Backups
If you are familiar with Amazon Glacier, you know it is a long-term, slow-access data storage solution that has two main concepts: **Vaults**, which contain **Archives**. Synology Glacier Backup stores each backed up file from your NAS as an Archive object, but gives it a nonsensical id of 128 random characters. The archive id is stored with the original filename and other metadata in an index file in a separate Vault. So, in your AWS Glacier console you will see two vaults:  
- 'NAS_0012345ABCDE_1' this is the main Vault with all backed up files, without any metadata.
- 'NAS_0012345ABCDE_1_mapping' this Vault contains one single Archive, which is the index file.  
The index file is actually an sqlite database. The mapping is in the 'file_info_tb' table, which has the following columns: 
|key|shareName|basePath|archiveID|lastBkpTime|fileSize|archiveVersion|current|checksum| 

## Performing a restore test of a single file
I downloaded the single archive from the NAS_0012345ABCDE_1_mapping vault, and renamed it from its 128-character nonsensical name to 'glacierbackup-index.sqlite' on my pc, for readability.
To download archives from Amazon Glacier, I used [FastGlacier](https://fastglacier.com/). There are other alternative clients, any client should work. 
Python code to read the index databse file:
```python
import sqlite3
conn = sqlite3.connect('glacierbackup-index.sqlite',isolation_level=None,
                       detect_types=sqlite3.PARSE_COLNAMES)
c = conn.cursor()
for row in c.execute("SELECT basePath FROM file_info_tb WHERE archiveID='f6-qTd5rn-MORE-GOBBLYGOOK-OKb3RC_zZG7PxpjPtkfBFzNpw'"):
    print(row)
for row in c.execute("SELECT archiveID FROM file_info_tb WHERE basePath='important/photos/march 2015/IMG_20150507_194814.JPG'"):
    print(row)
conn.close()
```
Using the archive id from the index database, you can download the archive from the NAS_0012345ABCDE_1 vault. 
As a final step, you probably want to rename the downloaded file from 'f6-qTd5rn-MORE-GOBBLYGOOK-OKb3RC_zZG7PxpjPtkfBFzNpw' to its original filename. Now you can verify that it is identical to the original backed up file on your NAS. 

