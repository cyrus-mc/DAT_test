_Based on that information, what is the rough size, in M of the image created by this Dockerfile? Based on your answer how might you modify the above to create the smallest possible image size?_

When I create the image, the total size is 207MB. If we account for the layer created by the COPY statement, the total image size would be around 407MB.
```java
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
mwpdx_dat                   latest              bbfbd5848f9f        3 minutes ago       207MB
```

To further reduce the image size you could merge all similar instructions together. If there were multiple RUN or COPY statements they could be merged into a single statements like:
```java
RUN yum update \
    && yum upgrade \
    && rm -f /tmp/application.tar.gz \
    && hostname
```

You could also use a *.dockerignore* file to exclude items that won't be included in the build. 
