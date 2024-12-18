# Web Scraping Tutorial

This tutorial describes the steps to scrape and download a website. This document was prepared for data preservation efforts in the fall of 2024. Some considerations when scraping a website: do you have enough local storage? It's pretty much impossible to know how big a site will be once downloaded (granted, the script will just stop running if it fills up your storage space, it won't lose the progress you've made) so I would recommend trying to run this in an environment with a sizeable (e.g. 100+ GB) amount of storage. You'll know from the log messages whether you completed your scrape or if it exited because you ran out of storage space.

Once you've downloaded a site, upload it to a storage site that guarantees long-term storage so that others can benefit from the data preservation.
## Get the tool

### On mac/linux:


#### Using brew:

First install brew:

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

And then install wget with brew:

`brew install wget`


#### Using MacPorts

First, download and run [MacPorts](https://www.macports.org/install.php) installer (.pkg)

And then install wget:

`sudo port install wget`


### On windows:

Download [WGET for Windows](https://gnuwin32.sourceforge.net/packages/wget.htm)

## Using WGET

To scrape an entire website, I've been using the following command:
`wget -m -k -K -l 10 -t 6 -w 0.5 http://www.website.com`

### Explanation

From [a handy stackoverflow post](https://stackoverflow.com/questions/9265172/scrape-an-entire-website), I got the following insight into the switches:

-m Essentially, this means “mirror the site”, and it recursively grabs pages & images as it spiders through the site. It checks the timestamp, so if you run wget a 2nd time with this switch, it will only update files/pages that are newer than the previous time.

-k This will modify links in the html to point to local files. If instead of using things like page2.html as links throughout your site you were actually using a full `http://www.website.com/page2.html` you’ll probably need/want this. I turn it on just to be on the safe side – chances are at least 1 link will cause a problem otherwise.

-K The option above (lowercase k) edits the html. If you want the “untouched” version as well, use this switch and it will save both the changed version and the original. It’s just good practise in case something is awry and you want to compare both versions. You can always delete the one you didn’t want later.

-E This saves HTML & CSS with “proper extensions”. Careful with this one – if your site didn’t have .html extensions on every page, this will add it. However, if your site already has every file named with something like “.htm” you’ll now end up with “.htm.html”.

-l 10 By default, the -m we used above will recurse/spider through the entire site. Usually that’s ok. But sometimes your site will have an infinite loop in which case wget will download forever. Think of the typical website.com/products/jellybeans/sort-by-/name/price/name/price/name/price example. It’s somewhat rare nowadays – most sites behave well and won’t do this, but to be on the safe side, figure out the most clicks it should possibly take to get anywhere from the main page to reach any real page on the website, pad it a little (it would be disappointing if you used a value of 7 and found out an hour later that your site was 8 levels deep!) and use that #. Of course, if you know your site has a structure that will behave, there’s nothing wrong with omitting this and having the comfort of knowing that the 1 hidden page on your site that was 50 levels deep was actually found.

-t 6 If trying to access/download a certain page or file fails, this sets the number of retries before it gives up on that file and moves on. You usually do want it to eventually give up (set it to 0 if you want it to try forever), but you also don’t want it to give up if the site was just being wonky for a second or two. I find 6 to be reasonable.

-w 0.5 This tells wget to wait a few seconds (0.5 seconds in this case) before grabbing the next file. It’s often critical to use something here. By default, wget will grab pages as fast as it possibly can. This can easily be multiple requests per second which has the potential to put huge load on the server (particularly if the site is written in PHP, makes MySQL accesses on each request, and doesn’t utilize a cache). If the website is on shared hosting, that load can get someone kicked off their host. Even on a VPS it can bring some sites to their knees. And even if the site itself survives, being bombarded with an insane number of requests within a few seconds can look like a DOS attack which could very well get your IP auto-blocked. If you don’t know for certain that the site can handle a massive influx of traffic, use the -w # switch.
