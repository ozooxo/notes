# Nginx Notes

Features:

+ Reverse proxy: integration with slower upstream servers
+ Load balancer: distribute your traffic properly
+ HTTP cache

Architecture:

+ Master process
	+ read the configuration file
	+ maintain worker processes
+ Worker(s)
	+ do the actual processing of requests

Setup is modifying the Nginx configuration file, which may be in the following locations:

```
/etc/nginx/nginx.conf
/usr/local/nginx/conf/nginx.conf
/usr/local/etc/nginx/nginx.conf
```

The configuration file describe a nested structure (by `{` abd `}`), which the list of contexts follow [this document](http://nginx.org/en/docs/dirindex.html).

+ Main context: 
	+ The very outer layer
	+ Setups includes:
		+ The user and group to run the worker processes as
		+ The number of workers
		+ The file to save the main process's PID
		+ ...
+ Events context: global options how Nginx handle connection in a general level
	+ In charge of:
		+ How worker processes should handle connections
	+ `events { ... }`
+ HTTP context 
	+ `http { ... }`
+ Server context 
	+ May setup multiple virtual servers to handle incoming request.
	+ `server { ... }`: nested under HTTP context
	+ Inside directives:
		+ `listen`
		+ `server_name`
		+ `return`
		+ `root`
		+ `location`
		+ `try_files`
+ Location context 
	+ `location match_modifier location_match { ... }`: nested under HTTP context. May nested itself.
+ Upstream context 
	+ `upstream upstream_name { ... }`

## References

1. DigitalOcean [Understanding the Nginx Configuration File Structure and Configuration Contexts](https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts) lists several most popular contexts, and explain their usages.
1. [Nginx Tutorial #1: Basic Concepts](https://www.netguru.co/codestories/nginx-tutorial-basics-concepts) from netguru: mostly talking about the directives in server context, so how Nginx works as a load balancer.
1. [Official beginner tutorial](http://nginx.org/en/docs/beginners_guide.html): Really technical. Didn't give high level description.