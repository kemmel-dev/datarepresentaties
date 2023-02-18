# Tekstcodering

1. Bestudeer onderstaande uitleg over tekstcodering,
2. hou goed bij wat je opvalt en wat je leert,
3. en beantwoord de vragen die in de tekst staan.

## Plain text

Files containing plain text are nothing special. As you've seen previously, even text is eventually stored in binary format, meaning that it is written in memory or on disk using 0's and 1's. In that sense, it's nothing different from formats like MP3 to store music or docx to store text and layout.

However, plain text has a distinct place in computing because of the sheer number of applications that use plain text, and because plain text files can easily be inspected by humans (in most cases).
Plain text can be shown using just about any computer without using specialized programs, as you would need to show, for example, a 3D model of a human organ.

Even plain text comes with some problems. It would have been great if every word in any language could be expressed using the 256 different characters of extended ASCII. And yes, it has been [tried](https://en.wikipedia.org/wiki/Transliteration_of_Chinese) to make other languages "compatible" with English. But in reality, ASCII is not flexible enough.

In addition, there are some problems with the interpretation of special characters and language concepts. We'll discuss the most important ones here.

It's even possible to represent structured data using plain text files, and we'll learn more about that in the next module.

## Unicode

In a previous module you've read about Unicode. Unicode is a more modern invention and is much more ambitious than ASCII: it aims to represent as many human scripts as possible (currently 159), as well as various kinds of symbols. Currently, there are around 100.000 characters in Unicode, and the number is increasing every year. Allowing that many characters is done, in essence, by increasing the number of bits available to represent one character ("code point" in Unicode).

In fact, in the book it is said that Unicode always uses 16 bits per code point. That's not quite the case. Unicode text are nowadays most often saved using UTF-8 encoding. Here, every character does not take the **same** number of bits. UTF-8 assigns characters 1 **or more** bytes. Any character encoded in 2 or more bytes is called a "multi-byte" character. So how does that work? Take a look at the following:

![embed](https://www.youtube.com/embed/MijmeoH9LT4)

So the nice thing is that the first 128 characters of Unicode are encoded exactly as they are in ASCII. The very first bit in a byte is always 0 in those cases. The remaining 7 bits are used to encode the specific character.

In contrast, characters starting with a 1 will be interpreted differently in Unicode UTF-8 than there are in ASCII. This means that every ASCII text (but not extended ASCII) is valid UTF-8. In other words, many files can be read assuming that they are interpretable as UTF-8.

**Question 1.** Why is not every text encoded in "extended ASCII" also valid Unicode?

However, every UTF-8 file is not necessarily valid ASCII. This becomes a problem in older text editors that do not expect UTF-8 text. It also becomes a problem in tools to process text written using for example Python version 2.x.

**Question 2.** What will happen if you load a UTF-8 file in a text editor that only supports (extended) ASCII?

Because of this, encodings can be a bit of a hassle. It's good to be aware of this. In this course, we will notify you in case encodings may become a problem.

## Line breaks

ASCII and Unicode also contains various kinds of control codes, or "unprintable characters". A few of those are used to represent the "end of" something. The most important ones are the characters that signal the **end of a line**.

There are two characters that may be used to end a line:

| Code | Hex | Meaning    | Acronym | Escape |
| ---- | --- | ---------- | ------- | ------ |
| 10   | 0a  | line feed  | LF      | \n     |
| 13   | 0d  | carriage return | CR | \r     |

Historically, [these two characters do not mean the same thing at all](https://youtu.be/C4Z0ZdYHW3o). ([Also.](https://youtu.be/JY3QfJ6KHq0)). But since printers got more automated (and we stopped using printers anyway) the difference has become less important.

Now in software, the thing we do most often is storing text files on disk and retrieving them again to show or edit. So what is needed there is some character that signifies the end of a line. Based on the availability of both LF and CR, different software makers did different things and we ended up with a mess!

At this point it is somewhat common to find text files using the following line endings:

| Characters | Operating System |
| ---------- | ---------------- |
| CR         | Old Mac OS files |
| LF         | Most UNIX files + Mac OS X |
| CRLF       | Windows files    |

Yes, on Windows they actually use two characters to indicate line endings. It's most consistent with how a type writer works, but maybe a bit old fashioned. But that's the way it works.

The point is here, you *could* encounter some of these combinations when processing data. It could also very well be that you encounter just one of the above. However, it is up to you to be certain about this by talking to whoever delivered you the data, or using `xxd` to see what's going on.

Let's create a file by typing some text into the terminal, redirecting to a new file (ending by pressing CTRL-D on an otherwise empty line):

    % cat > bla
    hoe
    dan
    ^D

Here's what `xxd` shows for a text made on modern macOS using the method above:

    % xxd -g1 bla 
    00000000: 68 6f 65 0a 64 61 6e 0a                          hoe.dan.

`xxd` shows the hexadecimal representation of each byte in the file. For example, hex 68 is the character `h` in ASCII. And so on.

🌵 **Question 3.** Write down everything you can figure out about the line endings in the above `xxd` results.

## White space

Another feature of ASCII and Unicode is white space. ASCII supports some characters that are shown as space on the screen and which are called the "white space characters". The most obvious example is the "space" character itself (ASCII 32) that is used to separate words.

A little bit more complex is the "tab" character (ASCII 9) that is used to separate records, but it's also used to format tables on screen. Unicode supports special versions of spaces, e.g. the ["thin space"](https://www.compart.com/en/unicode/U+2009) that takes up less room than a regular space.

The problem is: what is whitespace? This is not necessarily well-defined. Say we have the following text file:

    two
    words

There is no "space" character between "two" and "words", there's only a newline there (and/or a carriage return, as we discussed earlier). But obviously we see these as separate words when we read them. So what if you're making a tool that **counts the number of words in a file**? Which codes are seen as white space, and in turn decide how many words there are?

🌵 Your task is to investigate the inner workings of the tool called `wc` that is designed to count words in files.

**Question 4.** What is a "word" according to the `wc` tool? Which characters are white space? Use `man wc` to start your investigation. You're going to need to read other man pages as well, as referred to by the `wc` man page. Another way to find out what "words" are according to `wc` is to make all kinds of test files and check out how `wc` deals with those. Make sure to explain in detail how you found your answer.

**Question 5.** Which are the options to use with `wc` to show the number of **characters** vs. the number of **bytes**? Can you create a file using TextEdit or Notepad, save as UTF-8 and check whether wc provides different values for both counts? Provide details of what you did and what you found.

Your take home message: everything in computers is artificial, someone just made a decision and hopefully the rest of the world follows that decision (if they do, it's called a **convention**). So what counts as whitespace is just something that was defined somewhere, and some people do it differently, so be sure to check what the definitions are!

## Inleveren

Lever hieronder een PDF in met je uitwerkingen. Gebruik géén titelpagina. Vermeld je naam en studentnummer, en de naam van de opdracht. Vermeld ook de vraag boven elk antwoord---dit mag een samengevatte versie van de vraag zijn. Als je informatie van buiten de aangeleverde tekst gebruikt moet je een bronvermelding doen; je mag gewoon de site/titel noemen in de tekst.

## Nakijken

De antwoorden worden kritisch nagekeken op zorgvuldige beantwoording. Dat betekent dat de antwoorden gebaseerd moeten zijn op de lesstof, dat wat er staat goed is en logisch onderbouwd en dat er geen onware of irrelevante beweringen of informatie bij de antwoorden staan. Aan de andere kant: een klein foutje is geen probleem.
