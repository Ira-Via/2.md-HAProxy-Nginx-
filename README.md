# 2.md-HAProxy-Nginx-  
«Кластеризация и балансировка нагрузки» Васяева Ирина  
# Задание 1  
1. Запуск простых Python серверов  
`python3 --version`  
2. Создание директории для серверов.  
```bash
mkdir simple_servers
cd simple_servers
```
3. Создание первого сервера (server1.py)  
```python
   # server1.py
   from http.server import BaseHTTPRequestHandler, HTTPServer

   class SimpleHandler(BaseHTTPRequestHandler):
       def do_GET(self):
           self.send_response(200)
           self.send_header("Content-type", "text/html")
           self.end_headers()
           self.wfile.write(b"Hello from Server 1")

   def run(server_class=HTTPServer, handler_class=SimpleHandler, port=8001):
       server_address = ('', port)
       httpd = server_class(server_address, handler_class)
       print(f'Starting server on port {port}...')
       httpd.serve_forever()

   if __name__ == "__main__":
       run()
```
4. Создание второго сервера (server2.py)  
```python
   # server2.py
   from http.server import BaseHTTPRequestHandler, HTTPServer

   class SimpleHandler(BaseHTTPRequestHandler):
       def do_GET(self):
           self.send_response(200)
           self.send_header("Content-type", "text/html")
           self.end_headers()
           self.wfile.write(b"Hello from Server 2")

   def run(server_class=HTTPServer, handler_class=SimpleHandler, port=8002):
       server_address = ('', port)
       httpd = server_class(server_address, handler_class)
       print(f'Starting server on port {port}...')
       httpd.serve_forever()

   if __name__ == "__main__":
       run()
```
5. Запуск сервера в двух разных терминалах:  
`python3 server1.py/server2.py`  
6. Установка HAProxy  
```bash
sudo apt update
sudo apt install haproxy
```
7. Настройка HAProxy  
`sudo nano /etc/haproxy/haproxy.cfg`
```plaintext
global
       log /dev/log   local0
       log /dev/log   local1 notice
       chroot /var/lib/haproxy
       stats socket /run/haproxy/admin.sock mode 660 level admin
       stats timeout 30s
       user haproxy
       group haproxy
       daemon

   defaults
       log     global
       option  tcplog
       timeout connect 5000ms
       timeout client  50000ms
       timeout server  50000ms

   frontend http_front
       bind *:8000
       default_backend http_back

   backend http_back
       balance roundrobin
       server server1 127.0.0.1:8001 check
       server server2 127.0.0.1:8002 check
```
8. Перезапуск HAProxy для применения изменений  
`sudo systemctl restart haproxy`  
9. Проверка настройки  
`curl http://localhost:8000`  
# Задание 2

