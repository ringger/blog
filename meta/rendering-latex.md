## Rendering Latex on dataskeptic.com

In case you don't already know, Latex is a markup language that allows people to create beautifully typset documents using mathematical and scientific symbols and notiation.  It's an absolute necessity in technical writing.  There's a small learning curve, but once you get used to Latex, it's pretty easy to write.  Since back in grad school, I was used to writing Latex documents and using the available tools to render them to PDFs.

And then came the explosion of the web.  Luckily (or so I thought at the time), someone built a library called [MathJax](https://www.mathjax.org/).  For what it intends to be, MathJax is great.  You simply add a script to your html file, and surround your Latex code in the typical way, and all the work is done for you.

However, I was finding it nearly impossible to get MathJax to work with my new site design here at dataskeptic.com.  Its important to me that I be able to robustly blog in any format of my choosing.  Presently, I can work in R, Jupyter notebooks, or markdown files.  I have a continuous deployment process set up such that whenever I commit to a branch in my blog github repository, it automatically identifies the content that is new or has changed, picks the appropriate renderer (knitr, nbconvert, or the Python markdown library), and converts the source file to an rendered html file.

In each of these rendering processes, the Latex is a pass through.  It shows up un-rendered in the resulting file which I push to S3.  When someone views my site from their browser, the site instructs the browser to get the file from S3, and then MathJax kicks in to render the formulas.  Except sometimes it didn't.  Additionally, this process was slow, which is really bad for SEO.  The slow loading process would also make the layout of the page jitter around as different elements arrive, which cause a terrible user experience.

Something needed to change.

I finally decided to move the rendering server side.  The hackish way to do this would be to render the Latex to images and insert them, but I didn't want to do that.  I want the site to responsively scale to any resolution and screen, and that means I needed to use SVGs.

I added some new code to that rendering process I described above.  After the HTML has been generated by one of my three sources, I then scan that code for any Latex markup.  I extract each element, render it to an SVG, push the SVG to S3, and insert an image in it's place in the original source.

So far I'm quite happy with this approach.  Page times are quick and the result looks pretty good.  If you're looking to do something similar, you can find all the code my [blog deploy script](https://github.com/data-skeptic/blog/tree/master/deploy) on github.  It's not a robust library - this code is highly tuned for my needs, but I plan to generalize it bit by bit and perhaps eventually open source it into a formal project.  But, if you're looking for something to model your own efforts after, you can find inspiration there.