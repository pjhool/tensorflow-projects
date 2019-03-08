# tensorflow-projects
《실전활용! 텐서플로 딥러닝 프로젝트》 예제 코드

0. Colab Setting 
 
# ColabSheet



[Useful snippets for Google Colaboratory. ](https://medium.com/machine-learning-world/useful-snippets-for-google-colaboratory-free-gpu-included-d976d6b3e6de)
    



1. CNN Study
 * pyImageSearch 
   * https://www.pyimagesearch.com/author/adrian/ 
   
 * Labelling Tool    
   * https://github.com/christopher5106/FastAnnotationTool 
   * https://github.com/tzutalin/labelImg 
 * Fast R-CNN  
   * https://tensorflow.blog/tag/fast-r-cnn/
   * https://towardsdatascience.com/deep-learning-for-object-detection-a-comprehensive-review-73930816d8d9  
   * https://arxiv.org/pdf/1611.10012.pdf 
 
 * Object Detection API 
   * OpenCV install 
     * https://pypi.org/project/opencv-python/ 
   * TensorFlow Object Detection Model 
     * https://github.com/tensorflow/models/tree/master/research/object_detection 
 
 * Image Caption Generation 
   * https://arxiv.org/abs/1411.4389 
   * https://arxiv.org/abs/1502.03044 


2. GAN Study
 * DCGAN & CGAN 
   * https://arxiv.org/abs/1511.06434 
   * https://arxiv.org/abs/1411.1784
 



## Google Drive
```shell
!apt-get install -y -qq software-properties-common python-software-properties module-init-tools
!add-apt-repository -y ppa:alessandro-strada/ppa 2>&1 > /dev/null
!apt-get update -qq 2>&1 > /dev/null
!apt-get -y install -qq google-drive-ocamlfuse fuse

from google.colab import auth
auth.authenticate_user()
from oauth2client.client import GoogleCredentials
creds = GoogleCredentials.get_application_default()
import getpass

!google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret} < /dev/null 2>&1 | grep URL
vcode = getpass.getpass()
!echo {vcode} | google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret}
```

## SSH to Colab
```
#Generate root password
import random, string
password = ''.join(random.choice(string.ascii_letters + string.digits) for i in range(20))

#Download ngrok
! wget -q -c -nc https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
! unzip -qq -n ngrok-stable-linux-amd64.zip
#Setup sshd
! apt-get install -qq -o=Dpkg::Use-Pty=0 openssh-server pwgen > /dev/null
#Set root password
! echo root:$password | chpasswd
! mkdir -p /var/run/sshd
! echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
! echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
! echo "LD_LIBRARY_PATH=/usr/lib64-nvidia" >> /root/.bashrc
! echo "export LD_LIBRARY_PATH" >> /root/.bashrc

#Run sshd
get_ipython().system_raw('/usr/sbin/sshd -D &')

#Ask token
print("Copy authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()

#Create tunnel
get_ipython().system_raw('./ngrok authtoken ' + authtoken + ' && ./ngrok tcp 22 &')
#Print root password
print("Root password: {}".format(password))
#Get public address
! curl -s http://localhost:4040/api/tunnels | python3 -c \
    "import sys, json; result = json.load(sys.stdin)['tunnels'][0]['public_url'].replace('/', '').split(':');\
     print('ssh root@' + result[1] + ' -p ' + result[2]) \
    " 
