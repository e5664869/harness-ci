# Copy the delegate install command and modify it as follows:
Add --net=host to the first line.
Add -e DELEGATE_TAGS="DELEGATE_OS_ARCH", and replace DELEGATE_OS_ARCH with the tag corresponding to your Docker environment's architecture: linux-amd64 or linux-arm64.
Here's an example of an install script for Linux arm64:

 Download harness runner and start it
 chmod 755 drone-docker-runner-linux-amd64
 nohup ./drone-docker-runner-linux-amd64 server > log.txt 2>&1 &

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

#Configure a Sonar Server locally
apt install unzip
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start


Harness CI YAML
pipeline:
  name: Build java
  identifier: Build_java
  projectIdentifier: default_project
  orgIdentifier: default
  properties:
    ci:
      codebase:
        connectorRef: GitHubSource
        repoName: container-cicd
        build: <+input>
  stages:
    - stage:
        name: Build
        identifier: Build
        description: ""
        type: CI
        spec:
          caching:
            enabled: true
          cloneCodebase: true
          platform:
            os: Linux
            arch: Amd64
          runtime:
            type: Docker
            spec: {}
          execution:
            steps:
              - step:
                  type: RunTests
                  name: Build-Code
                  identifier: RunTests_1
                  spec:
                    language: Java
                    buildTool: Maven
                    args: test
                    packages: io.harness
                    runOnlySelectedTests: true
                    postCommand: mvn package -DskipTests
                    reports:
                      type: JUnit
                      spec:
                        paths:
                          - target/surefire-reports/*.xml
                    enableTestSplitting: false
              - step:
                  type: Run
                  name: CodeQualityCheck
                  identifier: CodeQualityCheck
                  spec:
                    shell: Sh
                    command: |-
                      mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=Harness \
                        -Dsonar.host.url=http://35.232.242.38:9000 \
                        -Dsonar.login=116ecff32df4e3636f3aa8ac8f101e7dfbbfd601
                  description: Code Quality Check using SonarQube
              - step:
                  type: Run
                  name: BuildDockerImage
                  identifier: BuildDockerImage
                  spec:
                    shell: Sh
                    command: docker build --rm=true -f Dockerfile -t ganeshmete11/ultimate-cicd:${BUILD_NUMBER} . --pull=true --label org.opencontainers.image.source=https://github.com/e5664869/container-cicd.git --label org.opencontainers.image.url=https://github.com/e5664869/container-cicd
                    envVariables:
                      BUILD_NUMBER: <+pipeline.sequenceId>
              - step:
                  type: Run
                  name: PushDockerImage
                  identifier: PushDockerImage
                  spec:
                    shell: Sh
                    command: |-
                      docker login -u ganeshmete11@gmail.com -p ${Password}
                      docker push ganeshmete11/ultimate-cicd:${BUILD_NUMBER}
                    envVariables:
                      BUILD_NUMBER: <+pipeline.sequenceId>
                      Password: ******
                  description: Push Docker Image to Docker Hub
              - step:
                  type: Run
                  name: CleanLocalImages
                  identifier: CleanLocalImages
                  spec:
                    shell: Sh
                    command: |
                      docker rmi ganeshmete11/ultimate-cicd:${BUILD_NUMBER}
                    envVariables:
                      BUILD_NUMBER: <+pipeline.sequenceId>
                  description: Clean Local Images


 
  
