## Formatting Dates in Javascript
<!-- more -->
At some point everyone will have to format dates in a web application.  I found some really cool tools for doing that.  They are Moment.js and Pikaday.js.

<!-- more -->
![large](/img/backFuture.jpg)

### Moment.js

Before learning about moment I manually formatted dates in javascript.  Here's an example:

    var d = new Date();
    datetext = d.getHours()+":"+d.getMinutes()+":"+d.    getSeconds();
    // this part fixes the leading zeroes
    var parts = datetext.split(':');
      if (parts[0].length == 1) parts[0] = '0' + parts[0];
      if (parts[1].length == 1) parts[1] = '0' + parts[1];
      if (parts[2].length == 1) parts[2] = '0' + parts[2];
      var date = parts[0] + ":" + parts[1] + ":" +     parts[2];

That's about as fun as it looks.  Moment does all of that for us in a really easy way.

Moment can be installed in node via NPM or added directly to your frontend code with a link.  You can download the moment source code here, <a href="http://momentjs.com/">Momentjs</a>.  On my project I'm using Jade and included moment like this:

    script(src="http://momentjs.com/downloads/moment.min.js")

In the example above I wrote some code to translate the output of the javascript date function:

    Wed Mar 30 2016 14:20:28 GMT-0700 (PDT)

into this:

    14:20:28

With Moment you can accomplish the same thing with one line of code.  Like so:

    var date = moment(d).format('HH:mm:ss')
    14:20:28

Moment can work with dates and times.  To format the date you simply change the format code like this:
 
    var date = moment(d).format('MMM DD, YYYY')); 
    Mar 30, 2016

Moment is essential for formatting times and dates.

### Pikaday.js

Pikaday gives you one of those fancy calendars to pick a date on a web form.  It actually uses moment too.  

There are lots of scripts out there to accomplish this but I find Pikaday easy to use and configure.  Actually the default is really nice.  See a live demo, <a href="https://dbushell.github.io/Pikaday/">here</a>.

Pikaday worked well on a project where the user needed to enter a specifically formatted date to be used in a query.  See example below:

![large](/img/pikaday.png)

<<<<<<< HEAD
Instead of using form validation and shaming the user for entering the wrong date, we took a way the typing.  When they pick a date from the calendar it is already in the right format.
=======
Instead of using form validation and shaming the user for entering the wrong date, we took a way the typing.  When they pick a date from the calendar it is already in the right format.  Pikaday spits out the results into the form and you can do whatever you want with it.
>>>>>>> gh-pages

To include Pikaday you need the source code and their custom CSS.  The two can be included in Jade like so:

    script(src="https://cdnjs.cloudflare.com/ajax/libs/pikaday/1.4.0/pikaday.min.js")
    link(rel='stylesheet', href='http://dbushell.github.io/Pikaday/css/pikaday.css')

Then Pikaday is insanely easy to set up.  It uses a Jquery selector to bind to your form.

    // Fancy Calendar Date Picker
      var picker = new Pikaday({ 
            field: $('#GDTDT')[0],
            format: 'YYYYMMDD'
        });

In my case the form has the ID '#GDTDT'.  That's it.  Now when the user clicks on the form the calendar will pop up and allow them to select a date.

The format is determined by the "format" option for Pickaday.  It uses Moment.js for the formatting, check out the moment page for details, <a href="http://momentjs.com/">Momentjs</a>.

Pikaday has tons of options for formatting the calendar.  You can pick a custom start date, minimum and maxuimum dates, disable weekends, etc.  Full options listed on the github page, <a href="https://github.com/dbushell/Pikaday"> Pikaday Github</a>.

With these two tools you will master the use of dates in javascript.  Have fun.