# Jenkins container image + Configuration as Code

This image provides Jenkins + Jenkins Configuration-as-Code

## Docker compose startup

With this interaction, no secret can be passed to container (so for instance admin password cannot be modified) 

Just issue:

`docker-compose up`

And connect to localhost:8888

## Docker swarm startup

For using secrets, we must use docker swarm.
First issue:

`docker swarm init` (optionally add --advertise-addr)

Then issue:

`docker stack deploy -c docker-compose.yaml jk`

And connect to localhost:8888

## Docker run:

Here some commands for runnig without docker-compose or docker stack deploy

Building image:

`docker build -t andreav/jenkins-ns ./jenkins-image`

And running the conatiner from a bash (mingw)

```
docker run  -it \
            -v jenkins_home:/var/jenkins_home \
            -v '\\.\pipe\docker_engine':'\\.\pipe\docker_engine' \
            -v $(cygpath.exe -w ${PWD})'/jcasc_configs':'/var/jenkins_home/jcasc_configs' \
            -p 8888:8080 \
            -p 55555:50000 \
            andreav/jenkins-ns
```

docker run  -it \
            -v jenkins_home:/var/jenkins_home \
            --privileged \
            -v $(cygpath.exe -w ${PWD})'/jcasc_configs':'/var/jenkins_home/jcasc_configs' \
            -p 8888:8080 \
            -p 55555:50000 \
            andreav/jenkins-ns

# Updating plugins

- Update from UI
- Restart Jenkins (checkbox inside UI)
- dump plugin list by:

    ```
    JENKINS_HOST=admin:passw0rd@localhost:8888
    curl -sSL "http://$JENKINS_HOST/pluginManager/api/xml?depth=1&xpath=/*/*/shortName|/*/*/version&wrapper=plugins" | perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g'|sed 's/ /:/' | sort > plugins_extra.txt
    ```

## JCasC cfg & project layout:

JCasC documentation: https://jenkins.io/projects/jcasc/

And praqma blog: https://github.com/Praqma/praqma-jenkins-casc
