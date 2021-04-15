# blue-green


### blue-green implement for version change 

Initially, it was assumed that the first version of the software was running. To change the version, the image is downloaded and the container is uploaded, then we check the health of the new version with a proxy. If all things good, restart the proxy to revert to the new container.

#### step1:
you must configure inventory files for ansible playbooks
#### step2:
you should run predeploy playbook for dependency tools run command as follow.


```
ansible-playbook -i inventory  predeploy.yaml
```
this playbook install docker on your system and create a network for your enviroment. The variable name is private_net.
If you change this variable's value remember  change for other playbook.
after install if everything was ok config nginx role create a container from nginx image.
the variables for config nginx to proxy to app:
```
 private_net: name of network for dokcer containers.
 nginx_name: name of nginx container.
 app_name: application desire name.
 version: application version.
 nginx_dis_config: /root/nginx/app.conf 
```


#### step3:

you shoud run create_image playbook for creating  image.

```
ansible-playbook -i inventory  create_image.yaml

```
this playbook copy app and build image for apps and login and push in docker Hub.

initialize variables for building image.

```
 path: path for copy app in image.
 version: application version.
 source_path: path app for copy in image. #"/home/reza/Documents/snap_project/apps/{{version}}/"
 full_path: "{{ path }}{{ version }}/"
 source_path: "./"
 app_name: application desire name.
 username: username for login in docker hub website.
 password: password for login in docker hub website.
 private_net: name of network for dokcer containers.
 nginx_name: name of nginx container.
```

#### step4:

finally you run deploy_app playbook for blue-green technique.

this playbook will pull image from docker hub public repository at first, then copy nginx_test template and reload that for test new version change.

```
ansible-playbook -i inventory  deploy_app.yaml

```

health check has been provided by /healthz path. If this path was available with status '200' response, nginx will be reload with new config.  


```
 nginx_test_dis_config: bind mount path for creating test config.
 nginx_dis_config: bind mount for path for creating production config.
 naginx_name: name of nginx container.
 app_name: application desire name.
 version: application version.
 private_net: name of network for dokcer containers.
 username: username for login in docker hub website.
 password: password for login in docker hub website.
 nginx_name: name of nginx container.

 ```


