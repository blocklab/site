---
layout: page
title: blockFUNK Podcast
permalink: /blockfunk/
---

  <script src="http://code.jquery.com/jquery-1.6.4.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.20.1/moment.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.20.1/locale/de.js"></script>
  <script src="/assets/js/jquery.rss.min.js"></script>
  <script>
    jQuery(function($) {
      moment.locale('de');
      $("#rss-feed").rss("https://digitalfinance.blog/feed/podcast",
      {
        entryTemplate:'<li>
        	<strong>{date}: {title}</strong><br/>
        	<a href="{url}" target="_href">Link</a><br/>
    	    {body}
    	</li>',
    	dateFormat: 'DD.MM.YYYY',
    	dateLocale: 'de'
      })
    })
  </script>
  
  <div id="rss-feed"></div>