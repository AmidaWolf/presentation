# Fonts Loading Strategy  
 
1. Talk about problems for fonts loading.  
  Webfonts are one of the very first things we add to our sites—whether it be for individuality or stylistic reasons. Unfortunately, they are also infamous for making our sites painfully slow and frustrating to read. The end result? Frustrated users and lots of lost business opportunities. Thankfully they’ve been given lots of attention the last couple years. So let’s take a look at the three best ways for you to load your webfonts, but first let’s get everyone on the same page regarding everything that can and will go wrong with webfonts.  
  With no effective font loading strategy, users will experiment what’s call FOIT (Flash of Invisible Text) as the font files are downloading.  
  Instead it’s preferable to go for FOUT (Flash of Unstyled Text), users will see content sooner with a font from the system and switch to the web font later.  
  FOFT: "Flash of Faux Text". The idea here is only waiting for the Roman variant of the custom font to load, and setting text in that right away. You force the browser to create "faux bold" and "faux italic" where needed, which are replaced by the real versions as soon as those are downloaded. 
    - FOIT, FOUT, FOFT.  
  Let’s start with the two most well known issues: FOIT (Flash of Invisible Text) and FOUT (Flash Of Unstyled Text). I’m going to assume you all know how and why these issues occur with webfonts, but in case you don’t, here’s a very brief explanation: When the browser detects your usage of a custom font, it waits for your webfont to load before rendering your text (i.e., FOIT). If after ~3 seconds it has not loaded yet, it renders the text with your fallback font (usually a system font like Arial). Afterwards when the webfont is loaded, the fallback font is replaced with your webfont (i.e., FOUT). Why is this a problem? Because your webfront may have added over 2 seconds to your page’s load time, and may have lost up to 50% of your visitors.  
  Now for the last issue, which people don’t think about very often: Content Shifting. Ever been reading an article and 5-10 seconds into reading, the fonts change and everything on the page scrolls up?
2. Talk about fonts loaing strategies.   
Now that we know the font loading problems, let’s look at the font loading methods you can use to avoid these problems completely.
    - Simple methods. Talk about `font-display`.  
    The new font-display descriptor for @font-face lets developers decide how their web fonts will render (or fallback), depending on how long it takes for them to load.    
    The font display timeline is based on a timer that begins the moment the user agent attempts to use a given downloaded font face. The timeline is divided into the three periods below which dictate the rendering behavior of any elements using the font face.  
    Font block period: If the font face is not loaded, any element attempting to use it must render an invisible fallback font face. If the font face successfully loads during this period, it is used normally.  
    Font swap period: If the font face is not loaded, any element attempting to use it must render a fallback font face. If the font face successfully loads during this period, it is used normally.  
    Font failure period: If the font face is not loaded, the user agent treats it as a failed load causing normal font fallback.   
    swap - gives the font face an extremely small block period and an infinite swap period.  
    optional - gives the font face an extremely small block period and no swap period. 
    At time of writing, this feature is available in the latest versions Edge, Firefox, Chrome, Safari and Opera, but is not available at mobile browsers.  
    For more information look at https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display and https://developers.google.com/web/updates/2016/02/font-display  
    Simple `@font-face`. Throw a naked @font-face block on your page and hope for the best. This is the default approach recommended by Google Fonts. Just add a CSS @font-face block with WOFF and WOFF2 formats or another formats if you need.
      - `font-dispay: swap`:
      Add a new font-display: swap descriptor to your @font-face block to opt-in to FOUT on browsers that support it. Optionally, font-display: fallback or font-display: optional can be used if you consider web fonts to be unnecessary to the design.
      - preload:  
      Just add `<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>` to fetch your font sooner.  
      - `font-dispay: swap` with preload. Pairs nicely with an previous simple `@font-face` block and feel free to also throw in the previous `font-display` descriptor as well for looking good. Just combine the two previous points, and be happy.
    - FOUT with a Class:  
    Use the CSS Font Loading API with a polyfill to detect when a specific font has loaded and only apply that web font in your CSS after it has loaded successfully. Usually this means toggling a class on your <html> element.   
    - FOUT:  
    Similar to the above, but without using a class—using only the CSS Font Loading API. This doesn’t require any modification of the CSS, injects the web fonts using JS programmatically.  
    - FOFT:  
    This approach builds on the FOUT with a Class method and is useful when you’re loading multiple weights or styles of the same typeface, e.g. roman, bold, italic, bold italic, book, heavy, and others. We split those web fonts into two stages: the roman first, which will then also immediately render faux-bold and faux-italic content (using font synthesis) while the real web fonts for heavier weights and alternative styles are loading. 
    - Critical FOFT:  
        The only difference between this method and standard FOFT approach is that instead of the full roman web font in the first stage, we use a subset roman web font (usually only containing A-Z and optionally 0-9 and/or punctuation). The full roman web font is instead loaded in the second stage with the other weights and styles. 
    - Critical FOFT with Data URI:  
    This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we simply embed the web font as a inline Data URI directly in the markup.  
    Just change for `font-family: LatoSubset;` url base-64 encoding font like this `src: url('font-lato/lato-regular-webfont.woff2') format('woff2')` into 
    `src: url("data:application/x-font-woff;charset=utf-8;base64,d09GRgABAAAAABVwAA0AAAAAG+Q...`, on another like Critical FOFT. This might sound like a lot of data to store but even 2-3 fonts encoded in this manner tend to stay under 100kb in size.
    - Critical FOFT with preload:  
    This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we use the new preload web standard as described above in the preload method. This should trigger the download sooner than previously possible. Just add in Critical FOFT strategy HTML preload
    - Ebay/Amazon method:  
    This solution use the localStorage, FontFaceSet APIs and the Font Face Observer utility (as a backup if the FontFaceSet API is not present) with a polyfill.
    
      1. Start out by loading your page using only system fonts. Effectively killing FOIT.
      2. Later into the page load, make an asynchronous request for the webfont so the browser caches it on disk for future use. They achieve this using their own custom libraries but you can achieve something similar using fetch or libraries like https://github.com/bramstein/fontfaceobserver
      3. After the async request has completed, they set a localStorage object indicating the font “should” now be cached on disk (see below).
      4. On the next page load, an inlined piece of JS checks if this localStorage object is present. If it is, the JS sets a class on the body object enabling use of the custom webfont. Since on this second page load the webfont “should” be cached, when the webfont is now used there is no flash of unstyled text and thus no content shifting.

    https://github.com/eBay/ebay-font

3. Finaly.   
  So which method should you use to load your webfonts? Unfortunately the answer is it depends:

    - If you’re looking for a quick and simple method you can have working within an hour, use Ebay’s method
    - If you want something a bit more thorough and are alright with the limited browser support for now, try using `font-display: optional` or `font-display: swap` with service https://meowni.ca/font-style-matcher/ for better expirience
    - And if you really want to make an effort to optimize your webfont loading, take the dive and look into the `localStorage` technique with any FOFT strategies.


