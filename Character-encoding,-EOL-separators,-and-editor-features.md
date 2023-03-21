CyberChef v10 introduces a range of new features to the Input and Output panes.

 - Status bars, showing statistics and options related to the data
 - Character encoding selection to explicitly determine how Input data should be encoded and Output data decoded
 - End of Line separator selection to improve data integrity
 - Non-printable character rendering to ensure you always know exactly what you are working with
 - Improved file loading, allowing you to edit any file you load into the tool
 - A range of modern editor features such as rectangular selections, bracket matching, and selection matching

In addition, a new contextual help feature has been added, activated by pressing `F1` while hovering over a feature that you would like to learn more about.

---

### Status bars

<img height="250" alt="Screenshot 2023-03-21 at 17 10 19" src="https://user-images.githubusercontent.com/22770796/226688108-40ad6ec7-8a39-4f93-8d31-19f34733f6a4.png" align="right">

Both the Input and Output now have status bars showing information about their data such as the total length, number of lines, current selection, character encoding and EOL separator.

<br clear="both"/>

---

### Character encoding

<img height="200" alt="Screenshot 2022-09-02 at 15 11 35" src="https://user-images.githubusercontent.com/22770796/226687070-468ae92f-1820-4ef7-b0b2-39f16baa130a.png" align="left">

<img height="350" alt="Screenshot 2022-09-02 at 15 13 31" src="https://user-images.githubusercontent.com/22770796/226687171-4bb06f0b-f4cd-4acf-b30f-f70395b1ab64.png" align="right">

You can now explicitly set which character encoding you would like your Input to be encoded with before it is processed by the Recipe. You can also select how the Output should be decoded once the Recipe has returned. The default value is 'Raw bytes' which attempts to treat every character as a byte in the range 0-255. If one of the characters has a Unicode value greater than 255, CyberChef will default to using UTF-8. This is the behaviour of CyberChef 9 and below, so it should be suitable for most cases, particularly if working with binary file data.

<br clear="both"/>

---


### End of line separators

<img height="200" alt="Screenshot 2022-09-02 at 15 02 48" src="https://user-images.githubusercontent.com/22770796/226687015-a594400f-a7cd-4c4c-9692-193d8e9e1797.png" align="left">

<img height="150" alt="Screenshot 2022-09-02 at 14 52 56" src="https://user-images.githubusercontent.com/22770796/226686626-23caffe4-329f-4cc0-8ca8-0ab6b351ef00.png" align="right">

EOL characters can now be set, with the default being the line feed character (`\n`). When data is pasted into CyberChef that contains a different EOL separator, its original value will be preserved. It will show up as a control character picture, as explained below.

<br clear="both"/>

---


### Non-printable character rendering

<img height="200" alt="Screenshot 2022-09-02 at 14 56 50" src="https://user-images.githubusercontent.com/22770796/226686709-0fa78064-9f40-4196-94f9-327023d54ae7.png" align="right">

Any characters not in the printable range are now rendered as control character pictures in red. This is important for data integrity as it is much clearer which characters are present. It also makes it easier to copy and paste binary data, for example that which has been compressed or encrypted, or the contents of binary files.

<br clear="both"/>
<br />

<img height="200" alt="Screenshot 2022-09-02 at 15 02 24" src="https://user-images.githubusercontent.com/22770796/226686868-4bcce2b7-2ed3-442a-a59d-1662a13b311c.png" align="right">
<img height="200" alt="Screenshot 2022-09-02 at 15 00 45" src="https://user-images.githubusercontent.com/22770796/226686798-d41b0067-f9ad-4181-b8e5-f10de6876a5f.png" align="right">

A good example of where this is helpful is if you copy multiple lines from a Windows application like Notepad or Microsoft Word into CyberChef. If your EOL separator is set to `LF` (`\n`), you will see Carriage Return control codes at the end of each line when the data is pasted in. This is because the EOL separator in Windows applications is `CR+LF` (`\r\n`) and CyberChef is set to render only `LF` characters as EOL separators, leaving the `CR` character surplus. In a tool like CyberChef it is important that we don't automatically convert these characters as we may require them to get an accurate hash of the data, decrypt it correctly, or convert it to a different format. Setting the EOL separator to `CRLF` will hide these surplus characters as both the `CR` and `LF` are used to denote the end of the line together.

<br clear="both"/>

---


### File loading

<img height="250" alt="Screenshot 2022-09-02 at 15 25 44" src="https://user-images.githubusercontent.com/22770796/226687325-0f11e3c4-0d28-4afd-a91f-916acfb0cd16.png" align="right">

Due to the changes in how non-printable characters are rendered, loaded files can now be edited in the Input as their bytes can be represented accurately. A side panel will be shown listing the file metadata along with a thumbnail preview if the file is an image (this thumbnail can be turned off in the Options pane).

<br clear="both"/>

---

### Editor features

<img height="250" alt="Screenshot 2022-09-02 at 14 43 52" src="https://user-images.githubusercontent.com/22770796/226686506-edb7da4e-1a35-44a6-a247-70ac6ba3845a.png" align="right">

Various modern code editor features have been added to CyberChef's Input and Output, thanks to the use of the excellent [CodeMirror6 editor](https://codemirror.net/). These include rectangular selections and multiple selections, allowing you to copy, delete, or edit multiple parts of the Input at once.


<img width="88" alt="Screenshot 2022-09-02 at 15 16 40" src="https://user-images.githubusercontent.com/22770796/226687280-a570408d-1ca2-40a2-aaf8-824f988ad5ec.png">
<img width="90" alt="Screenshot 2022-09-02 at 15 18 19" src="https://user-images.githubusercontent.com/22770796/226687290-0833be2a-f855-489b-bdf4-ab227ee6ff92.png">
<img width="97" alt="Screenshot 2022-09-02 at 15 15 27" src="https://user-images.githubusercontent.com/22770796/226687234-69f203f5-f5b0-47d3-b899-1cb931fc7cf6.png">

<br clear="both"/>
<br />

Bracket matching has also been added, along with selection matching which highlights other instances of the same word or phrase elsewhere in the Input or Output. If the operations in the current Recipe support it, the corresponding selections will be shown in the Output or Input respectively.

<p align="center">
<img height="300" alt="Screenshot 2022-09-02 at 14 43 52" src="https://user-images.githubusercontent.com/22770796/226693131-125dcded-4c1e-465c-83a4-9d913245466a.png">
</p>



<br clear="both"/>

---

### Contextual help

<img height="250" alt="Screenshot 2023-03-21 at 17 36 10" src="https://user-images.githubusercontent.com/22770796/226694551-0fce18a0-8916-4ccc-afaa-aa5023f6dddb.png" align="right">

Taking a leaf out of [Ghidra's](https://github.com/NationalSecurityAgency/ghidra) book, I have implemented a contextual help feature. This is accessed by pressing `F1` while hovering the mouse over a feature that you want to learn more about.

<br clear="both"/>
<br />

I had always intended CyberChef to be as self-explanatory as possible, requiring no training to understand the tool itself or how to use it. That is not to say that training is not required to learn how to get the most out of it, or to understand how some of the Operations work, but the tool itself and the methods by which you use it should be fairly obvious. This release has the potential to break that paradigm due to the introduction of more complex features with the occasional nuance as explained above. Contextual help has been added to explain some of these nuances, but I am open to suggestions for improvements. I think that the benefits they provide are worth the added complexity. As the [Zen of Python](https://peps.python.org/pep-0020/) says, "Simple is better than complex. Complex is better than complicated."

- n1474335, 21st March 2023