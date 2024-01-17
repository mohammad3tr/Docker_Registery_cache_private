## Docker-Registry by using letsencrypt CA as private & cache registry



## Server side Config :
```
1- 
sudo apt-get update
sudo apt-get install certbot
sudo apt-get install apache2-utils
2-
sudo certbot certonly --standalone -d <your domain address>
3- 
ls  /etc/letsencrypt/live/
ls /etc/letsencrypt/archive/

4-
cd Docker-Registery
5-
mkdir certs
6-
#cp fullchain1.pem  privkey1.pem in certs directory and be careful check the excat pem and priatekey files
7-
htpasswd -Bbn <Your UserName> > < Your Password> htpasswd # encrypt your password via bcrypt algorithm 

#If you no need to authentication you can comment these lines in docker-compose:
      - ./htpasswd:/auth/htpasswd
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Real

8-
 docker-compose up -d

```


## Client side config :


***To use the cache_registry for pulling images***
```

1- 
touch  /etc/docker/daemon.json
2-
 vim  /etc/docker/daemon.json

{
    "registry-mirrors": ["https://<your repository URL>"],
    "log-opts": {
        "max-size": "100m"
    }
}

2-
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```
***To use the private_registry to push  and pull your images***
```
3-
docker login -u <"your username"> -p <"your password"> <your repository URL>:7000

#You will probably get something like this :

WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/mohammad/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

4-
docker tag <your image:tag> <your repository URL>:7000/<your image>:tag
#Example: docker tag m3tr-jenkins:latest registry.a.com:7000/m3tr-jenkins:latest

5-
docker push <your repository URL>:7000/m3tr-jenkins:latest
#Example: docker push registry.a.com:7000/m3tr-jenkins:latest

6-
docker pull <your repository URL>:7000/m3tr-jenkins:latest
#Example: docker pull registry.a.com:7000/m3tr-jenkins:latest

```

