 Hello, now we talk about problems for fonts loading.  
  Webfonts are one of the very first things we add to our sites—whether it be for stylistic reasons. But, they are also infamous for making our sites painfully slow and frustrating to read. The end result? Frustrated users and lots of lost business opportunities. 
  With no effective font loading strategy, users will experiment what’s call FOIT (Flash of Invisible Text) as the font files are downloading.  
  Instead it’s preferable to go for FOUT (Flash of Unstyled Text), users will see content sooner with a font from the system and switch to the web font later.  
  FOFT: "Flash of Faux Text". The idea here is only waiting for the Roman variant of the custom font to load, and setting text in that right away. You force the browser to create "faux bold" and "faux italic" where needed, which are replaced by the real versions as soon as those are downloaded. 
  Now for the last issue, which people don’t think about very often: Content Shifting. Ever been reading an article and 5-10 seconds into reading, the fonts change and everything on the page scrolls up?   
 Now that we know the font loading problems, let’s look at the font loading methods you can use to avoid these problems completely.
    - Simple methods.  
    The new font-display descriptor for @font-face lets decide how web fonts will render, depending on how long it takes for them to load.     
    The font display timeline is based on a timer that begins the moment the user agent attempts to use a given downloaded font face. The timeline is divided into the three periods below which dictate the rendering behavior of any elements using the font face.  
    Font block period: If the font face is not loaded, any element attempting to use it must render an invisible fallback font face. If the font face successfully loads during this period, it is used normally.  
    Font swap period: If the font face is not loaded, any element attempting to use it must render a fallback font face. If the font face successfully loads during this period, it is used normally.  
    Font failure period: If the font face is not loaded, the user agent treats it as a failed load causing normal font fallback.   
    swap - gives the font face an extremely small block period and an infinite swap period.  
    optional - gives the font face an extremely small block period and no swap period. 
    At time of writing, this feature is available in the latest versions Edge, Firefox, Chrome, Safari and Opera, but is not available at mobile browsers.  
    Simple `@font-face`. Throw a naked @font-face block on your page. This is the default approach recommended by Google Fonts. Just add a CSS @font-face block with WOFF and WOFF2 formats or another formats if you need.
      - `font-dispay: swap`:
      Add a new font-display: swap descriptor to your @font-face block to opt-in to FOUT on browsers that support it. Optionally, font-display: fallback or font-display: optional can be used if you consider web fonts to be unnecessary to the design.
      - preload:  
      Just add preload to fetch your font sooner.  
      - `font-dispay: swap` with preload. Pairs nicely with an previous simple `@font-face` block and feel free to also throw in the previous `font-display` descriptor as well for looking good. Just combine the two previous points, and be happy.
    - FOUT with a Class:  
    Use the CSS Font Loading API with a polyfill to detect when a specific font has loaded and only apply that web font in your CSS after it has loaded successfully. Usually this means toggling a class on your <html> element.   
    - FOUT:  
    Similar to the above, but without using a class. Using only the CSS Font Loading API. This doesn’t require any modification of the CSS, injects the web fonts using JavaScript programmatically.  
    - FOFT:  
    This approach builds on the FOUT with a Class method and is useful when you’re loading multiple weights or styles of the same typeface, like roman, bold, italic, bold italic, book, heavy, and others. We split those web fonts into two stages: the roman first, which will then also immediately render faux-bold and faux-italic content (using font synthesis) while the real web fonts for heavier weights and alternative styles are loading. 
    - Critical FOFT:  
    The only difference between this method and standard FOFT approach is that instead of the full roman web font in the first stage, we use a subset roman web font (usually only containing A-Z and optionally 0-9 and/or punctuation). The full roman web font is instead loaded in the second stage with the other weights and styles. 
    - Critical FOFT with Data URI:  
    This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we simply embed the web font as a inline Data URI directly in the markup.  
    Just change url in base-64 encoding font. This might sound like a lot of data to store but even 2-3 fonts encoded in this manner tend to stay under (ванхандрид килобитс)100kb in size.
    - Critical FOFT with preload:  
    This variation of the Critical FOFT approach simply changes the mechanism through which we load the first stage. Instead of using our normal font loading JavaScript API to initiate a download, we use the new preload web standard as described above in the preload method. This should trigger the download sooner than previously possible. Just add in Critical FOFT strategy HTML "preload".
    - Ebay/Amazon method:  
    This solution use the localStorage, FontFaceSet APIs and the Font Face Observer utility (as a backup if the FontFaceSet API is not present) with a polyfill.
    
      1. Start out by loading your page using only system fonts. Effectively killing FOIT.
      2. Later into the page load, make an asynchronous request for the webfont so the browser caches it on disk for future use. They achieve this using their own custom libraries but you can achieve something similar using fetch or libraries like font face observer
      3. After the async request has completed, they set a localStorage object indicating the font “should” now be cached on disk (see below).
      4. On the next page load, an inlined piece of JS checks if this localStorage object is present. If it is, the JS sets a class on the body object enabling use of the custom webfont. Since on this second page load the webfont “should” be cached, when the webfont is now used there is no flash of unstyled text and thus no content shifting.

Finaly.   
  So which method should you use to load your webfonts? Unfortunately the answer is it depends:

    - If you’re looking for a quick and simple method you can have working within an hour, use Ebay’s method
    - If you want something a bit more thorough and are alright with the limited browser support for now, try using `font-display: optional` or `font-display: swap` with service font-style-matcher for better expirience
    - And if you really want to make an effort to optimize your webfont loading, take the dive and look into the `localStorage` technique with any FOFT strategies.


