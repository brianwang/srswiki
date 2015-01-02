# Security

SRS提供了禁用或允许客户端的简单安全策略。

## Config

Vhost中安全策略的配置：

```
# the security to allow or deny clients.
vhost security.srs.com {
    # security for host to allow or deny clients.
    # @see https://github.com/winlinvip/simple-rtmp-server/issues/211   
    security {
        # whether enable the security for vhost.
        # default: off
        enabled         on;
        # the security list, each item format as:
        #       allow|deny    publish|play    all|<ip>
        # for example:
        #       allow           publish     all;
        #       deny            publish     all;
        #       allow           publish     127.0.0.1;
        #       deny            publish     127.0.0.1;
        #       allow           play        all;
        #       deny            play        all;
        #       allow           play        127.0.0.1;
        #       deny            play        127.0.0.1;
        # SRS apply the following simple strategies one by one:
        #       1. allow all if security disabled.
        #       2. default to deny all when security enabled.
        #       3. allow if matches allow strategy.
        #       4. deny if matches deny strategy.
        allow           play        all;
        allow           publish     all;
    }
}
```

SRS应用安全策略的方式是:

* 若securty没有开启，则允许所有。
* 若security开启了，默认禁止所有。
* 允许客户端，若找到了匹配的允许策略。
* 禁用客户端，若找到了匹配的禁用策略。

## Bug

关于这个功能的Bug，参考：[#211](https://github.com/winlinvip/simple-rtmp-server/issues/211)

## Reload

当Reload改变security配置后，只影响新连接的客户端，已经连接的客户端不受影响。

Winlin 2015.1