# Jenkins container image + Configuration as Code

This image provides Jenkins + Jenkins Configuration-as-Code

## Docker compose (secrets support)

With docker compose secrets can be passed to container thus creating credentials from configuration.

As an example, one username/password and one file credentilas will be created.

Just issue:

`docker-compose up`

And connect to localhost:8888

## Docker run (no secret support)

With docker run there is no secret support.

Building image:

`docker build -t andreav/jenkins-jcasc ./jenkins-image`

And running the conatiner from a bash (mingw)
- note the double slash in front of '//var/run/docekr.sock' mandatory from inside mingw

```
docker run  -it \
            -v jenkins_home:/var/jenkins_home \
            -v $(cygpath.exe -w ${PWD})'/jcasc_configs':'/var/jenkins_home/jcasc_configs' \
            -p 8888:8080 \
            -p 55555:50000 \
            -v "//var/run/docker.sock:/var/run/docker.sock" \
            --name jenkins \
            andreav/jenkins-jcasc
```

# Updating plugins

- Update from UI
- Restart Jenkins (checkbox inside UI)
- dump plugin list by:

    ```
    JENKINS_HOST=admin:passw0rd@localhost:8888
    curl -sSL "http://$JENKINS_HOST/pluginManager/api/xml?depth=1&xpath=/*/*/shortName|/*/*/version&wrapper=plugins" | perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g'|sed 's/ /:/' | sort > jenkins-image/plugins_extra.txt
    ```

## JCasC cfg & project layout:

JCasC documentation: https://jenkins.io/projects/jcasc/

And praqma blog: https://github.com/Praqma/praqma-jenkins-casc

## Secuity

In order to use docker in docker, jenkins user is added to admin group.

Comment this line inside docker file if you do not need docker in docker support.

```
RUN usermod -a -G 0 jenkins
```


## Extra - Monitoring containers

Using portainer is a nice way to monitor containers.
Again, not the double slash in front of '//var/run/docekr.sock' mandatory from inside mingw

``` bash
docker run -d -p 9000:9000 --name portainer -v "//var/run/docker.sock:/var/run/docker.sock" portainer/portainer
```
