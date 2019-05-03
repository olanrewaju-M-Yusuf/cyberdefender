CyberChef v8 introduces the ['Magic'](https://gchq.github.io/CyberChef/#recipe=Magic()) operation, designed to automatically detect how your data is encoded and which operations can be used to decode it. A number of methods are used to achieve this.

### Pattern matching

Many common data encoding schemes, such as Base64, Hexadecimal and Gzip, have predictable structures that can be detected using pattern matching techniques. Regular expressions have been written for all operations where this is the case. They are each run over the data and any matches are recorded. In some cases, multiple regular expressions are written for the same operation where different arguments can be applied, for example Base64 using a non-standard alphabet.

Example regular expression for Base64 data using the standard alphabet:
```regex
/^(?:[A-Z\\d+/]{4})+(?:[A-Z\\d+/]{2}==|[A-Z\\d+/]{3}=)?$/i
```

Example regular expression for Base64 data using the y64 alphabet:
```regex
/^(?:[A-Z\\d._]{4}){5,}(?:[A-Z\\d._]{2}--|[A-Z\\d._]{3}-)?$/i
```

### Speculative execution

For every pattern that matches, the corresponding operation is speculatively executed to determine what the output looks like. Various metrics are collected for each of these possible branches to determine whether they look like valid data or not. Each branch is also checked for further pattern matches, meaning that data under multiple levels of encoding can be unwrapped recursively. The maximum number of levels of recursion is controlled by the 'Depth' argument.

The methods used to detect how "valid" the data looks are as follows, ranging from simple to more complex techniques:

#### Magic byte detection

In many file formats, a [magic number](https://en.wikipedia.org/wiki/List_of_file_signatures) is included to allow trivial detection of the file type. If a magic byte sequence is found in a branch, it increases the likelihood that the correct decoding sequence has been found.

![Gzip detection](https://user-images.githubusercontent.com/22770796/43699009-b52eae5a-9944-11e8-9c60-a648be5e1788.png)

#### UTF-8 detection

UTF-8 data has a well-defined structure which can be easily tested for. The presence of valid UTF-8 data may suggest that a valid decoding sequence has been found.

#### Entropy measurement

[Shannon Entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory)), in the context of information theory, is a measure of the rate at which information is produced by a source of data. It can be used, in a broad sense, to detect whether data is likely to be structured or unstructured. If a branch results in data with high entropy, it is possible that it has simply output unstructured, random garbage. Branches resulting in lower entropy data are ranked higher as they are more likely to contain repeating structures.

#### Byte frequency analysis

On average, the English language contains more "e"s than any other letter. In fact, given a long enough sample, the relative frequency of each character is very predictable, to the extent that we can consider any text not roughly matching these frequencies as unlikely to be English.

![English letter frequencies](https://user-images.githubusercontent.com/22770796/43697173-2253822c-993a-11e8-9ced-b567b5eea61a.png)

This set of frequencies can be expanded to include all possible bytes, incorporating punctuation, numbers, symbols and other formatting characters. To generate a set of accurate "truth data", the [English language Wikipedia dump](https://dumps.wikimedia.org/enwiki/) was downloaded, wiki syntax was stripped out, then the byte frequencies were calculated. The resulting values assume a character encoding of UTF-8.

For every branch created by the Magic operation, the byte frequencies for the output are calculated and then compared to this truth data using [Pearson's chi-squared goodness of fit test](https://en.wikipedia.org/wiki/Pearson%27s_chi-squared_test). This process tells us how closely the branch's output matches the English language and therefore hopefully gives us an idea of how likely it is that we have found correctly decoded data, if that data includes a reasonably high proportion of English text.

Truth data was also generated for all other languages supported by Wikipedia. By default, only the top 38 languages are checked (based on the most popular languages used on the Internet, as listed on [W3 Techs](https://w3techs.com/technologies/overview/content_language/all)), however if 'Extensive language support' is selected, all 245 languages are supported.

![Georgian language detection](https://user-images.githubusercontent.com/22770796/43700059-24afe498-9949-11e8-8ce4-ce4c79863f49.png)

### Intensive mode

The above methods have been optimised to run reasonably quickly over most types of input, however there are some methods which take considerably longer to run due to their high branching factor. These can be turned on by enabling the 'Intensive mode' argument.

#### Character encoding brute forcing

For each branch, the data is converted into a number of different character encodings. If this conversion results in different data, a new branch is created and metrics are calculated as described above. This can help to detect the correct character encodings for [mojibake](https://en.wikipedia.org/wiki/Mojibake) (garbled data represented in the wrong encoding). Over 40 character encodings are currently supported.

![Mojibake detection](https://user-images.githubusercontent.com/22770796/43699979-e84e070a-9948-11e8-82fd-3bbaf98be75b.png)

#### Arithmetic logic brute forcing

Single byte XORs are carried out over every branch, creating 255 further branches to analyse. Bit rotates are also calculated, resulting in another 7 branches.

![Single byte XOR detection](https://user-images.githubusercontent.com/22770796/43699531-0f2fb3de-9947-11e8-90be-16778a29973c.png)

### Automated background magic

As well as being available as a standalone operation, CyberChef runs the 'Magic' operation automatically in a background thread whenever the Output is changed. If it manages to find an operation or set of operations that can help decode the data, the magic icon will be displayed in the Output pane. Hovering over this icon shows which operations are most likely to help and a snippet of what they will produce. Clicking the icon will append those operations to your recipe.

![Automated magic](https://user-images.githubusercontent.com/22770796/43699791-19b77f2a-9948-11e8-87d6-8d822d528615.png)
