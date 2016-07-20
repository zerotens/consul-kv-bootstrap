# consul-kv-bootstrap

A simple cli tool to bootstrap / initialize the Consul Key Value Database from a YAML file.

## Usage

```bash
CONSUL_HTTP_ADDR="127.0.0.1:8500" ./consul-kv-bootstrap -f app.yml
```
Commandline options:

* `-file` `-f`: YAML file to import into Consul Key Value database
* `-prefix` `-p`: A Prefix for the imported Keys from the YAML file (example: /service/nginx/)

Supported Consul Connection Environment options:

* `CONSUL_HTTP_ADDR`
* `CONSUL_HTTP_TOKEN`
* `CONSUL_HTTP_AUTH`
* `CONSUL_HTTP_SSL`
* `CONSUL_HTTP_SSL_VERIFY`

## Usage Examples

nginx.yaml
```yaml
nginx:
  client_body_buffer_size: 1024k
  virtual_hosts:
    vhost1:
      hostname: www.example.tld
      ssl: true
      ssl_certificate:
        - ./ssl/www.example.tld.crt
        - ./ssl/sub_chain.crt
        - ./ssl/ca.crt
      ssl_key:
        - ./ssl/www.example.tld.key
      upstream: backend:8080
    vhost2:
      hostname: www.example.tld
      upstream: backend:8080
```

Bootstraping nginx.yaml with a prefix /services/test/
```bash
./consul-kv-bootstrap -f nginx.yml -p /services/test/
```
Consul Database Result:

```bash
/services/test/nginx/client_body_buffer_size => 1024k
/services/test/nginx/virtual_hosts/vhost1/hostname => www.example.tld
/services/test/nginx/virtual_hosts/vhost1/ssl => true
/services/test/nginx/virtual_hosts/vhost1/ssl_certificate => ... (see below)
/services/test/nginx/virtual_hosts/vhost1/ssl_key => ... (see below)
/services/test/nginx/virtual_hosts/vhost1/upstream => backend:8080
/services/test/nginx/virtual_hosts/vhost2/hostname => www.example.tld
/services/test/nginx/virtual_hosts/vhost2/upstream => backend:8080
```

YAML Lists are used as concatenated file includes.

The resulting value of
```bash
/services/test/nginx/virtual_hosts/vhost1/ssl_certificate
```
would be like using cat to concatenate the 3 files
```bash
cat ./ssl/www.example.tld.crt ./ssl/sub_chain.crt ./ssl/ca.crt
```


## License

Copyright (c) 2016 The consul-kv-bootstrap Author

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE. OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
