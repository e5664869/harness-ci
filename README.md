# Copy the delegate install command and modify it as follows:
Add --net=host to the first line.
Add -e DELEGATE_TAGS="DELEGATE_OS_ARCH", and replace DELEGATE_OS_ARCH with the tag corresponding to your Docker environment's architecture: linux-amd64 or linux-arm64.
Here's an example of an install script for Linux arm64:

docker run  --cpus=1 --memory=2g \
  -e DELEGATE_NAME=docker-delegate-ci \
  -e NEXT_GEN="true" \
  -e DELEGATE_TYPE="DOCKER" \
  -e ACCOUNT_ID=EDwM_joHSFK4oz33rVMOeg \
  -e DELEGATE_TOKEN=YWQxZTZhNWY5NzcxZGExNDNkZjQyNGQzMzNiODMwODM= \
  -e DELEGATE_TAGS="linux-arm64" \
  -e LOG_STREAMING_SERVICE_URL=https://app.harness.io/log-service/ \
  -e MANAGER_HOST_AND_PORT=https://app.harness.io harness/delegate:23.12.81808
