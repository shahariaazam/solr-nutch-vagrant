# solr-nutch-vagrant

This is a Vagrantfile to create a [Vagrant](https://www.vagrantup.com/) virtual machine running the [Nutch](http://nutch.apache.org/) web crawler, and storing the results of the crawl in the [Solr](http://lucene.apache.org/solr/) search engine. It's meant for web site developers who need to develop and test search functionality on their web site. You can run this virtual machine on the machine where you're developing the web site. You can test all the search functionality of your site against a real search engine.

# Quick start

## VirtualBox and Vagrant
You need to [install VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Vagrant](https://www.vagrantup.com/downloads.html) first (and they may have their own prerequisites, depending on your operating system). The following steps are applicable for Ubuntu, Mint, Debian, etc.

    sudo apt-get install virtualbox-dkms virtualbox
    wget "https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb"
    sudo dpkg -i vagrant_1.7.2_x86_64.deb

You may want to try the [latest version of Vagrant](https://www.vagrantup.com/downloads.html), rather than the version specified above.

## The Vagrant Machine

    git clone https://github.com/lcreid/solr-nutch-vagrant.git
    cd solr-nutch-vagrant
    vagrant up

The `vagrant up` step takes a long time the first time -- about 10 minutes for me with a relatively fast Internet connection. That's because it has to download a whole Ubuntu 14.04 virtual machine, then the Java JRE. Finally, the Solr and Nutch downloads themselves are a decent size.

Once you're done downloading and installing, you can test that Solr is running by browsing to `localhost:8983/solr`. Through the magic of Vagrant, your requests will actually be sent to the Vagrant box's port 8983, which is the default Solr port for queries. Don't be too quick, however. It takes Solr a minute or two to get running enough to respond to the request.

If you try a search, you won't get any documents yet. You have to crawl the web site first.

## Configuring the URL to Crawl
You can ignore this section if the web site you want to search is on the host machine's port 3000 (e.g. being served by [Mongrel](https://rubygems.org/gems/mongrel/versions/1.1.5), like [Rails](http://rubyonrails.org) or [Nanoc](http://nanoc.ws)).

First, edit `nutch/urls/seed.txt`. Change the domain name and port to the site you want to crawl. If you're hosting the site on the same machine as you're hosting the Vagrant machine, you can leave the domain. If your web server is answering on a port other than 3000, you'll have to change the port.

Note that you can't say `localhost`, because it's going to be interpreted on the Vagrant machine. `localhost` will refer to the Vagrant machine, not to the web host you want to crawl.

Next, edit `nutch/conf/regex-urlfilter.txt`. Go to the last line, and make the obvious changes to make it consistent with the site you're crawling based on the `seed.txt` file. The spider will crawl only sites that match the regular expressions in this file. The default file provided in this Vagrant machine makes sure you only crawl on the local machine that's hosting the Vagrant machine.

Note that messing up the regular expression in `regex-urlfilter.txt` can lead to very baffling error messages, or just plain failures to do what you expect. You might want to try reading the [Nutch tutorial](http://wiki.apache.org/nutch/NutchTutorial) on setting up a spider to crawl web sites if you're having trouble.

## Running a Crawl
After installing, and periodically thereafter, you need to crawl the site you're testing. On the host machine, type:

    vagrant ssh -c "crawl"

Or log into the guest and do it there:

    vagrant ssh
    crawl

Now browse to `localhost:8983/solr/#/cark/query`, and do a search to see if you get any documents.

Note that there's no regularly-scheduled crawl set up on this box. The assumption is that you're using this for development, and would probably prefer on-demand crawls. If you want a scheduled crawl, you can add it using `cron` or any other way you're comfortable with. Remember, however, that anything you add to the machine's configuration, you'll have to redo after every time you do a `vagrant destroy`, or any time you create a new machine somewhere else (e.g. a co-worker wants to do the same thing that you're doing).

## Starting and Stopping Solr
If you're messing with Solr configuration files, you may need to start and stop Solr. You can use the standard commands `sudo stop solr` and `sudo start solr`, or even `sudo restart solr`.

## Using the Search Engine
To learn how to *use* the Solr search engine from your own site, try the [Solr Quickstart](http://lucene.apache.org/solr/quickstart.html) and the [other Solr documentation](http://lucene.apache.org/solr/resources.html) on-line.

You can also see the code for the [Jade Systems web site](https://github.com/lcreid/jade-site), which has a simple and naive implementation of a search. Note that the Jade code should not be used for a production web site, as it assumes a search engine open to anyone on the Internet.

# Support
You can report problems or contribute to this project by submitting an issue to the [Github issue tracker](https://github.com/lcreid/solr-nutch-vagrant/issues). I'm happy to entertain pull requests, but it's best to submit an issue first, so we can discuss before you run off and do a lot of work.

This machine is built on Ubuntu Server 14.04 with Solr 4.10.3 and Nutch 1.9.
