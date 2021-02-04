# FASTHELLO: FastAPI with Docker and WSL2

## WSL2 and Docker configuration
See official Docker How-To: [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/)  
Using the link above, you will get Docker running as a Windows service.  
You can then use Docker CLI directly from within your Linux distro.

## Image creation & run
You can create the image with the following command, to be issued in WSL2 command prompt:
```Bash
docker build -t fasthello .
```

Again, in WSL2 command prompt, use the following command to run the image.
```Bash
docker run -d --name mycontainer -p 80:80 fasthello
```
Pay attention that, since Docker service is run as Window service, you are exposing FastAPI app on Windows host.

## B/E access
Here is the setup:

- WSL2 is treated as a VM, connected to Windows host using a dedicated network. 
- FastAPI application is exposed as a Windows service.

### From Windows
You can get access from Windows using `localhost` host name. For example, to get access using Windows hosted browser, use the following:
```Bash
http://localhost:80/docs
``` 

### From WSL2
To get the IP address of windows host, as seen by WSL2 VM, a number of tricks is available. Most simple one is to ask for IP address of default DNS, from WSL2 prompt:
```Bash
cat /etc/resolv.conf
``` 
You can get access from WSL2 to your Windows host using above found IP address.  
For example, to test mock API exposed by this app, you can define local variable `WINDOWS_HOST_IP` with your collected IP address:
```Bash
export WINDOWS_HOST_IP=<HOST_IP>
curl -X GET "http://$WINDOWS_HOST/items/1" -H  "accept: application/json"
``` 
