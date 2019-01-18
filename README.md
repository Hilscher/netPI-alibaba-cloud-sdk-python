## Accessing  Alibaba Cloud IoT Platform with Python sample

Made for [netPI](https://www.netiot.com/netpi/), the Raspberry Pi 3B Architecture based industrial suited Open Edge Connectivity Ecosystem

### Debian with Alibaba Cloud Python SDK, Python sample, SSH server and user root

The image provided hereunder deploys a container with installed Python SDK for Alibaba Cloud(Aliyun) and self written sample applications connecting to your personal Alibaba Cloud IoT Platform after a short setup.

Base of this image builds [debian](https://www.balena.io/docs/reference/base-images/base-images/) with enabled [SSH](https://en.wikipedia.org/wiki/Secure_Shell), created user 'root' and preinstalled Python IoT SDK from [here](https://github.com/aliyun/aliyun-openapi-python-sdk/tree/master/aliyun-python-sdk-iot). Guidance for a self written sample program find below. 

Before using the sample you have to sign up with [Alibaba Cloud](https://myaccount.alibabacloud.com) and create an account. At the time of image preparation Alibaba's Cloud IoT Platform services are not free of charge. You pay 0.8$ for 1.000.000 Pub or Sub operations.

How to use the Python IoT SDK is described [here](https://www.alibabacloud.com/help/doc-detail/42700.htm).

#### Container prerequisites

##### Port mapping

For remote login to the container across SSH the container's SSH port `22` needs to be mapped to any free netPI host port.

#### Getting started

STEP 1. Open netPI's landing page under `https://<netpi's ip address>`.

STEP 2. Click the Docker tile to open the [Portainer.io](http://portainer.io/) Docker management user interface.

STEP 3. Enter the following parameters under **Containers > Add Container**

* **Image**: `hilschernetpi/netpi-alibaba-cloud-sdk-python`

* **Port mapping**: `Host "22" (any unused one) -> Container "22"` 

* **Restart policy"** : `always`

STEP 4. Press the button **Actions > Start/Deploy container**

Pulling the image may take a while (5-10mins). Sometimes it takes so long that a time out is indicated. In this case repeat the **Actions > Start/Deploy container** action.

#### Accessing

The container starts the SSH server automatically when started. Open a terminal connection to it with an SSH client such as [putty](http://www.putty.org/) using netPI's IP address at your mapped port.

Use the credentials `root` as user and `root` as password when asked and you are logged in as root user `root`.

##### Alibaba Cloud Console

STEP 1: Login to Alibaba Cloud

STEP 2: Click `Console` in the top navigation bar.

STEP 3: Create an access key clicking `AccessKey` under your icon (avatar) in the top navigation bar rightmost. Continue with a click to `Create Access Key`. The console will return to your the value `AccessKey ID` and `Access Key Secret`. Handle those values with care.

STEP 4: Unfold `Base Services` in navigation bar to the left and click `IoT Platform`. Click `Activate Now`. Check the `Agreement` and click `Enable Now`. The IoT Platform is now enabled.

STEP 5: In the `IoT Platform` pane click `Products` and then `Create Product`. Name it e.g. "MyPi" and click `OK`. After creation note down the generated `ProductKey` for reference e.g. "a1GXWXg6Tmu".

STEP 6: In the `IoT Platform` pane click `Devices` and then `Add Device`. As product select "MyPi" and give it the `DeviceName` e.g. "MyDevice".

STEP 7: Notice the just created device in the `Device List`. Click it and then `Topic List`. There you find listed the `Device Topic List` (access strings) for the SDK to publish or to subscribe data to the device.

##### SDK 

The Python IoT SDK services provided make use of Alibaba's IoT Platform HTTP/HTTPS based [API call reference](https://www.alibabacloud.com/help/doc-detail/30559.htm). For a simple test edit a file e.g. `test.py` and make the following edits:

STEP 1: Let your Python test program begin with the following lines to import the relevant functions for a simple publishing message test

```
from aliyunsdkcore import client
from aliyunsdkiot.request.v20170420 import PubRequest
```


STEP 2: Continue editing by setting the credentials of your cloud account received in the step above

```
accessKeyId = 'LAAISEXe14uQS58h'
accessKeySecret = 'ayr6SZe9I5nvFNgoQiyXR4H9YPhFOb'
clt = client.AcsClient(accessKeyId, accessKeySecret, 'cn-shanghai')
```


Replace the given example values `accessKeyId` and `accessKeySecret` above with your own values received during the creation step before.

STEP 3: In accordance with the [PubRequest API](https://www.alibabacloud.com/help/doc-detail/69793.htm) generate a request as follows 

```
request = PubRequest.PubRequest()
request.set_accept_format('json')
request.set_ProductKey('a1GXWXg6Tmu')
request.set_TopicFullName('/a1GXWXg6Tmu/MyDevice/get')
request.set_MessageContent('aGVsbG8gd29ybGQ=') #"hello world" Base64 encoded
request.set_Qos(0)
result = clt.do_action_with_exception(request)
print 'result : ' + result
```


Replace the value in section set_ProductKey('...') with your personal value received from the IoT Platform console the time you created the product "MyPi".

In the section set_TopicFullName('...') use any of the device topics from the `Device Topic List` offered in the console for the device "MyDevice".

STEP 4: Call your test program with `python test.py`

STEP 5: Go back to the console and click the device "MyDevice" again and then its `Topic List`. See the counters `Published Notifications` incrementing on each example program's call.

#### Automated build

The project complies with the scripting based [Dockerfile](https://docs.docker.com/engine/reference/builder/) method to build the image output file. Using this method is a precondition for an [automated](https://docs.docker.com/docker-hub/builds/) web based build process on DockerHub platform.

DockerHub web platform is x86 CPU based, but an ARM CPU coded output file is needed for Raspberry systems. This is why the Dockerfile includes the [balena.io](https://balena.io/blog/building-arm-containers-on-any-x86-machine-even-dockerhub/) steps.

#### License

View the license information for the software in the project. As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).
As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.

[![N|Solid](http://www.hilscher.com/fileadmin/templates/doctima_2013/resources/Images/logo_hilscher.png)](http://www.hilscher.com)  Hilscher Gesellschaft fuer Systemautomation mbH  www.hilscher.com
