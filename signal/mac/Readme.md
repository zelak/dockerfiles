## build dockerfile
`docker build -t signal:0.0.1 signal/mac`
## run container
`docker run --name signal -it -p 5002:22/tcp -v ${PWD}:/root/host signal:0.0.1`
## build signal server

Get into Signal-server folder and run the following build command in container's shell.

`mvn clean install -DskipTests`