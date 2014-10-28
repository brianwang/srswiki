# RTMP URL/Vhost

The url of RTMP is simple, and the vhost is not a new concept, although it's easy to confuse for the fresh. What is vhost? What is app? Why FMLE should input the app and stream?

The benifit of RTMP and HLS, see: [HLS](https://github.com/winlinvip/simple-rtmp-server/wiki/v1_EN_DeliveryHLS)

## Use Scenario

The use scenario of vhost:
* Multiple customers cloud: For example, CDN(content delivery network) serves multiple customers. How does CDN to seperate customer and stat the data? Maybe stream path is duplicated, for example, live/livestream is the most frequently used app and stream. The vhost, similar to the virtual server, provides abstract for multiple customers.
* Different config: For example, FMLE publish h264+mp3, which should be transcoded to h264+aac. We can use vhost to use different config, h264+mp3 disabled hls while the h264+aac vhost enalbe hls.

In a word, vhost is the element of config, to seperate customer and apply different config.

## Standard RTMP URL

Standard RTMP URL is the most compatible URL, for all servers and players can identify. The RTMP URL is similar to the HTTP URL:

<table>
<thead>
<tr>
<th>HTTP</th>
<th>Schema</th>
<th>Host</th>
<th>Port</th>
<th colspan=2>Path</th>
</tr>
</thead>
<tbody>
<tr>
<td>http://192.168.1.10:80/players/srs_player.html</td>
<td>http</td>
<td>192.168.1.10</td>
<td>80</td>
<td colspan=2>/players/srs_player.html</td>
</tr>
<tr>
<td>rtmp://192.168.1.10:1935/live/livestream</td>
<td>rtmp</td>
<td>192.168.1.10</td>
<td>1935</td>
<td>live</td>
<td>livestream</td>
</tr>
</tbody>
<tfoot>
<tr>
<th>RTMP</th>
<th>Schema</th>
<th>Host</th>
<th>Port</th>
<th>App</th>
<th>Stream</th>
</tr>
</tfoot>
</table>

It is:
* Schema：The protocol prefix, HTTP/HTTPS for HTTP protocol, and RTMP/RTMPS/RTMPE/RTMPT for RTMP protocol, while the RTMFP is adobe flash p2p protocol.
* Host：The server ip or dns name to connect to. It is dns name for CDN, and the dns name is used as the vhost for the specified customer.
* Port：The tcp port, default 80 for HTTP and 1935 for RTMP.
* Path：The http file path for HTTP.
* App：The application for RTMP, similar to the directory of resource(stream).
* Stream：The stream for RTMP, similar to the resource(file) in specified app.

## NoVhost

However, most user donot need the vhost, and it's a little complex, so donot use it when you donot need it. Most user actually only need app and stream.

When to use vhost? When you serve 100+ customers and use the same delivery network, they use theire own vhost and can use the sample app and stream.

The common use scenario, for example, if you use SRS to build a video website. So you are the only customer, donot need vhost. Suppose you provides video chat, there are some categories which includes some chat rooms. For example, categories are military, reader, history. The military category has rooms rock, radar; the reader category has red_masion. The config of SRS is very simple:

```bash
listen              1935;
vhost __defaultVhost__ {
}
```

When generate web pages, for instance, military category, all app is `military`. The url of chat room is `rtmp://yourdomain.com/military/rock`, while the encoder publish this stream, and all player play this stream.

The other pages of the military category use the same app name `military`, but use the different stream name, fr example, radar chat room use the stream url `rtmp://yourdomain.com/military/radar`.

When generate the pages, add new stream, the config of SRS no need to change. For example, when add a new chat room cannon, no need to change config of SRS. It is simple enough!

The reader category can use app `reader`, and the `red_mansion` chat room can use the url `rtmp://yourdomain.com/reader/red_mansion`.

## Vhost Use scenario

The vhost of RTMP is same to HTTP virtual server. For example, the demo.srs.com is resolve to 192.168.1.10 by dns or hosts:

<table>
<thead>
<tr>
<th>HTTP</th>
<th>Host</th>
<th>Port</th>
<th>VHost</th>
</tr>
</thead>
<tbody>
<tr>
<td>http://demo.srs.com:80/players/srs_player.html</td>
<td>192.168.1.10</td>
<td>80</td>
<td>demo.srs.com</td>
</tr>
<tr>
<td>rtmp://demo.srs.com:1935/live/livestream</td>
<td>192.168.1.10</td>
<td>1935</td>
<td>demo.srs.com</td>
</tr>
</tbody>
<tfoot>
<tr>
<th>RTMP</th>
<th>Host</th>
<th>Port</th>
<th>VHost</th>
</tr>
</tfoot>
</table>

The use scenario of vhost:
* Multiple Customers: When need to serve multiple customers use the same network, for example, cctv and wasu delivery stream on the same CDN, how to seperate them, when they use the same app and stream?
* DNS scheduler: When CDN delivery content, the fast edge for the specified user is resolved for the dns name. We can use vhost as the dns name to scheduler user to different edge.
* Multiple Config sections: Sometimes we need different config, for example, to delivery RTMP for PC and transcode RTMP to HLS for android and IOS, we can use one vhost to delivery RTMP and another for HLS.

### Vhost For Multiple Customers

For example, we got two customers cctv and wasu, use the same edge server 192.168.1.10, when user access the stream of these two customers:

<table>
<thead>
<tr>
<th>RTMP</th>
<th>Host</th>
<th>Port</th>
<th>VHost</th>
<th>App</th>
<th>Stream</th>
</tr>
</thead>
<tbody>
<tr>
<td>rtmp://show.cctv.cn/live/livestream</td>
<td>192.168.1.10</td>
<td>1935</td>
<td>show.cctv.cn</td>
<td>live</td>
<td>livestream</td>
</tr>
<tr>
<td>rtmp://show.wasu.cn/live/livestream</td>
<td>192.168.1.10</td>
<td>1935</td>
<td>show.wasu.cn</td>
<td>live</td>
<td>livestream</td>
</tr>
</tbody>
</table>

The config on the edge 192.168.1.10, need to config the vhost:

```bash
listen              1935;
vhost show.cctv.cn {
}
vhost show.wasu.cn {
}
```

### Vhost For DNS scheduler

Please refer to the tech for DNS and CDN.

### Vhost For Multiple Config

For example, two customers cctv and wasu, and cctv needs mininum latency, while wasu needs fast startup. 

Then we config the cctv without gop cache, and wasu config with gop cache:

```bash
listen              1935;
vhost show.cctv.cn {
    gop_cache       off;
}
vhost show.wasu.cn {
    gop_cache       on;
}
```

These two vhosts is completely isolated.

## \_\_defaultVhost\_\_

The default vhost is \_\_defaultVhost\_\ introduced by FMS. When mismatch and vhost not found, use the default vhost if configed.

For example, the config of SRS on 192.168.1.10:

```bash
listen              1935;
vhost demo.srs.com {
}
```

Then, when user access the vhost:
* rtmp://demo.srs.com/live/livestream：OK, matched vhost is demo.srs.com.
* rtmp://192.168.1.10/live/livestream：Failed, no matched vhost, and no default vhost.

The rule of default vhost is same to other vhost, the default is used for the vhost not matched and not find.

## Access Specified Vhost

There are two ways to access the vhost on server:
* DNS name: When access the dns name equals to the vhost, by dns resolve or hosts file, we can access the vhost on server.
* App parameters: When connect to tcUrl on server, that is, connect to the app, the parameter can specifies the vhost. This needs the server supports this way, for example, SRS can use parameter ?vhost=VHOST and ...vhost...VHOST to access specified vhost.

For example:

```bash
RTMP URL: rtmp://demo.srs.com/live/livestream
Edge servers: 50 servers
Edge server ip: 192.168.1.100 to 192.168.1.150
Edge SRS config:
    listen              1935;
    vhost demo.srs.com {
        mode remote;
        origin: xxxxxxx;
    }
```

The ways to access the url on edge servers:

<table>
<thead>
<tr>
<th>User</th>
<th>RTMP URL</th>
<th>Hosts file</th>
<th>Target</th>
</tr>
</thead>
<tbody>
<tr>
<td>User</td>
<td>rtmp://demo.srs.com/live/livestream</td>
<th>NO</th>
<td>Scheduled by DNS</td>
</tr>
<tr>
<td>Admin</td>
<td>rtmp://demo.srs.com/live/livestream</td>
<th>192.168.1.100 demo.srs.com</th>
<td>Stream on 192.168.1.100</td>
</tr>
<tr>
<td>Admin</td>
<td>rtmp://192.168.1.100/live?<br/>vhost=demo.srs.com/livestream</td>
<th>NO</th>
<td>Stream on 192.168.1.100</td>
</tr>
<tr>
<td>Admin</td>
<td>rtmp://192.168.1.100/live<br/>...vhost...demo.srs.com/livestream</td>
<th>NO</th>
<td>Stream on 192.168.1.100</td>
</tr>
</tbody>
</table>

It is sample way to access other servers.

## FMLE的奇怪URL方式

FMLE推流时，URL那个地方，有三个可以输入的框，参考[Adobe FMLE](http://help.adobe.com/en_US/FlashMediaLiveEncoder/3.0/Using/WS5b3ccc516d4fbf351e63e3d11c104ba878-7ff7.html)：
* FMS URL: 需要输入rtmp://host:port/app，例如：rtmp://demo.srs.com/live
* Backup URL: 备份的服务器，格式同FMS URL。若指定了备份服务器，FMLE会同时推送给这两个服务器。
* Stream: 流名称，例如：livestream

实际上是将RTMP URL分成了两部分，stream前面那部分和stream。为何要这么搞？我猜想有以下原因：
* 支持多级app和Stream：我们目前举的例子都是一级app和一级stream，实际上RTMP支持多级app和stream，就像子文件夹，实际上很少用得到。所以SRS的URL都是一个地址，默认最后一个/后面就是stream，前面是app。
* 支持流名称带参数：Adobe的鬼HLS/HDS非常之麻烦，那个地址是个恶心的完全不一致。参考[FMS livepkgr](http://help.adobe.com/en_US/flashmediaserver/devguide/WSd391de4d9c7bd609-52e437a812a3725dfa0-8000.html#WSd391de4d9c7bd609-52e437a812a3725dfa0-7ff5)，例如发布一个rtmp，并切片成HLS：
```bash
FMLE:
FMS URL: rtmp://demo.srs.com/livepkgr
Stream: livestream?adbe-live-event=liveevent

Client:
RTMP:  rtmp://demo.srs.com/livepkgr/livestream
HLS: http://demo.srs.com/hls-live/livepkgr/_definst_/liveevent/livestream.m3u8
HDS: http://demo.srs.com/hds-live/livepkgr/_definst_/liveevent/livestream.f4m
```
没有比这个更恶心的东西了。比较SRS的简洁方案：
```bash
FMLE: 
FMS URL: rtmp://demo.srs.com/livepkgr
Stream: livestream

Client:
RTMP: rtmp://demo.srs.com/livepkgr/livestream
HLS: http://demo.srs.com/livepkgr/livestream.m3u8
HDS: not support yet.
```

既然谈到了RTMP URL中的参数，下一章就说说这个。

## RTMP URL参数

RTMP URL一般是不带参数，类似于http的query，有时候为了特殊的要求，会在RTMP URL中带参数，譬如：
* Vhost：前面讲过，在app后面加参数，可以访问指定服务器的指定Vhost。这个SRS的特殊约定，方便排错。
* FMLE的Stream后面的参数，指定event之类的。SRS不需要这么麻烦，HLS是内置支持，无需这种复杂的配置。Callback也是http的，FMS为了支持服务器端脚本，需要很复杂的配置和复杂的参数，实在是很麻烦的设计。
* token认证：SRS还未实现。在连接服务器时，在app后面指定token（方式和vhost一样），例如rtmp://server/live?vhost=xxx&token=xxx/livestream，服务器可以取出token，进行验证，若验证失败则断开连接，这种是比Refer更高级的防盗链。

app和stream后面带参数，这两者有何区别，为何SRS把参数放在app后面？客户端播放流的as3代码大约是：

```as
// how to play url: rtmp://demo.srs.com/live/livestream
conn = new NetConnection();
conn.connect("rtmp://demo.srs.com/live");

stream = new NetStream(conn);
stream.play("livestream");
```

从RTMP协议的角度来看：
* NetConnection.connect(vhost+app)：这一步会完成握手，connect到vhost，切换到app。类似于登录到vhost后，cd到app这个目录。也就是vhost的验证，都可以在这一步做，也就是指定vhost也是在一步了，所以app后面跟的参数都是和vhost/app相关的。
* NetStream.play(stream)：这一步是播放指定的直播流。所以和stream相关的事件，都可以传递参数，譬如Adobe的event。SRS是没有这些事件的，流启动时，若配置了HLS会自动开始切片。

## SRS的URL规则

SRS只做简化的事情，绝对不把简单的事情搞复杂。

SRS的RTMP URL使用标准的RTMP URL，一般不需要对app和stream加参数，或者更改他们的意义。除了两个地方：
* vhost支持参数访问：为了方便运维访问某台服务器的vhost，不需要设置hosts。不影响普通用户。
* 支持token验证：为了支持token验证，在app后面带参数，这个是token验证必须的方式。

另外，SRS建议用户使用一级app和一级stream，不使用多级app和多级stream。譬如：

```bash
// 不推荐使用的多级app或stream
rtmp://demo.srs.com/show/live/livestream
rtmp://demo.srs.com/show/live/livestream/2013
```

srs播放器(srs_player)和srs编码器(srs_publisher)不支持多级app和stream，他们认为最后一个斜杠（/）后面的就是stream，前面的是app。即：

```bash
// srs_player和srs_publisher的解析方式：
// play or publish the following rtmp URL:
rtmp://demo.srs.com/show/live/livestream/2013
schema: rtmp
host/vhost: demo.srs.com
app: show/live/livestream
stream: 2013
```

做此简化的好处是，srs播放器和编码器，只需要指定一个url，而且两者的url是一样的。

SRS常见的三种RTMP URL，详细见下表：
<table>
<thead>
<tr>
<th>URL</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>rtmp://demo.srs.com/live/livestream</td>
<td>普通用户的标准访问方式，观看直播流</td>
</tr>
<tr>
<td>rtmp://192.168.1.10/live?vhost=demo.srs.com/livestream</td>
<td>运维对特定服务器排错</td>
</tr>
<tr>
<td>rtmp://demo.srs.com/live?key=ER892ID839KD9D0A1D87D/livestream</td>
<td>token验证用户，或者带宽测试的key验证</td>
</tr>
</tbody>
</table>

## SRS的Vhost

SRS的full.conf配置文件中，有很多Vhost，主要是为了说明各个功能，每个功能都单独列出一个vhost。所有功能都放在demo.srs.com这个vhost中。

<table>
<thead>
<th>Category</th>
<th>Vhost</th>
<th>说明</th>
</thead>
<tbody>
<tr>
<td>RTMP</td><td>__defaultVhost__</td><td>默认Vhost的配置，只支持RTMP功能</td>
</tr>
<tr>
<td>RTMP</td><td>chunksize.vhost.com</td><td>如何设置chunk size的实例。其他Vhost将此配置打开，即可设置chunk size。</td>
</tr>
<tr>
<td>Forward</td><td>same.vhost.forward.vhost.com</td><td>Forward实例：将流转发到同一个vhost。</td>
</tr>
<tr>
<td>Forward</td><td>change.vhost.forward.vhost.com</td><td>Forward实例：转发时，转发到服务器的不同vhost。</td>
</tr>
<tr>
<td>HLS</td><td>with-hls.vhost.com</td><td>HLS实例：如何开启HLS，以及HLS的相关配置。</td>
</tr>
<tr>
<td>HLS</td><td>no-hls.vhost.com</td><td>HLS实例：如何禁用HLS。</td>
</tr>
<tr>
<td>RTMP</td><td>min.delay.com</td><td>RTMP最低延迟：如何配置最低延迟的RTMP流。</td>
</tr>
<tr>
<td>RTMP</td><td>refer.anti_suck.com</td><td>Refer实例：如何配置Refer防盗链。</td>
</tr>
<tr>
<td>RTMP</td><td>bandcheck.srs.com</td><td>带宽测试用的vhost，srs测速默认连接到这个vhost。这个vhost配置了带宽测速的key，可测速间隔和最大测速带宽限制。其他Vhost也可以支持测速，只要把这个配置项打开，然后在测速播放器的参数中指明另外的vhost</td>
</tr>
<tr>
<td>RTMP</td><td>removed.vhost.com</td><td>禁用vhost实例：如何禁用vhost。</td>
</tr>
<tr>
<td>Callback</td><td>hooks.callback.vhost.com</td><td>设置http callback的实例，当这些事件发生时，SRS会调用指定的http api。其他Vhost将这些配置打开，就可以支持http callback。</td>
</tr>
<tr>
<td>Transcode</td><td>mirror.transcode.vhost.com</td><td>转码实例：使用ffmpeg的实例filter，将视频做镜像翻转处理。其他Vhost添加这个配置，就可以对流进行转码。<br/>注：所有转码的流都需要重新推送到SRS，使用不同的流名称（vhost和app也可以不一样）。</td>
</tr>
<tr>
<td>Transcode</td><td>drawtext.transcode.vhost.com</td><td>转码实例：在视频上加文字filter。其他Vhost添加此配置，就可以做文字水印。<br/>注：所有转码的流都需要重新推送到SRS，使用不同的流名称（vhost和app也可以不一样）。</td>
</tr>
<tr>
<td>Transcode</td><td>crop.transcode.vhost.com</td><td>转码实例：剪裁视频filter。其他vhost添加此filter，即可对视频进行剪裁。<br/>注：所有转码的流都需要重新推送到SRS，使用不同的流名称（vhost和app也可以不一样）。</td>
</tr>
<tr>
<td>Transcode</td><td>logo.transcode.vhost.com</td><td>转码实例：添加图片/视频水印。其他vhost添加这些配置，可以加图片/视频水印。<br/>注：所有转码的流都需要重新推送到SRS，使用不同的流名称（vhost和app也可以不一样）。</td>
</tr>
<tr>
<td>Transcode</td><td>audio.transcode.vhost.com</td><td>转码实例：只对音频转码。其他vhost添加此配置，可只对音频转码。<br/>注：所有转码的流都需要重新推送到SRS，使用不同的流名称（vhost和app也可以不一样）。</td>
</tr>
<tr>
<td>Transcode</td><td>copy.transcode.vhost.com</td><td>转码实例：只转封装。类似于forward功能。</td>
</tr>
<tr>
<td>Transcode</td><td>all.transcode.vhost.com</td><td>转码实例：对上面的实例的汇总。</td>
</tr>
<tr>
<td>Transcode</td><td>ffempty.transcode.vhost.com</td><td>调用ffempty程序转码，这个只是一个stub，打印出参数而已。用作调试用，看参数是否传递正确。</td>
</tr>
<tr>
<td>Transcode</td><td>app.transcode.vhost.com</td><td>转码实例：只对匹配的app的流进行转码。</td>
</tr>
<tr>
<td>Transcode</td><td>stream.transcode.vhost.com</td><td>转码实例：只对匹配的流进行转码。</td>
</tr>
</tbody>
</table>

SRS的demo.conf配置文件中，包含了demo用到的一些vhost，参考[Usage: Demo](https://github.com/winlinvip/simple-rtmp-server/wiki/v1_EN_SampleDemo)。

<table>
<thead>
<th>Category</th>
<th>Vhost</th>
<th>说明</th>
</thead>
<tbody>
<tr>
<td>DEMO</td><td>players</td><td>srs_player播放的演示流，按照Readme的Step会推流到这个vhost，demo页面打开后播放的流就是这个vhost中的流</td>
</tr>
<tr>
<td>DEMO</td><td>players_pub</td><td>srs编码器推流到players这个vhost，然后转码后将流推送到这个vhost，并切片为hls，srs编码器播放的带字幕的流就是这个vhost的流</td>
</tr>
<tr>
<td>DEMO</td><td>players_pub_rtmp</td><td>srs编码器演示页面中的低延时播放器，播放的就是这个vhost的流，这个vhost关闭了gop cache，关闭了hls，让延时最低（在1秒内）</td>
</tr>
<tr>
<td>DEMO</td><td>demo.srs.com</td><td>srs的演示vhost，Readme的step最后的12路流演示，以及播放器的12路流延时，都是访问的这个vhost。包含了SRS所有的功能。</td>
</tr>
<tr>
<td>Others</td><td>dev</td><td>开发用的，可忽略</td>
</tr>
</tbody>
</table>

Winlin 2014.10