# mscoreLaTeX

This is a proof of concept package for an integration of MuseScore and LaTeX. Specifically including scores or notation (e.g. musical examples for education, examples academic research papers, critical editions with texts etc) to LaTeX documents directly from the MuseScore file (`.mscx`, `.mscz`), MusicXML (`.musicxml`, `.mxl`), or midi file (`.mid`, `.midi`) and probably more.

The purpose of the package is to make it easier and faster to edit notation and displaying it in your document, by just saving your changes in MuseScore and compiling your LaTeX code. You can watch a demo in the video below.

![demo](https://user-images.githubusercontent.com/8700107/113226642-580f9580-9299-11eb-8b41-62b60d6963e3.gif)

https://user-images.githubusercontent.com/8700107/113226188-36fa7500-9298-11eb-82fc-088076c804cb.mp4

You can run the example above, if you have LaTeX and the programmes listed installed:

- the MuseScore command line (`mscore`) which is installed with the MuseScore gui app
- `xelatex` (i had some problems with pdftex but they seem solvable)
- `pdfcrop`, which comes with the complete installation of LaTeX (e.g. mikTeX or macTeX) otherwise can be downloaded from CTAN

To compile the document you need run `xelatex --shell-escape main.tex`. The `--shell-escape` option is needed because the macro `\printscore{filename}` needs to execute the `mscore -o` and `pdfcrop` cli commands.

## how it works

The package `mscoreTeX.sty` provides the macro `\printscore{filename}`. 

```LaTeX
\newcommand{\printscore}[1]{
    \immediate\write18{mscore -o #1.pdf #1.msc*}
    \immediate\write18{pdfcrop #1.pdf #1.pdf}
    \includegraphics{#1.pdf}
}
```

Tha macro then runs two cli commands.

```bash
mscore -o #1.pdf #1.msc*
```

that converts the MuseScore file to pdf, just like it would do if you exported from the gui app

```bash
pdfcrop #1.pdf #1.pdf
```
that crops the white space of the pdf, to make it ready for inclusion as a graphic.

After tha `\includegraphics{...}` is used, from the `graphicx.sty` package to include the pdf.

In my example I put the notation inside the figure environment, but because they are just graphics they are really versatile.

## ideas for improvment

### file handling

The way the package currently handles files and filenames is not good at all, but it works in a controlled environment.

### notation formatting

The formatting of the notation to be included should somehow be aware of the current document format. For example:
 
- use the same text font, and font size
- notation size should depend on font size
- same margins etc.

This can be done by editing the MusicXML, or MuseScore files properties before converting to pdf, or even better to have a specific style file for MuseScore, that gets called with the option `mscore -S`, so the original files are not changed.

Also to be a respectable package the above should be package options, or have a setup macro, to configure important formatting options like the notation font to be used.

### speed

Right now as it stands, the speed is ok for a handful of MuseScore files, but the `mscore -o` command actually opens an instance of MuseScore without the gui, and would be really slow at processing a lot of files independently. That may be solved with the batch process command that the `mscore` cli provides, but the way to implement it is leagues above me.

### contributions

Both the MuseScore and the LaTeX communities could be really helpful in making this a really useful package for musicologist, music historians and musicians, as i alone am not well versed to make a robust LaTeX package, or to efficiently manipulate the XML files of MuseScore.