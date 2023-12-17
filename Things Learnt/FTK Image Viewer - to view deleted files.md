FTK Imager: User Interface (UI)

|   |
|---|
|FTK Imager's interface is intuitive and user-friendly. It displays an "x" icon next to deleted files and includes key UI components vital for its functionality. These components are:<br><br>1. **Evidence Tree pane**: Displays a hierarchical view of the added evidence sources such as hard drives, flash drives, and forensic image files.<br>2. **File List pane:** Displays a list of files and folders contained in the selected directory from the evidence tree pane.<br>3. **Viewer pane:** Displays the content of selected files in either the evidence tree pane or the file list pane.|

![FTK Imager User Interface (UI)](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/9d6cf661865c811b7bcda308496e2602.png)

  

FTK Imager: Previewing Modes

|   |
|---|
|FTK Imager presents three distinct modes for displaying file content, arranged sequentially from left to right, each represented by icons enclosed in yellow:<br><br>1. **Automatic mode:** Selects the optimal preview method based on the file type. It utilises Internet Explorer (IE) for web-related files, displays text files in ASCII/Unicode, and opens unrecognised file types in their native applications or as hexadecimal code.<br>2. **Text mode:** Allows file contents to be previewed as ASCII or Unicode text. This mode is useful for revealing hidden text and binary data in non-text files.<br>3. **Hex mode:** Displays files in hexadecimal format, providing a detailed view of file data at the binary (or byte) level.|

![FTK Imager Previewing Mode - Automatic/IE](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/95d24cdc6d382c1b89f5af989c1be9b7.png)

  

Use `Ctrl + F` to search for specific text within a file while in either text or hex preview mode.

![FTK Imager Previewing Mode - Find Xiaomi in Hex mode](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/8ad39dd0de241fae81acedf698579775.png)

  

FTK Imager: Recovering Deleted Files and Folders

To view and recover deleted files, expand directories in the File List pane and Evidence Tree pane. Right-click and select `Export Files` on individual files marked with an "x" icon or on entire directories/devices for bulk recovery of files (whether deleted or not).

![FTK Imager - Recovering deleted file](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/4e915c80d85efa7c992d76021fc6f6bd.png)

  

![FTK Imager - Recovered deleted file](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/1fd732351d04f0636e19f0b0bce64001.png)

  

FTK Imager: Verifying Drive/Image Integrity

To verify the integrity of a drive/image, click on it from the **Evidence Tree** pane and navigate to `File > Verify Drive/Image` to obtain its MD5 and SHA1 hashes.

![FTK Imager - Verify Drive/Image](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/2e379d61330d4c6d9c09ebd30dc601d8.png)

  

![FTK Imager - Verify Drive/Image in Progress](https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/cd2b401779b4bde0d861e12449d46967.png)

  

Practical Exercise With FTK Imager

Use what you have learned today to analyse the contents of the USB drive and answer the questions below.

**IMPORTANT:** Please use **Hex** mode instead of **Text** mode to avoid crashing FTK Imager when processing files as text.