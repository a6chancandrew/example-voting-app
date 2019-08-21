FROM java:openjdk-8-jdk-alpine

RUN MAVEN_VERSION=3.3.3 \
 && cd /usr/share \
 && wget http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz -O - | tar xzf - \
 && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
 && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

WORKDIR /code

ADD pom.xml /code/pom.xml
RUN ["mvn", "dependency:resolve"]
RUN ["mvn", "verify"]

# Adding resource for doing Yourkit java profiling
# ADD YourKit-JavaProfiler-2019.8-docker.zip /tmp/
ADD YourKit-JavaProfiler-2019.8-docker.zip /tmp/
RUN unzip /tmp/YourKit-JavaProfiler-2019.8.zip -d /usr/local && \
  rm /tmp/YourKit-JavaProfiler-2019.8-docker.zip

# add necessary library for yourkit
RUN apk add --no-cache libc6-compat

#expose port to listen on
EXPOSE 10001

# Adding source, compile and package into a fat jar
ADD src/main /code/src/main
RUN ["mvn", "package"]

CMD ["java", "-agentpath:/usr/local/YourKit-JavaProfiler-2019.8/bin/linux-x86-64/libyjpagent.so=port=10001,listen=all,logdir=/tmp/yourkit/", "-jar", "target/worker-jar-with-dependencies.jar"]
