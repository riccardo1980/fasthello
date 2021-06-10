# FASTHELLO: FastAPI with Docker and WSL2

## WSL2 and Docker configuration
See official Docker How-To: [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/)  
Using the link above, you will get Docker running as a Windows service.  
You can then use Docker CLI directly from within your Linux distro.

## Testing containerized solution
### Image creation & run
You can create the image with the following command, to be issued in WSL2 command prompt:
```Bash
docker build -t fasthello .
```
Again, in WSL2 command prompt, use the following command to run the image.
```Bash
docker run -d --name mycontainer -p 80:80 fasthello
```
Pay attention that, since Docker service is run as Window service, you are exposing FastAPI app on Windows host.

### Accessing containerized B/E from Windows
You can get access from Windows using `localhost` host name.  
For example, to get access using Windows hosted browser, use the following:
```Bash
http://localhost:80/docs
``` 

### Accessing containerized B/E from WSL2
WSL2 is treated as a VM, connected to Windows host using a dedicated network.  
From this point of view, FatAPI application is exposed as a Windows service: to get the IP address of windows host, a number of tricks is available. Most simple one is to ask for IP address of default DNS, from WSL2 prompt:
```Bash
cat /etc/resolv.conf
``` 
You can get access from WSL2 to your Windows host using above found IP address.  
For example, to test mock API exposed by this app, you can define local variable `WINDOWS_HOST_IP` with your collected IP address:
```Bash
export WINDOWS_HOST_IP=<HOST_IP>
curl -X GET "http://$WINDOWS_HOST_IP/items/1" -H  "accept: application/json"
``` 

## Testing process solution
You can launch your application as a process in WSL2 by issuing the following command from whithin `fasthello` folder
```
uvicorn --reload --host=0.0.0.0 app.main:app
```
Check uvicorn logs for exact bind port (usually 8000).

### Accessing running B/E from WSL2
```Bash
curl -X GET "http://127.0.0.1:8000/items/1" -H  "accept: application/json"
``` 

### Accessing running B/E from Windows
You need to find IP of WSL2 virtual machine, as seen by Windows. Open a Power shell and use the following command:
```Powershell
wsl hostname -I
```
Then use your Windows browser and navigate to:
```Bash
http://<WSL2_IP>:8000/docs
``` 
where `<WSL2_IP>` is IP address found with previous command.

#### Powershell: Invoke-WebRequest
```Powershell
Invoke-WebRequest -Uri "http://<WSL2_IP>:8000/items/1" -Method GET -Headers @{"accept"="application/json"}
```
