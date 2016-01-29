---
layout: post
title:  primus transformers 比较
category: 技术
tags: primus, transformers 
keywords: primus, transformers 
---

# primus transformers 比较

## websockets
    只支持 WebSocket


## engine.io

### Transports：

    1. polling: XHR / JSONP polling transport.
    2. websocket: WebSocket transport.

## socket.io

Socket.io 0.9.X 版本 不支持1.0 以上 。 1.0以上使用engine.io
没有官方支持。

## browserchannel

不支持跨越连接
BrowserChannel   使用长连接， 不使用websocket。 可以在任何网络环境下使用。

## sockjs

websocket -> html stream -> long polling -> ajaxjsonp

使用Coffee Script编写

## faye

   只支持 WebSocket

