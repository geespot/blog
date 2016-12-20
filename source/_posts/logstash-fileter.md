---
title: logstash_fileter
date: 2016-12-05 20:45:53
tags:
- 技术
categories: [linux,数据库,开发]
---

## Logstash插件配置（ELK）


官方插件参考
[plugin](https://www.elastic.co/guide/en/logstash/5.0/filter-plugins.html)


### 以下是生产环境的一个过滤配置：
涉及到 geoip 配置和 useragent配置:
*  5.0以前版本配置：database => "/srv/elk/server/GeoLiteCity.dat"
*  5.0之后配置：    database => "/opt/pkgs/GeoLite2-City.mmdb"
### 对于最新的data下载：

geoip数据网站：[data](http://dev.maxmind.com/geoip/geoip2/geolite2/)
下载数据：
[city](http://geolite.maxmind.com/download/geoip/database/GeoLite2-City.mmdb.gz)
[Country](http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.mmdb.gz)

```conf
input {
    redis{
                host => "127.0.0.1"
                data_type => "list"
                key => "logstash"
        }
}

filter {
    grok {
        match => ["message", "%{IP:client_ip},,-,,\[%{HTTPDATE:timestamp}\],,\"(?:%{WORD:verb} %{NOTSPACE:request}(?: HTTP/%{NUMBER:http_version}))\",,%{NUMBER:status:int},,%{NUMBER:bytes:float},,%{QS:referrer},,%{QS:agent},,\"%{IP:upstream_host}\",,\"(%{NUMBER:upstream_time:float})\",,\"(%{NUMBER:request_time:float})\",,(%{NUMBER:request_size:float}),,%{QS:login_id}"]
        }

      ruby {
           code => "event['@timestamp'] = event['@timestamp'].getlocal"
           #code => "event['@timestamp'] = event['@timestamp'].localtime('+08:00')"
           #code => "event['@timestamp'] = event['@timestamp'].local('-08:00')"
           #code => "event['@timestamp'] = LogStash::Timestamp.new( Time.at( event['@timestamp'].to_i() ).getlocal())"
}

    useragent {
        source => "agent"
        target => "brower"
   }

    geoip {
        source => "upstream_host"
        target => "geoip"
        database => "/opt/pkgs/GeoLite2-City.mmdb"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
    }


    mutate {
        convert => [ "[geoip][coordinates]", "float"]
    }
}

output {
 elasticsearch {
 hosts => ["10.57.112.126:9200"]
 }
}

output {
  elasticsearch {
    hosts => ["10.70.14.144:9200"]
    index => "shgt-nginx-%{+YYYY.MM.dd}"
  }
}
```   
