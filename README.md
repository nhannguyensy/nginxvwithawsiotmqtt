Problem :

you can not configure http to reverse proxy for aws iot mqtt , nginx would return these error:
software.amazon.awssdk.crt.mqtt.MqttException: socket is closed.
at software.amazon.awssdk.crt.mqtt.MqttClientConnection.onConnectionComplete(MqttClientConnection.java:140)

Resolved :

You must use nginx module: ngx_stream_proxy_module ( like network load balancer) this is a template to resolve :

  Stream{
        map $ssl_preread_server_name $domain {
        stg-iot.yourdomain  stg-iot;
        iot.yourdomain prod-iot;
        }

        upstream stg-iot {
                server IoT-xxxxxx..ap-your_region-1.amazonaws.com:443;
        }

       upstream prod-iot {
                server IoT-xxxxxx..ap-your_region-1.amazonaws.com:443;
        }
      map $ssl_server_name $targetCert {
        stg-iot.yourdomain /etc/nginx/ssl/star_yourdomain.crt;
        iot.yourdomain /etc/nginx/ssl/star_yourdomain.crt;
  }

    map $ssl_server_name $targetCertKey {
      stg-iot.yourdomain /etc/nginx/ssl/star_yourdomain.key;
      iot.yourdomain /etc/nginx/ssl/star_yourdomain.key;
  }

        server {
                listen 443;
                ssl_certificate     $targetCert;
                ssl_certificate_key $targetCertKey;
                proxy_pass $domain;
                ssl_preread on;
        }
} 


https://www.0937686468.com/2022/09/nginx-reverse-proxy-for-aws-iot-mqtt.html
