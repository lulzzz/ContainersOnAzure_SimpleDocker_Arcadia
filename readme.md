# A simple intro to Docker

<br><br><br>

## Step 1 - Install Docker:

This has been completed in advance.  Access your 'Docker' VM using Guacamole.

<br>

Images key:

| **Line Colour** | **What is it showing?** |
| :-------- | :------ |
| Purple | The path of your access to various machines |
| Black | Console commands and locations |
| Dark Blue | Docker control plane |
| Green | Docker actions/events |
| Red | Standard 'Data Plane' comms |
| Yellow | Network (logical) |

Docker-specific key:

| **Character** | **Item** |
| :-------- | :------ |
| "i:" | Image |
| "c:" | Container (from image) |
| "l:" | Writeable union filesystem *layer* atop image |

<br>

![Step 1 Image](images/SimpleDocker_Step1.jpg?raw=true)

<br><br>

## Step 2 - Run a container using the 'Hello World' image:

```
sudo docker run hello-world
```

<br>

Note the "Unable to find image 'hello-world:latest' locally" and the subsequent "Pulling" message.

<br>

![Step 2 Image](images/SimpleDocker_Step2.jpg?raw=true)

<br><br>

## Step 3 - Pull down an image from Docker Hub before running a container:

```
sudo docker pull ubuntu:latest
```

<br>

![Step 3 Image](images/SimpleDocker_Step3.jpg?raw=true)

<br><br>

## Step 4 - Run a container based on the image pulled down in Step 3 and attach:

```
sudo docker run -it ubuntu:latest /bin/bash
```

<br>

![Step 4 Image](images/SimpleDocker_Step4.jpg?raw=true)

<br><br>

## Step 5 - Exit the container and clear up:

```
exit
```

or

<br>

'Ctrl+d'

<br><br>

```
sudo docker ps -a
```

```
sudo docker rm <idofcontainercopiedfromlastoutput>
```

<br><br>

## Step 6 - Run another container based on the image pulled down in Step 3, attach to it, install Nginx, test the service, and exit:

On your 'Docker VM':

```
ifconfig -a
```

Note down the IP address on 'Eth0'.

<br><br>

```
sudo docker run -it -p 80:80 --net=host ubuntu /bin/bash
```

<br>

![Step 6 Image a](images/SimpleDocker_Step6a.jpg?raw=true)

<br>

On the container:

```
apt-get update
```

```
apt-get install nano -y
```

```
apt-get install nginx -y
```

```
nano /var/www/html/index.nginx-debian.html
```

Edit the html file in any way you see fit

<br>

'Ctrl+o', 'Ctrl+x' to save and exit nano

<br>

Start nginx:

```
/etc/init.d/nginx start
```

<br>

![Step 6 Image b](images/SimpleDocker_Step6b.jpg?raw=true)

<br>

On your 'Mgmt VM' (Windows), browse to:

```
http://<dockervmipadress>
```

You should see an Nginx welcome page with your edits.

<br>

![Step 6 Image c](images/SimpleDocker_Step6c.jpg?raw=true)

<br><br>

## Step 7 - Build a new image based on the addition of a new layer that was added in Step 6 atop the original 'Ubuntu' image:

Back on the 'Docker VM':

```
sudo docker ps -a
```

Create a new image based on the ubuntu image + the changes made:

```
sudo docker commit <idofcontainerlistedinthelastoutput> <yourfirstname>/nginx
```

<br>

![Step 7 Image a](images/SimpleDocker_Step7a.jpg?raw=true)

<br>

Remove the container that we used to build a new image:

```
sudo docker rm <idofcontainerlistedinthelastoutput>
```

Run a new container based on the image created above and start the nginx service in the background:

```
sudo docker run -d -p 80:80 --net=host <yourfirstname>/nginx nginx -g 'daemon off;'
```
<br>

See the '-d' switch which runs the container in the background (i.e. you'll remain at the prompt provided by your 'Docker VM' after seeing an ID printed to screen which is the new container's ID).

<br>

![Step 7 Image b](images/SimpleDocker_Step7b.jpg?raw=true)

<br><br>

On 'Mgmt VM', browse to:

```
http://<dockervmipadress>
```

You should see the page that you edited again.

Note. The new image is currently only stored locally as it's not been pushed to a registry such as Docker Hub or Azure Container Registry.  This would mean a lack of 'portability'.

<br>

![Step 7 Image c](images/SimpleDocker_Step7c.jpg?raw=true)

<br><br>

Clear up:
```
sudo docker ps
```

```
sudo docker stop <idofcontainerlistedinthelastoutput>
```

```
sudo docker rm <idofcontainerlistedinthelastoutput>
```

<br><br>

## Step 8 - Inspect the layers of our image:

```
sudo docker history <yourfirstname>/nginx
```

See the /bin/bash layer at the top that's added ~96.5MB.

<br><br>

## Step 9 - <i>Declaratively template</i> the process that we've just run through using a 'Dockerfile':

On your 'Docker VM':

```
sudo mkdir nginxdockerfile
```

```
cd nginxdockerfile
```

```
sudo nano Dockerfile
```

Type in:

```

FROM ubuntu 

RUN apt-get update

RUN apt-get install -y nginx

RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

CMD ["nginx"]

EXPOSE 80

```

'Ctrl+o', 'Ctrl+x' to save and exit.

<br><br>

Perform a build using the Dockerfile just created:

```
sudo docker build -t <yourfirstname>/nginx2 .
```

<br><br>

Run a container using the new image:

```
sudo docker run -d -p 80:80 --net=host <yourfirstname>/nginx2
```

<br>

Test on 'Mgmt VM' by browsing to:

```
http://<dockervmipadress>
```


<br><br>

## Step 10 - Declaratively template a multi-container template using Docker Compose

Richard to demonstrate.
