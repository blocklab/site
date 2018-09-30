---
layout: page
title: blockFUNK Podcast
permalink: /blockfunk/
---

  <script src="https://code.jquery.com/jquery-1.6.4.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.20.1/moment.min.js"></script>
  <script src="/assets/js/jquery.rss.min.js"></script>
  <script>
    jQuery(function($) {
      $("#rss-feed").rss("https://digitalfinance.blog/feed/podcast",
      {
        entryTemplate:'<li class="blockfunk"><h3>{date}: {title}</strong></h3>
        	<a href="{url}" target="_href">Link</a><br/>
    	    {body}{podcast}</li>',
    	  dateFormat: 'DD.MM.YYYY',
    	  dateLocale: 'de',
    	  ssl: true,
        tokens: {
          podcast: function (entry, tokens) {}
        }
      })
    })
  </script>
  
  <div class="blockfunk-feed" id="rss-feed"></div>
