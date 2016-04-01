## Linux Tools - Conky
<!-- more -->
Conky is one of my favorite linux tools.  It displays information directly on your desktop.  The config file is simple to use and highly configureable.  Conky has developed a cult following amoung linux nerds.
<!-- more -->

![large](/img/Conky/conky_bubbles.jpg)

You can display just about anything on conky.  It is most often used for displaying system information such as cpu usage, memory, disk space, network speed, etc.  You an run external scripts and bash commands too.  Conky can run in a window but the cool part is running it as part of your desktop.

Conky has very well maintained code.  Check out the [github page](https://github.com/brndnmtthws/conky).  It has 3,116 commits, wow.  Conky got its name from a ventriloquist dummy on the TV show "Trailer Park Boys".  Check out the video below to see some of his anitcs.

<iframe width="600" height="450" src="https://www.youtube.com/embed/ZuhN1fUCMBM" frameborder="0" allowfullscreen></iframe>

I'll show you how to set up Conky on your machine.  I'm running Ubuntu, Lubuntu actually, but Conky is available on all linux distrubutions.

To install on Ubuntu you run:

    sudo apt-get install conky

The excitement takes place in the config file.  The default file is located at `/etc/conky/conky.conf`.  To work properly with all the cool looking stuff you need to create your own config file.  Start by copying the default to your home directory.  Like so:

    cp /etc/conky/conky.conf /home/$USER/.conkyrc

Then you can start editing, the default file is pretty user friendly and has numerous options commented out with #'s.  You can just delete the # and enable the features that you want.  Over time, like most configurations in linux, you'll know what you want and slim down the file.

Here's the options part of my .conkyrc with a screen shot on the side.

![left](/img/Conky/my_conky_crop.png)

    background yes
    cpu_avg_samples 2
    net_avg_samples 2
    out_to_console no
    use_xft yes
    xftfont Bitstream Vera Sans Mono:size=8
    xftalpha 0.8
    mail_spool $MAIL
    update_interval 1
    own_window yes
    own_window_transparent yes
    own_window_hints undecorated,below,sticky,skip_taskbar,skip_pager
    double_buffer yes
    minimum_size 400 5
    maximum_width 250
    draw_shades no
    draw_outline no
    draw_borders no
    stippled_borders 10
    border_width 0
    gap_x 15
    gap_y 30 
    alignment top_right
    use_spacer none
    no_buffers yes
    uppercase no

The most important options are `background`, which runs Conky in the background.  `minimum_size` and `maximum_width` define the size on screen.  `own_window_transparent` makes the background transparent so it blends in to your desktop. 

Next is the "code" part of the config.  I'll break it into chunks for readability.  The first part defines the clock, cpu usage bars and process data.  It's pretty self explanatory.  You can define fonts, sizes, date formats and so on.

    TEXT
    ${hr 2}
    ${alignc 19}${font Arial Black bold:size=22}${time %H:%M}${font}
    ${voffset 2}${alignc}${time %A, %d %B %Y}
    ${hr 2}
    ${font bold:size=8}${color #FFFFFF}
    CPU
    ${cpu cpu1}% ${color CCFF00}${cpubar cpu1}
    ${color #FFFFFF}${cpu cpu2}% ${color #00FFFF}${cpubar cpu2}
    ${color #FFFFFF}Load Av $alignr${loadavg}
    processes: $processes  $alignr running: $running_processes
    uptime: $uptime

Next I define the memory usage bars and hard drive usage.  The bar heights are defined by this bit `${membar 15,200}` where height is 15 and length is 200 pixels.  You can easily pick the colours and mount points for dives to be used.

     MEM: ${alignr}$mem / $memmax
        ${color blue}${membar 15,200}
    ${color #FFFFFF}SWAP: ${alignr}$swap / $swapmax 
       ${color blue}${swapbar 15,200}
    ${color #FFFFFF}Home   ${alignr}${fs_used /} / ${fs_size /}
        ${fs_bar 8,200 /}
     DATA   ${alignr}${fs_used /media/ng/DATA} / ${fs_size /media/ng/DATA}
        ${fs_bar 8,200 /media/ng/DATA}
     WIN ${alignr}${fs_used /media/ng/48F49F9AF49F8938} / ${fs_size /media/ng/48F49F9AF49F8938}
        ${fs_bar 8,200 /media/ng/48F49F9AF49F8938}

  The next section shows the most resouce expensive processes.  This is really helpful for me since I am running an old/slow laptop at the moment.  I just have to look at my desktop to see where the bottlenecks are.
    
    cpu usage:  ${alignr}CPU%   
     ${top name 1}  ${alignr}${top cpu 1}
     ${top name 2}  ${alignr}${top cpu 2}
     ${top name 3}  ${alignr}${top cpu 3}
     ${top name 4}  ${alignr}${top cpu 4}
    
    mem usage:  ${alignr}MEM%   
     ${top_mem name 1}  ${alignr}${top_mem mem 1}
     ${top_mem name 2}  ${alignr}${top_mem mem 2}
     ${top_mem name 3}  ${alignr}${top_mem mem 3}
     ${top_mem name 4}  ${alignr}${top_mem mem 4}
    
Next is the network section.  Simalr to the rest, except here I am running an external bash command.  That is executed by `${execi 600 ...`.  I'm using w3m (terminal based browser) to scrape my external IP address from whatismyip.com.  You could do all kinds of stuff with this.  It is also great to have you internal and external IP address available at a glance.

    NETWORK
     hardline $alignr${addr eth0}
     wireless $alignr${addr wlp4s0}
     external $alignr${execi 600  w3m -no-cookie http://www.whatismyip.com/ | grep -A1 Is: | tail -n 1}
     ng-server $alignr 70.69.246.142
    
    Download Speed $alignr${downspeedf wlp4s0} kb/s
      ${color green}${downspeedgraph wlp4s0 15,200}
    ${color #FFFFFF}Upload Speed $alignr${upspeedf wlp4s0} kb/s
      ${color pink}${upspeedgraph wlp4s0 15,200}
    ${color orange}

My config is pretty stratightforward.  You can get really creative with it.  You can run all kinds of scripts such as Ruby, Perl, Python or anything else.  I used to have one that would display a list of to-do items from file.

There are some great examples of Conky config files at this link [ubuntuforums.org/showthread.php?t=281865](http://ubuntuforums.org/showthread.php?t=281865)

Check out these cool Conky set ups that I found online.

![sml](/img/Conky/bloody.png)
![sml](/img/Conky/circles.png)
![sml](/img/Conky/karmic.png)
![sml](/img/Conky/grass.png)
![sml](/img/Conky/bb.png)
![sml](/img/Conky/full.png)