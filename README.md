# Manifest Trust in IoT Edge v2
This document is verify the on going feature development of Manifest Trust in IoT edge. The goal is sign the deployment manifest and verify if the signatures are valid in the IoT edge device. 

## Generate Certificates and  Sign Deployment Manifest
1. git clone https://github.com/ggjjj/iotedge.git
2. git checkout `origin/ggjjj/verify-twin-signatures`
3. Follow instructions in https://github.com/ggjjj/iotedge/blob/ggjjj/verify-twin-signatures/samples/dotnet/ManifestSignerClient/Readme.md to generate certificates root CA cert and signer certs. If you want to use intermediate certs or skip using the scripts you use the test certs in the shared files via email. It is under `demofiles\test_certs_for_signing_deployment`
4. Open the solution `Microsoft.Azure.Devices.Edge.sln`  in Visual studio and set ManifestSignerClient project as the start project. 
5. Edit the `Properties/launchSettings.json` to update the inputs to the console application of ManifestSignerClient. Use the above Readme.md file to fill the details in the `launchSettings.json` file. 
6. Sample `deployment.json` and  `launchSettings.json` are available in the `ggjjj/manifest-trust github repo`. Sample `deployment.json` points to use the docker containers of edge agent and edge hub which uses the recent changes of manifest trust feature. 
7. On running the sample with right settings, a signed deployment manifest is generated in the location and the file name mentioned in `launchSettings.json`.  

## Deploy signed deployment manifest file to IoT edge 
1. Create an IoT edge device in your IoT hub and get the connection string. No need to use the command `set modules` in IoT hub portal at this stage as we want to deploy the signed deployment using VS code and not using the IoT hub portal.
2. To deploy the signed deployment manifest file, VS code is used. Make sure Azure IoT Edge extension is installed.  
3. Press `Ctrl+Shift+P` Choose `Azure IoT Edge: Create Deployment at Scale` and load the signed deployment manigest that is generated from the manifest signer client. Then give a name to the deployment along with the target condition of `deviceId='<device-name'>` (one c created in step 1. Once the priority options are set the output from the console says: `Deployment with deployment id succeded'.

## Prepare the device to install edge v2 runtime 
1. Please follow the one-note instructions (sent via email) to install v2 runtime. Use the test certs for `aziot-edged-ca` and `aziot-edged-trust-bundle` in the shared files via email. It is under `demofiles\trust_bundle_and_device_ca` and use the `note.txt` for more details. 
2. Make sure the runtime is installed by using the command `sudo systemctl status aziot-{keyd,certd,identityd,edged}`. If the signed deployment is set correctly then there will be docker containers running. Check using the command `docker ps`. 

## Check the logs for feature verification
1. Once the signed deployment is applied, the changes are be see in the logs by `docker logs -f edgeAgent` and `docker logs -f edgeHub`
2. In the logs there will be a specific log saying `Twin signature is verified` when a proper signed deployment is applied or `Twin signature is not verified` when a improper signed deployment is applied. 
