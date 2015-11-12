# Open SoC Debug

The slide decks are based on [Reveal.js][]. For creating the online
presentations, we derived from [dploeger/jekyll-revealjs][] and create
slides using [Markdown][].

## Slide basics

For each slide deck there is a folder that contains a file
`slides.md`. This is the one where you create slides and separate them
by a newline, three dashes and another newline. [Reveal.js][] gives a
very impressive demo about what can be achieved with it.

Beside this there is an `index.html` with jekyll syntax to include the
slides. The reason we use separated files for this is to be flexible
for other used of the slides, such as directly showing with
[reveal-md][].

## Previewing locally

You can preview the slides using [reveal-md] or by running [jekyll][]
locally:

```
jekyll serve -w --baseurl="/"
```

## But I need PDFs!

Printing from the browser is supposed to produce proper PDFs, but I
cannot confirm this with Firefox so far.

But what works is using [reveal-md][]:

```
reveal-md slides.md --print slides.pdf
```

[Reveal.js]: http://lab.hakim.se/reveal-js/#/
[Jekyll]: http://jekyllrb.com/
[dploeger/jekyll-revealjs]: https://github.com/dploeger/jekyll-revealjs
[Markdown]: http://daringfireball.net/projects/markdown/
[reveal-md]: https://github.com/webpro/reveal-md

