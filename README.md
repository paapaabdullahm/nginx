# Nginx

Nginx is an open source reverse proxy server for HTTP, HTTPS, SMTP, POP3, and IMAP protocols, as well as a Load Balancer, HTTP cache, Web Server, etc.

Nginx version:  **`v1.16.*`**

## Usage

* #### Hosting some simple static content

  ```shell
  $ docker run --name nginx \
    -v /some/content:/usr/share/nginx/html:ro \
    -d pam79/nginx
  ```

* #### Exposing external port

  ```shell
  $ docker run --name nginx \
    -v /some/content:/usr/share/nginx/html:ro \
    -p 8080:80 \
    -d pam79/nginx
  ```

  Then you can hit `http://localhost:8080` or `http://host-ip:8080` in your browser.

* #### Complex configuration
  ```shell
  $ docker run --name nginx \
    -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro \
    -d pam79/nginx
  ```

* #### Using environment variables in nginx configuration

  > Out-of-the-box, nginx doesn't support environment variables inside most configuration blocks. But `envsubst`may be used as a workaround if you need to generate your nginx configuration dynamically before nginx starts.

  ```shell
  web:
    image: pam79/nginx
    volumes:
     - ./mysite.template:/etc/nginx/conf.d/mysite.template
    ports:
     - "8080:80"
    environment:
     - NGINX_HOST=foobar.com
     - NGINX_PORT=80
    command: /bin/bash -c "envsubst < /etc/nginx/conf.d/mysite.template > /etc/nginx/conf.d/default.conf && exec nginx -g 'daemon off;'"
  ```

  The `mysite.template` file may then contain variable references like this:

  `listen ${NGINX_PORT};`

* #### Running nginx in read-only mode

  ```shell
  $ docker run -d -p 80:80 --read-only 
    -v $(pwd)/nginx-cache:/var/cache/nginx 
    -v $(pwd)/nginx-pid:/var/run pam79/nginx
  ```

* #### Running nginx in debug mode

  ```shell
  $ docker run --name nginx \
    -v /host/path/nginx.conf:/etc/nginx/nginx.conf:ro \
    -d pam79/nginx nginx-debug -g 'daemon off;'
  ```

  Similar configuration in docker-compose.yml may look like this:
  ```shell
  web:
    image: pam79/nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    command: [nginx-debug, '-g', 'daemon off;']
  ```

* #### Monitoring nginx with Amplify

  [Amplify](https://amplify.nginx.com/signup/) is a free monitoring tool that can be used to monitor microservice architectures based on nginx. Amplify is developed and maintained by the company behind the nginx software.

  With Amplify it is possible to collect and aggregate metrics across containers, and present a coherent set of visualizations of the key performance data, such as active connections or requests per second. It is also easy to quickly check for any performance degradations, traffic anomalies, and get a deeper insight into the nginx configuration in general.

  In order to use Amplify, a small Python-based agent software (Amplify Agent) should be [installed](https://github.com/nginxinc/docker-nginx-amplify) inside the container.

  For more information about Amplify, please check the official documentation [here](https://github.com/nginxinc/nginx-amplify-doc).
