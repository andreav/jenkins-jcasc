# Jenkins container image + Configuration as Code

This image provides Jenkins + Jenkins Configuration-as-Code

## Docker compose (secrets support)

With this interaction, no secret can be passed to container (so for instance admin password cannot be modified) 

Just issue:

`docker-compose up`

And connect to localhost:8888

## Docker run (secrets not supported)

Here some commands for runnig without docker-compose or docker stack deploy

Building image:

`docker build -t andreav/jenkins-jcasc ./jenkins-image`

And running the conatiner from a bash (mingw)
- note the double slash in front of '//var/run/docekr.sock' mandatory from inside mingw
- '--group-add 0' for letting jenkins user access /var/run/docker.sock

```
docker run  -it \
            -v jenkins_home:/var/jenkins_home \
            -v $(cygpath.exe -w ${PWD})'/jcasc_configs':'/var/jenkins_home/jcasc_configs' \
            -p 8888:8080 \
            -p 55555:50000 \
            -v "//var/run/docker.sock:/var/run/docker.sock" \
            --group-add 0 \
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


## Extra - Monitoring containers

Using portainer is a nice way to monitor containers.
Again, not the double slash in front of '//var/run/docekr.sock' mandatory from inside mingw

``` bash
docker run -d -p 9000:9000 --name portainer -v "//var/run/docker.sock:/var/run/docker.sock" portainer/portainer
```
