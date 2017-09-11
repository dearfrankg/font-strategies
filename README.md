


# A COMPREHENSIVE GUIDE TO FONT LOADING STRATEGIES


## THE QUICK ANSWER


- `best well rounded approach`: FOUT with a class

- `easiest to implement`: don't use web fonts

- `best performance`: one of the Critical FOFT approaches

- `large number of web fonts`: FOFT, or FOUT with Two Stage Render approach

- `works with cloud/font hosting solution`: FOFT approaches generally require self hosting, so stick with the tried and true FOUT with a Class approach.




## TERMS

- `FOFT`: flash of faux text

- `FOIT`: flash of invisible text

- `FOUT`: flash of unstyled text

- `@font-face Descriptors`: key-value pairs inside of the font-face block

- `Font Matching Algorithm`: How the browser matches a font-family CSS property on an element to an appropriate @font-face block.

- `Font Synthesis`: known as Faux-bold or Faux-italic rendering - few browsers support the font-synthesis CSS property to control this.

- `System Fonts`: Fonts that are available locally on an operating system without making any network requests.

- `Roman font`: Traditionally lowercased, roman is used to mean non-italic but in my experience, it’s usually extended to non-bold as well. The “regular” font.

- `Subsetting`: Modifying the font file to include only a small portion, typically used to optimize a font file for a single language on the web








## Unceremonious @font-face

Throw a naked @font-face block on your page and hope for the best. This is the default approach recommended by Google Fonts.

RECOMMENDATION: do not use - bad rendering performance


## font-display

Add a new font-display: swap descriptor to your @font-face block to opt-in to FOUT on browsers that support it. Optionally, font-display: fallback or font-display: optional can be used if you consider web fonts to be unnecessary to the design.

RECOMMENDATION: Definitely add it to your @font-face blocks, but by itself it’s not sufficient.


## preload

Add <link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin> to fetch your font sooner. Pairs nicely with an unceremonious @font-face block and feel free to also throw in the font-display descriptor as well for bonus points.

Keep in mind: The pros and cons for this approach are heavily dependent on the font loading strategy you pair it with, whether it be Unceremonious @font-face or font-display.

RECOMMENDATION: Not sufficient by itself.


## Don’t use web fonts

I won’t go into this approach too much because, well, it isn’t technically a font loading strategy. But I will say that it’s better than using web fonts incorrectly.

RECOMMENDATION: Sure, I guess, but I wouldn’t be excited about it.


## Inline Data URI

There are typically two kinds of inlining covered by this method: in a blocking <link rel="stylesheet"> request or in a &lt;style> element in the server rendered markup. Both alibaba.com (two web fonts embedded in a blocking CSS request) and medium.com (seven web fonts) use this approach.

RECOMMENDATION: Alibaba and Medium use this approach but the author does not recommend.


PROS

- Seemingly great rendering performance: this approach has no FOUT or FOIT. This is a big deal.

- Flexibility: Don’t need to worry about grouping repaints/reflows—this approach has no FOUT or FOIT.

- Robustness: inlining puts all your eggs into your initial server request basket.

CONS

- A catch with rendering performance: while this approach doesn’t FOUT, it can significantly delay initial render time. On the other hand it will render “finished.” But keep in mind that even a single WOFF2 web font is probably going to be around 10KB—15KB and inlining just one as a Data URI will likely take you over the HTTP/1 recommendation of only having 14KB or less in the critical rendering path.

- Browser support: Doesn’t take advantage of the comma separated format list in @font-face blocks: this approach only embeds one format type. Usually in the wild this has meant WOFF, so using this method forces you to choose between ubiquity (WOFF) and much narrower user agent support but smaller file sizes (WOFF2).

- Bad scalability: Requests don’t happen in parallel. They load serially.

- Self hosting: Required, of course.


## Asynchronous Data URI

Use a tool like loadCSS to fetch a stylesheet with all of the fonts embedded as Data URIs. Often you’ll see this coupled with a localStorage method of storing the stylesheet on the user agent for repeat views.

RECOMMENDATION: It’s OK but we can do better.


## FOUT with a Class

Use the CSS Font Loading API with a polyfill to detect when a specific font has loaded and only apply that web font in your CSS after it has loaded successfully. Usually this means toggling a class on your <html> element. Use with SASS or LESS mixins for easier maintenance.

RECOMMENDATION: THIS IS THE BASELINE STANDARD. THIS WILL WORK FOR MOST USE CASES.


## FOFT, or FOUT with Two Stage Render

This approach builds on the FOUT with a Class method and is useful when you’re loading multiple weights or styles of the same typeface, e.g. roman, bold, italic, bold italic, book, heavy, and others. We split those web fonts into two stages: the roman first, which will then also immediately render faux-bold and faux-italic content (using font synthesis) while the real web fonts for heavier weights and alternative styles are loading.

RECOMMENDATION: GREAT FOR THOSE INTERESTED IN EXTRA PERFORMANCE BUT CAN’T SUBSET WITH CRITICAL FOFT.


## Critical FOFT

The only difference between this method and standard FOFT approach is that instead of the full roman web font in the first stage, we use a subset roman web font (usually only containing A-Z and optionally 0-9 and/or punctuation). The full roman web font is instead loaded in the second stage with the other weights and styles.

RECOMMENDATION: USE ONE OF THE IMPROVED CRITICAL FOFT VARIATIONS BELOW.


## Critical FOFT with Data URI

This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we simply embed the web font as a inline Data URI directly in the markup. As previously discussed, this will block initial render but since we’re only embedding a very small subset roman web font this is a small price to pay to mostly eliminate FOUT.

RECOMMENDATION: THIS IS THE CURRENT GOLD STANDARD, IN MY OPINION.


## Critical FOFT with preload

This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we use the new preload web standard as described above in the preload method. This should trigger the download sooner than previously possible.

RECOMMENDATION: THIS WILL BE THE NEW GOLD STANDARD WHEN BROWSER SUPPORT IMPROVES.



## REFERENCE

    - https://www.zachleat.com/web/comprehensive-webfonts/
