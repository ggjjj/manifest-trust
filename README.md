# Manifest Trust in IoT Edge
This document is verify the on going feature development of Manifest Trust in IoT edge. The goal is sign the deployment manifest and verify if the signatures are valid in the IoT edge device. 

## Generate Certificates and  Sign Deployment Manifest
1. git clone https://github.com/ggjjj/iotedge.git
2. git checkout `origin/ggjjj/verify-twin-signatures`
3. Follow instructions in https://github.com/ggjjj/iotedge/blob/ggjjj/verify-twin-signatures/samples/dotnet/ManifestSignerClient/Readme.md to generate certificates
4. Open the solution `Microsoft.Azure.Devices.Edge.sln`  in Visual studio and set ManifestSignerClient project as the start project. 
5. Edit the `Properties/launchSettings.json` to update the inputs to the console application of ManifestSignerClient.
6. Sample `deployment.json` and  `launchSettings.json` are available in the `ggjjj/manifest-trust github repo`. Sample `deployment.json` points to use the docker containers of edge agent and edge hub which uses the recent changes of manifest trust feature. 
7. On running the sample with right settings, a signed `deployment.json` is generated. 

## Deploy signed deployment manifest file to IoT edge
1. Create an IoT edge device via https://docs.microsoft.com/en-us/azure/iot-edge/quickstart-linux?view=iotedge-2018-06 
2. To deploy the signed deployment manifest file, az cli tools are needed. Install them by https://github.com/Azure/azure-iot-cli-extension
3. Create a deployment  `az iot edge deployment create -d manifest-trust-deployment -n test-iot-hub --content sample-deployment-signed.json`. This create a deployment and does not apply this to the IoT edge device yet. 
4. To apply to the IoT edge device created in step 1, in the azure portal under IoT edge deployment, click the deployment and update the target conditions to `deviceid='<iot-edge-device-name>'`. 
5. Once the signed deployment is applied, the changes are be see in the logs by `docker logs -f edgeAgent` and `docker logs -f edgeHub`
6. In the logs there will be a specific log saying `Twin signature is verified` or `Twin signature is not verified` 
