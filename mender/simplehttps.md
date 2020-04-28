# Простейший https сервер, только для тестирования

Код сервера взят с [simple-https](https://gist.github.com/dergachev/7028596)

```python
# taken from http://www.piware.de/2011/01/creating-an-https-server-in-python/
# generate server.xml with the following command:
#    openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes
# run as follows:
#    python simple-https-server.py
# then in your browser, visit:
#    https://localhost:4443

import BaseHTTPServer, SimpleHTTPServer
import ssl

httpd = BaseHTTPServer.HTTPServer(('192.168.1.49', 4444), SimpleHTTPServer.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket (httpd.socket, certfile='server.pem', server_side=True)
httpd.serve_forever()
```

Перед запуском необходимо создать файл сертификата server.pem:

```shell
openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes
```

Запускать сервер следует из директории, в которой находятся файлы для раздачи.
