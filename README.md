# Copy the delegate install command and modify it as follows:
Add --net=host to the first line.
Add -e DELEGATE_TAGS="DELEGATE_OS_ARCH", and replace DELEGATE_OS_ARCH with the tag corresponding to your Docker environment's 
architecture: linux-amd64 or linux-arm64.
Here's an example of an install script for Linux arm64:

 Download harness runner and start it
 ```
 chmod 755 drone-docker-runner-linux-amd64
 nohup ./drone-docker-runner-linux-amd64 server > log.txt 2>&1 &
```
```
 docker run  -d --cpus=1 --memory=2g \
  -e DELEGATE_NAME=docker-delegate-gh \
  -e NEXT_GEN="true" \
  -e DELEGATE_TYPE="DOCKER" \
  -e ACCOUNT_ID=EDwM_joHSFK4oz33rVMOeg \
  -e DELEGATE_TOKEN=YWQxZTZhNWY5NzcxZGExNDNkZjQyNGQzMzNiODMwODM= \
  -e DELEGATE_TAGS="linux-amd64" \
  -e RUNNER_URL=http://10.10.1.15:3000 \
  -e LOG_STREAMING_SERVICE_URL=https://app.harness.io/log-service/ \
  -e MANAGER_HOST_AND_PORT=https://app.harness.io harness/delegate:23.12.81808
```

# Configure a Sonar Server locally
```
apt install unzip
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```
## Stage 1 : Select the Build Library "Run Test"
<img width="419" alt="image" src="https://github.com/e5664869/harness-ci/assets/122775400/66e18674-6f8c-4815-a14f-4697f5b2d5da">

## Stage 2: Select the Build Library "Run"
<img width="446" alt="image" src="https://github.com/e5664869/harness-ci/assets/122775400/b41850cf-72c4-41cb-ae2e-8f3ef53076aa">

## Stage 3 : Select the Build Library "Run"
<img width="440" alt="image" src="https://github.com/e5664869/harness-ci/assets/122775400/d344c332-ee86-4499-a926-6b1572badbc0">


