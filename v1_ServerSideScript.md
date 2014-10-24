# 服务器端开发脚本

SRS不支持服务器端脚本，所谓服务器端脚本，指的是服务器可以加载外部脚本文件，解释并执行。

支持服务器脚本的服务器有FMS，语言是actionscript1.0；nginx支持的是lua。

SRS不支持服务器脚本的原因有：
* 不Simple：违反了SRS(Simple RTMP Server)的第一个S，支持扩展脚本，出错的几率也扩展了。
* 实际用处很小：我在国内知名的CDN公司工作时，所在部门就是用FMS，当然FMS不提供源码，所以只能支持服务器脚本来定制。结果商用起来很费劲，基本上每天出问题，而且还没法查原因。所以实际的用处很小。
* SRS支持HTTP调用：调用外部http，实际上也是一种扩展方式，SRS支持这种较好的方式。譬如当用户连接上SRS时，会调用HTTP接口，可以做验证。
* SRS开源：为何要定制脚本？重要的一个原因就是闭源，SRS开源，可以修改源码。
* SRS代码定制简单：SRS整个服务器实现代码才2万行，nginx-rtmp是3万行+nginx的14万行，定制SRS要简单很多。而且SRS是“同步”处理的，逻辑很少。

综上所述，SRS暂时不考虑支持扩展脚本，这个东西没啥用。

Winlin 2014.2