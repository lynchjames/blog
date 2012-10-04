---
title: Running CouchDB Under IIS 7
tagline: A 12 Step Program
layout: post
category: .net
tags : [couchdb, iis, windows, devops]
---
When using CouchDB on .net projects I had always used a hosting provider such as [Cloudant](https://cloudant.com) or [Iris Couch](http://www.iriscouch.com). Recently, I worked on a project that required a CouchDB instance that was hosted on the same platform which meant I needed to figure out a way to host a CouchDB instance on windows under IIS.

It turned out to be reasonably straightforward.

1. Install CouchDB on the server using a [windows installer](http://couchdb.apache.org/#download).
2. Check the install on the server at [http://localhost:5984](http://localhost:5984).
3. Install the [ARR IIS feature](http://www.iis.net/downloads/microsoft/application-request-routing).
4. Open the ARR feature found at the server root in IIS.
5. Click Server Proxy Settings.
6. Click the checkbox to enable proxy and click apply.
7. Create a site in IIS pointing to an empty folder with a hostname.
8. Open Url Rewrite in the site properties.
9. Add a blank rule with the following properties:
	- Match Url Pattern: .\*
	- Action type: Rewrite
	- Append query string: checked
	- Rewrite Url: 

			http://localhost:5984/{R:0}

10. You should now be able to access the CouchDB instance using the hostname of the IIS site using any/all of the CouchDB HTTP api.
11. The only CouchDB feature that doesn’t work at this point is Continuous Replication. To enable this, set the following properties in the ARR proxy settings:
	- Time-out: 86400
	- Response buffer threshold: 0
12. Check that you can stream from the proxy site using an open connection with a url like:

		http://[CouchDB_Site_Hostname]/[Database_Name]/_changes?feed=continuous&style=all_docs&since=0&heartbeat=10000

You should not have a CouchDB instance running under localhost:5984 that is proxied by a site running in IIS with full support for the CouchDB Http Document API, including replication.
