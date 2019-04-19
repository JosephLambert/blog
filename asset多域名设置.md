### asset多域名设置

nginx.conf

```ruby
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes 2;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;



    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    gzip  on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 5;
    gzip_types text/css text/html application/x-javascript application/javascript text/javascript application/json;
    gzip_buffers 16 8k;

    client_max_body_size 20m;

    upstream app {
        server localhost:3000 fail_timeout=0s;
    }

    server {
       listen 80;
       server_name asset0.example.com asset1.example.com asset2.example.com asset3.example.com asset4.example.com asset5.example.com;

       location ~* \.(jpg|png|jpeg|gif|js|css)$ {
            root /var/www/current/public;
            expires max;
        }

        location / {
            rewrite ^(.*)$ http://www.example.com$1 permanent;
        }
    }


    server {
       listen 80;
       server_name www.example.com;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        root /var/www/current/public;

        # try_files will find the $uri in the given file list, if not found it will hand it to @app handler
        try_files $uri/index.html $uri @app;

        location ~* \.(xml|txt|html|htm|ico|css|js|gif|jpe?g|png)(\?[0-9]+)?$ {
            expires max;
            try_files $uri/index.html $uri @app;
        }

        location @app {
            proxy_set_header   Host $host;
            proxy_set_header   X-Forwarded-Host $host;
            proxy_set_header   X-Forwarded-Server $host;
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_buffering    on;
            proxy_pass http://app;
       }
    }
}
```

app/controllers/concerns/asset_url_generator.rb

```ruby
module Concerns
  module AssetUrlGenerator

    def self.included(base)
      base.send :extend, ClassMethods

      base.class_eval do
        helper_method :gen_asset_abs_url

        ActionController::Base.asset_host = Proc.new { |source| gen_asset_host(source) }
      end
    end

    private
    def gen_asset_abs_url asset_relative_path
      asset_relative_path = asset_relative_path.to_s

      if Rails.env.production? or Rails.env.staging?
        "//#{self.class.gen_asset_host(asset_relative_path)}#{asset_relative_path}"
      else
        asset_relative_path
      end
    end

    module ClassMethods
      def gen_asset_host asset_relative_path
        path_md5 = Digest::MD5.hexdigest(asset_relative_path).to_s
        asset_host = case Rails.env.to_s
        when 'production'
          AppConfig.assets.host % (path_md5[0].ord % AppConfig.assets.host_counter.to_i + 1)
        when 'staging'
          AppConfig.assets.host # staging images url test
        else
          nil
        end

        asset_host
      end
    end

  end

end
```

application_controller.rb

```ruby
# 这里也可以直接放在ApplicationController中
class HomeController
  include Concerns::AssetUrlGenerator
end
```

