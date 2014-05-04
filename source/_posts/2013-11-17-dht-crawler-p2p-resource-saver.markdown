---
layout: post
title: "Magnet Searcher Engine, DHT Crawler, P2P Resource Saver"
date: 2013-11-17 00:07
comments: true
categories: TechSpark
tags: [JustForFun, python, torrent, MagnetSearcher, SearchEngine]
keywords: 磁力搜索, 磁力搜索原理, 磁力搜索技术
---

1.Preface
-------

In China, Pirate Party, as hot as The Communist Party, can get some resources like movies, books, tv series, easily and free, without danger because of some reason.

<!-- more -->

And there are too many ways to get them, such as http, ftp, bt, ed2k and too many tools to work in protocol as refer.  However, one of the worst things is how to find the links.  

BT and EMule is the most popular, because it works by p2p tech (peer to peer, a computing or networking distributed application architecture that partitions tasks or workloads among peers)  

[DHT](http://en.wikipedia.org/wiki/Distributed_hash_table) is more advantaged architecture, very interesting, which has two key feature:

a, distributed, peer to peer  
b, decentralized  


decentralized (no boss, democracy) and distributed (every small person involved in the jobs, but the team, the big team is great, beyond your imagination)

Some product list is like this:

[磁力搜索](http://bt.shousibaocai.com/)

[磁力搜索引擎](http://www.wotorrent.com/)

[磁力搜](http://cili.so/)

2.Magnet Searcher Engine
----------

A search engine should have three parts:

####a, data  

The key module, you have to own the magnet links.  
Searcher engine, like google, baidu? yep ,one way.  

The greater way is get them by dht crawler which i will give the desciption later.

####b, index & weight  

Making the index, for latter search more quickly.  
Give every link some weight, and it will be used in rank

####c, search & rank  

the service for keyword search
rank it 
some post process, like making unique, making some special format better rank, etc



3, Data Module - DHT Crawler
-----------

It works in THREE steps: getting id, detail and file

####a, get the source id

According to DHT procotol, you can pretend a p2p client, get all info by peers's "get_peers" package


The more code: [https://github.com/arekyao/dht-crawler](https://github.com/arekyao/dht-crawler)

```python

def handle_query(self, message):
        trans_id = message["t"]
        query_type = message["q"]
        args = message["a"]
        node_id = args["id"]

        client_host, client_port = self.client_address
        logger.debug("Query message %s from %s:%d, id:%r" % (query_type, client_host, client_port, node_id.encode("hex")))
        
        # Do we know already about this node?
        node = self.server.dht.rt.node_by_id(node_id)
        if not node:
            node = Node(client_host, client_port, node_id)
            logger.debug("We don`t know about %r, add it as new" % (node))
            self.server.dht.rt.update_node(node_id, node)
        else:
            logger.debug("We already know about: %r" % (node))

        node.update_access()

        if query_type == "ping":
            logger.debug("handle query ping")
            node.pong(socket=self.server.socket, trans_id = trans_id, sender_id=self.server.dht.node._id, lock=self.server.send_lock)
        elif query_type == "find_node":
            logger.debug("handle query find_node")
            target = args["target"]
            found_nodes = encode_nodes(self.server.dht.rt.get_close_nodes(target, 8))
            node.found_node(found_nodes, socket=self.server.socket, trans_id = trans_id, sender_id=self.server.dht.node._id, lock=self.server.send_lock)
        elif query_type == "get_peers":
            logger.debug("handle query get_peers")
            logger.error(args)
            logger.error("get_peer_info_hash_crawler:" + args['info_hash'].encode("hex"))
            node.pong(socket=self.server.socket, trans_id = trans_id, sender_id=self.server.dht.node._id, lock=self.server.send_lock)
            return
        elif query_type == "announce_peer":
            logger.debug("handle query announce_peer")
            logger.error(args)
            logger.error("announce_peer_info_hash_crawler:" + args['info_hash'].encode("hex"))
            node.pong(socket=self.server.socket, trans_id = trans_id, sender_id=self.server.dht.node._id, lock=self.server.send_lock)
            return
        else:
            logger.error("Unknown query type: %s" % (query_type))

```

####b, get the detail info, like whether availble

Send "announce_peer" package to peers, to announce that:  
You wanna the resouce, bla bla


####c, get the torrent file, and the desciption

with the infohash, you can easy to get the file by this:

> "http://torrage.com/torrent/" ++ MagHash ++ ".torrent"  
> "https://zoink.it/torrent/" ++ MagHash ++ ".torrent"  
> http://bt.box.n0808.com  

```bash

awk '{
    code=toupper($1);
    head=substr(code,0,2)
    tail=substr(code,39,2)
    print "http://bt.box.n0808.com/"head"/"tail"/"code".torrent"
}'

```

Yep, you already have the torrent files, decode the files, and you can get more info.


4, Index module & Rank module
-------------------

It's typical search engine modules.  
Easy to get lots of infomations on Internet.

5, About [DHT protocol](http://www.bittorrent.org/beps/bep_0005.html)
--------

Four Query Operation:

* ping: check a node alive

* find_node: find some node

* get_peers: get some resouce and querying by id

* announce_peer: announce downloading some resouce.


Reference
------

* http://blog.csdn.net/liweisnake/article/details/9207919
* http://codemacro.com/2013/07/02/dhtcrawler2/
* http://gobismoon.blog.163.com/blog/static/5244280220100893055533/

