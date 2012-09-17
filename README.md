mathematica-blog
================

Little rails web app to streamline blog publishing from a Mathematica notebook.

The author has a [Mathematica](http://www.wolfram.com/mathematica/) Home User license,
and although one can easily write formulas, create plots and even interactive 
interfaces, the process of translating that into a web app is not straight forward.

Introduction
------------

Mathematica can export a notebook into a web page, but one needs a little bit of
software to take that and turn it into a blog post. That's what the author did. In addition,
Wolfram recently created what they call the _computable document format_, which is
something you can generate from a notebook and put it in the web for downloading
or even play inside the browser via the free _CDF player_. Well, if one wants to publish
a notebook as a blog post and include CDFs defined within the document easily, this
piece of software may help.

It is very basic! I am not a Mathematica expert, so didn't do it in Mathematica. And not
a Rails expert either, so it is kind of basic. However, it works and contributions
and ideas are welcome.

Mathematica how-to part
-----------------------

The idea is: you write your blog as a notebook, include formulas and graphics. I found that
using the default behaviour in Mathematica, by which a formula's result is added
as a new _cell_ with a label, leads to more work. Instead, a better approach is
to use CellPrint which programmatically does the same, but gives you more control over the
resulting output. So, in Mathematica you write your graphics as:

```Mathematica
Module[{ ... },
  exp = Plot[ ... ]; (* or any other graphics output *)
  CellPrint[ExpressionCell[exp, "Output", CellLabel -> None]];
  ];
``` 

to have a _generated cell_ without the usual *Out[.]=* label. And with a slight modification
the software will manipulate the output to streamline CDF rendering! Just produce output
like

```Mathematica
Module[{ ... },
  exp = Manipulate[ ... ]; (* interactive Mathematica command *)
  CellPrint[ExpressionCell[exp, "Output", CellTags -> "example.cdf",
    ShowCellLabel -> False]];
  ];
``` 

and the web blog software will make sure a CDF gets inserted in place.

After this, one needs to do few things in the notebook:

1. Uncheck *Cell* > *Cell Properties* > *Open* over the input formulas, so they won't appear in
   your exported web page.

2. Select the output cells that have *Manipulate* output (all should have CellTags -> *a_name.cdf*)
   and use the *File* > *Deploy* > *Standalone*
   menu to turn the cell contents into a CDF. Yes, name the CDF after the label you use to generate
   the cell as the code above tells. Place the CDF in a folder (more on this below.)

3. Save the document now !

4. And then use *File* > *Save as* to export the document in web page format. Place the .html in
   same folder as the CDFs. *Don't save this version!* I don't know why, but Mathematica freezes
   the Manipulate output after rendering the PDF; better if you reload the document saved in 3.

Next is Rails app time!

Rails how-to part
-----------------

This web application works as follows: it is configured with a particular 'posts' folder. It expects the
following tree structure:

```
posts/2012/001/my-post.html
              /HtmlFiles/*  << generated by Mathematica
              /my-post.html.yml << written by you
              /example.cdf  << saved manually by you
              ...
          /002/...
      ...
```

Yes, it forces you to have certain structure and is a little bit opinionated in this respect. As
one sees, there is a .yml file in there too. This follows Jekyll and Octopress idea of YAML
front, but it lives in another file. This YAML file contains a little description of your post for the web app. The software comes
with a sample 'About' page, which is also a blog entry, so have a look.

Then one uploads (rsynch for example) the local *posts* folder to the server's *posts* folder.
This will change the modification time of the .html file, which will trigger, upon display, a refresh (pre-process)
of the HTML generated by Mathematica for your blog.

To publish a new entry, one needs to tell the software that a 'year' folder needs to be re-scanned.
Use the /year/2012 (for example) URL to trigger that (I am in the process of putting a simple u/p for this
admin function.)

By the way, I want to thank [Diego Alonso](http://www.diegoalonso.net/) for teaching me Rails and
kicking my ss when I said that I was going to do a blogging tool until I got it ready. Although
I am pretty sure the app is not yet to the quality standards he taught me...

Quick note
----------

The app is not yet fully portable. But in production it needs a little configuration file in
/etc/mathematica-blog/config.yml. There is a sample of such file in the source (see etc folder).

To do
-----

Too much to do! I just wanted to put it out there to get into blogging... anyway, if I manage to have
time or get help from someone, I think this should be done...

  * The publishing part in Mathematica could be even better if one installs a custom *Palette*
  * The Rails app needs more work
  ** More testing harness
  ** Better configuration
  ** Search built-in
  * Overall, better documentation

