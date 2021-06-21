# FASTHELLO: FastAPI with Docker and WSL2

## WSL2 and Docker configuration
See official Docker How-To: [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/)  
Using the link above, you will get Docker running as a Windows service.  
You can then use Docker CLI directly from within your Linux distro.

## Testing process solution
You can launch your application as a process in WSL2 by issuing the following command from whithin `fasthello` folder
```
uvicorn --reload --host=0.0.0.0 --port=8080 app.main:app
```

### Accessing running B/E from WSL2
```Bash
curl -X GET "http://localhost:8080/items/1" -H  "accept: application/json"
``` 

### Accessing running B/E from Windows
Default WSL2 configuration settings enable port forwarding to host:
- see [here](https://docs.microsoft.com/en-us/windows/wsl/compare-versions#accessing-linux-networking-apps-from-windows-localhost) for supported Windows versions
- see [here](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#wsl-2-settings) `localhostForwarding` key.

So unless explicitly configured as `false` your process hould be accessible from Windows browser at:
```Bash
http://localhost:8080
```
#### Powershell: Invoke-WebRequest
```Powershell
Invoke-WebRequest -Uri "http://localhost:8080/items/1" -Method GET -Headers @{"accept"="application/json"}
```

### Accessing running B/E from Windows whend no forwarding is configured
You need to find IP of WSL2 virtual machine, as seen by Windows. Open a Power shell and use the following command:
```Powershell
wsl hostname -I
```
Then use your Windows browser and navigate to:
```Bash
http://<WSL2_IP>:8080/docs
``` 
where `<WSL2_IP>` is IP address found with previous command.

#### Powershell: Invoke-WebRequest
```Powershell
Invoke-WebRequest -Uri "http://<WSL2_IP>:8080/items/1" -Method GET -Headers @{"accept"="application/json"}
```

## Testing containerized solution
### Image creation & run
You can create the image with the following command, to be issued in WSL2 command prompt:
```Bash
docker build -t fasthello .
```
Again, in WSL2 command prompt, use the following command to run the image.
```Bash
docker run -d --name mycontainer -p 8080:80 fasthello
```
Pay attention that, since Docker service is run as Window service, you are exposing FastAPI app on Windows host.

### Accessing containerized B/E from Windows
You can get access from Windows using `localhost` host name.  
For example, to get access using Windows hosted browser, use the following:
```Bash
http://localhost:8080/docs
``` 

### Accessing containerized B/E from WSL2
Since Docker-desktop 2.3 you can:
- activate WSL 2 based engine under "General" -> "Use the WSL2 based engine"
- enable WSL 2 integration under "Resources" -> "WSL integration" -> check all the distros you want to use.

You should reach containerized B/E using `localhost`, as in:
```Bash
curl -X GET "http://localhost:8080/items/1" -H  "accept: application/json"
``` 

### Accessing containerized B/E from WSL2 using Windows IP
WSL2 is treated as a VM, connected to Windows host using a dedicated network.  
From this point of view, FatAPI application is exposed as a Windows service: to get the IP address of windows host, a number of tricks is available. Most simple one is to ask for IP address of default DNS, from WSL2 prompt:
```Bash
cat /etc/resolv.conf
``` 
You can get access from WSL2 to your Windows host using above found IP address.  
For example, to test mock API exposed by this app, you can define local variable `WINDOWS_HOST_IP` with your collected IP address:
```Bash
export WINDOWS_HOST_IP=<HOST_IP>
curl -X GET "http://$WINDOWS_HOST_IP:8080/items/1" -H  "accept: application/json"
``` 
