---
date: "2012-08-30T00:00:00Z"
description: Monitor your resources and application
tags:
- statsd
- graphite
- monitoring
- ruby
title: Application Monitoring
---



## Importance of monitoring

Many websites go production with only monitoring online availability, but how many monitor resources, services or application errors, and their trend? Things you should monitor:

+ infracstructure resources (cpu usage, memory usage, disk space / node, ...)
+ services (database, mail server, application server, ...)
+ application errors (server errors, caching errors, queries ...)
+ user bahavior (how many attempt to log in, how long user stay on the site, ...)

It's an important point for the quality of a business to monitor a maximum of data but also to keep the information concise with dashboards and alerts. Good news, there are quite a few tools to help. For the one I know:

* simple monitor: [montastic](http://www.montastic.com/), [pingdom](http://www.pingdom.com/)
* resources and application (commercials): [new relic](http://newrelic.com/), [scout](https://scoutapp.com/), [cloudkit](https://www.cloudkick.com/), [circonus](http://circonus.com/), [server density](http://www.serverdensity.com/)
* user behavior: [intercom](https://www.intercom.io) (free)
* a long list of open source solutions: [munin](http://munin-monitoring.org/), [nagios](http://www.nagios.org/)(alerts), [monit](http://mmonit.com/monit/) (watch pro [episode on railscasts](http://railscasts.com/episodes/375-monit)), [ganglia](http://ganglia.sourceforge.net/), [zabbix](http://www.zabbix.com/), [uptime](http://fzaninotto.github.com/uptime/), [cacti](http://www.cacti.net/) + [RRDTool](http://www.rrdtool.org/) and [even more monitoring solutions](http://en.wikipedia.org/wiki/Comparison_of_network_monitoring_systems).
* tools to collect general data: [collectd](http://collectd.org/), [statsd](https://github.com/etsy/statsd), [bucky](https://github.com/cloudant/bucky), [amon](http://amon.cx/)

Update 31/08/2012 [shinken](http://www.shinken-monitoring.org/)

## StatsD / Graphite example

Last week-end I gave a try to statsd / graphite:
* [graphite](http://graphite.wikidot.com/) - "scalable realtime graphing": a python framework to display data
* [statsd](https://github.com/etsy/statsd): a simple daemons to collect data made with node.js

### Architecture

General architecture for monitoring is composed of:
* a main data storage (graphite db)
* a daemon to collect the data (statsd)
* graphing system (graphite)
* a dashboard (graphite web)

![Statsd/Graphite architecture]({{ BASE_PATH }}/images/posts/statsd_graphite_architecture.png)

### Installation


#### Pre-requirements:

You need to have a server with node.js, ruby and python installed.


#### Install graphite

A gist is better than a long speech: [Install Graphite 0.9.10 on Ubuntu 12.04](https://gist.github.com/3112065). **Don't forget to start carbon at the end**.

    cd /opt/graphite/
    sudo ./bin/carbon-cache.py start

Then test graphite

    graphite/examples$ python ./example-client.py

![Graphite resources graphic]({{ BASE_PATH }}/images/posts/graphite_resources.png)

So now we have the storage / graphing solution ready, next we need the daemon to collect data.

#### Install statsd

First clone the node.js daemon:

    git clone https://github.com/etsy/statsd

Create a config file from exampleConfig.js and put it somewhere and then start the daemon

    host: "localhost"

    node stats.js exampleConfig.js

It works also, now you can play with some ruby code.

#### The ruby

Install the gem [statsd ruby client](https://github.com/reinh/statsd)

    gem install statsd

Then use this script to create some data:

    require 'statsd'
    statsd = Statsd.new 'localhost', 8125
    statsd = Statsd.new('localhost').tap{|sd| sd.namespace = 'account'}
    (1..100).each do
      sleep rand(10)
      statsd.increment 'activate'
    end

Result:

![Graphite accounts from statsd-rb]({{ BASE_PATH }}/images/posts/statsd-rb.png)

You can now have a look to the graphite interface, all data are available and graphing option are numerous (calculation, presentation, aggregation).

Also Shopify has shared is [statsd-instrument](https://github.com/Shopify/statsd-instrument), a meta client to send data from your code and 37signals has shown its [alternative](http://37signals.com/svn/posts/3091-pssst-your-rails-application-has-a-secret-to-tell-you), but without available graphic interface, yet.

## Going further

### Other dashboards

The default dashboard is Graphite Web, but they are many others. I will try some in coming days:
* [graphiti](https://github.com/paperlesspost/graphiti)
* [graphene](https://github.com/jondot/graphene)
* [charcoal](https://github.com/cebailey59/charcoal)
* [gdash](https://github.com/ripienaar/gdash), [blog post](http://www.devco.net/archives/2011/10/08/gdash-graphite-dashboard.php)
* [tasseo](https://github.com/obfuscurity/tasseo)
* [etsy's dashboard](https://github.com/etsy/dashboard)
* [Rickshaw](http://code.shutterstock.com/rickshaw/)
* [Descartes](https://github.com/obfuscurity/descartes)

Other similar global solutions with [cube](http://square.github.com/cube/)
* [cubism](http://square.github.com/cubism/) and [crossfilter](http://square.github.com/crossfilter/)

and [Sensu](https://github.com/sensu/sensu) with its list of [plugins](https://github.com/sensu/sensu-community-plugins/tree/master/plugins).

If you don't want to bother with all the setup and technical maintenance, you can use a commercial dashboard:
* [Geckoboard](http://www.geckoboard.com/)
* [Docksboard](http://ducksboard.com/)
* [Leftronic](https://www.leftronic.com/)
* [Librato Metrics](https://metrics.librato.com/)

### Other way to collect data

Several interfaces exist and allow you to send data to the storage, here from logs:
* [logstash](https://github.com/logstash/logstash)
* [etsy's logstar](https://github.com/etsy/logster)

and this one for sending data through HTTP/JSON:
* [backstop](https://github.com/obfuscurity/backstop)

Finally if you want to notify a deployment event: [graphite-notify](https://github.com/hellvinz/graphite-notify)

## It's only the beginning

You will find a good overview of what you can monitor on the following post of Etsy, author of the node.js version of statsd, and few other tools linked: [Measure Anything, Measure Everything](http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/). Also a lot information about graphite at Jason Dixon's blog (at Github, previuously at Heroku and Circonus), [Obfuscurity.](http://obfuscurity.com/Tags/Graphite). With active work of these two people, and others, we can expect more to come in coming months.

Now you can monitor, watch and follow any metric of your business, there is no excuse not to do it and a lot to gain.

**UPDATE: extension blogs [From monitoring to public status](http://rubynaut.net/2012/08/31/from-monitoring-to-public-status/)**

**UPDATE (06/09/2012): Another for monitoring some metrics is shown with the [railscast 378](http://railscasts.com/episodes/378-fnordmetric) using [fnordmetric](https://github.com/paulasmuth/fnordmetric). Must see**

**UPDATE (08/09/2012) Interesting post from Github about monitoring responsiveness: [How we keep github fast](https://github.com/blog/1252-how-we-keep-github-fast)**

**UPDATE (09/09/2012) Video about [Monitoring and metrics at Travis](http://www.eventials.com/rubyconfbr2012/recorded/M2UzZTJkMzY2MzdiNTg2NTUxNWM1MzI3NWY1YjRhMzYjIzEyOTc_3D) and refer to [Metricks](https://github.com/eric/metriks)**

**UPDATE (31/10/2012) Shopify has released its homemage dashboard, have alook [Dashing](http://shopify.github.com/dashing/)**

**UPDATE (18/12/2012) New tool [Giraffe](https://github.com/kenhub/giraffe) and [GDash](https://github.com/ripienaar/gdash)**


<div class="BXKFNKYFPP69"></div>
