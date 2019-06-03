---
layout: post
title: "Socks proxy & proxychains & run the tensorflow hello_world program"
date: 2019-06-03 19:00 +0800
categories: tech
---


# Step 1
Get a VPS if you don't have one. 
I already have one. Let's connect to the VPS by SSH:
```
ssh -D 12345 root@12.34.56.78
```

# step 2
Config the computer to apply the network proxy. Here I use ubuntu OS, my config as following:
[![](https://rayxyz.github.io/assets/images/general/setup-socks-proxy-on-ubuntu.png)](https://rayxyz.github.io/assets/images/general/setup-socks-proxy-on-ubuntu.png)

# Step 3
Install proxychains
```
sudo apt-get install proxychains
```

Edit the proxychains configuration file.
```
vim /etc/proxychains.conf
```

Comment the sock4 proxy, and add the new sock5 proxy entry.
```
# Some timeouts in milliseconds
tcp_read_time_out 15000
tcp_connect_time_out 8000

# ProxyList format
#       type  host  port [user pass]
#       (values separated by 'tab' or 'blank')
#
#
#        Examples:
#
#               socks5  192.168.67.78   1080    lamer   secret
#               http    192.168.89.3    8080    justu   hidden
#               socks4  192.168.1.49    1080
#               http    192.168.39.93   8080    
#               
#
#       proxy types: http, socks4, socks5
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
# socks4        127.0.0.1 9050
socks5 127.0.0.1 12345
```

# Step 4 
Let's install the tensorflow. If you cannot access the website, go to the step 5 and step 6 and get back here.
```
https://www.tensorflow.org/install
```
```
ray@ray-pc:~$ sudo pip3 install tensorflow
WARNING: The directory '/home/ray/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
WARNING: The directory '/home/ray/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Collecting tensorflow
  Downloading https://files.pythonhosted.org/packages/ca/f2/0931c194bb98398017d52c94ee30e5e1a4082ab6af76e204856ff1fdb33e/tensorflow-1.13.1-cp35-cp35m-manylinux1_x86_64.whl (92.5MB)
     |████████████████████████████████| 92.5MB 11.9MB/s 
Collecting keras-applications>=1.0.6 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/71/e3/19762fdfc62877ae9102edf6342d71b28fbfd9dea3d2f96a882ce099b03f/Keras_Applications-1.0.8-py3-none-any.whl (50kB)
     |████████████████████████████████| 51kB 11.7MB/s 
Collecting termcolor>=1.1.0 (from tensorflow)
Requirement already satisfied: wheel>=0.26 in /usr/local/lib/python3.5/dist-packages (from tensorflow) (0.33.4)
Collecting numpy>=1.13.3 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/bb/ef/d5a21cbc094d3f4d5b5336494dbcc9550b70c766a8345513c7c24ed18418/numpy-1.16.4-cp35-cp35m-manylinux1_x86_64.whl (17.2MB)
     |████████████████████████████████| 17.2MB 4.2MB/s 
Collecting gast>=0.2.0 (from tensorflow)
Collecting protobuf>=3.6.1 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/7c/d2/581ebc3c41879aca2c4fce5c37cdb8d779c4ea79109b6da7f640735ea0a2/protobuf-3.8.0-cp35-cp35m-manylinux1_x86_64.whl (1.2MB)
     |████████████████████████████████| 1.2MB 13.9MB/s 
Collecting grpcio>=1.8.6 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/14/19/f1858ed60786ff681a5f8681448b56bffaaa87a81e9a7ca5cd075a873b35/grpcio-1.21.1-cp35-cp35m-manylinux1_x86_64.whl (2.2MB)
     |████████████████████████████████| 2.2MB 15.0MB/s 
Collecting keras-preprocessing>=1.0.5 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/28/6a/8c1f62c37212d9fc441a7e26736df51ce6f0e38455816445471f10da4f0a/Keras_Preprocessing-1.1.0-py2.py3-none-any.whl (41kB)
     |████████████████████████████████| 51kB 12.4MB/s 
Collecting tensorflow-estimator<1.14.0rc0,>=1.13.0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/bb/48/13f49fc3fa0fdf916aa1419013bb8f2ad09674c275b4046d5ee669a46873/tensorflow_estimator-1.13.0-py2.py3-none-any.whl (367kB)
     |████████████████████████████████| 368kB 13.7MB/s 
Collecting tensorboard<1.14.0,>=1.13.0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/0f/39/bdd75b08a6fba41f098b6cb091b9e8c7a80e1b4d679a581a0ccd17b10373/tensorboard-1.13.1-py3-none-any.whl (3.2MB)
     |████████████████████████████████| 3.2MB 1.0MB/s 
Collecting absl-py>=0.1.6 (from tensorflow)
Requirement already satisfied: six>=1.10.0 in /usr/lib/python3/dist-packages (from tensorflow) (1.10.0)
Collecting astor>=0.6.0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/d1/4f/950dfae467b384fc96bc6469de25d832534f6b4441033c39f914efd13418/astor-0.8.0-py2.py3-none-any.whl
Collecting h5py (from keras-applications>=1.0.6->tensorflow)
  Downloading https://files.pythonhosted.org/packages/4c/77/c4933e12dca0f61bcdafc207c7532e1250b8d12719459fd85132f3daa9fd/h5py-2.9.0-cp35-cp35m-manylinux1_x86_64.whl (2.8MB)
     |████████████████████████████████| 2.8MB 13.8MB/s 
Requirement already satisfied: setuptools in /usr/local/lib/python3.5/dist-packages (from protobuf>=3.6.1->tensorflow) (41.0.1)
Collecting mock>=2.0.0 (from tensorflow-estimator<1.14.0rc0,>=1.13.0->tensorflow)
  Downloading https://files.pythonhosted.org/packages/05/d2/f94e68be6b17f46d2c353564da56e6fb89ef09faeeff3313a046cb810ca9/mock-3.0.5-py2.py3-none-any.whl
Collecting werkzeug>=0.11.15 (from tensorboard<1.14.0,>=1.13.0->tensorflow)
  Downloading https://files.pythonhosted.org/packages/9f/57/92a497e38161ce40606c27a86759c6b92dd34fcdb33f64171ec559257c02/Werkzeug-0.15.4-py2.py3-none-any.whl (327kB)
     |████████████████████████████████| 327kB 16.6MB/s 
Collecting markdown>=2.6.8 (from tensorboard<1.14.0,>=1.13.0->tensorflow)
  Downloading https://files.pythonhosted.org/packages/c0/4e/fd492e91abdc2d2fcb70ef453064d980688762079397f779758e055f6575/Markdown-3.1.1-py2.py3-none-any.whl (87kB)
     |████████████████████████████████| 92kB 10.8MB/s 
Installing collected packages: numpy, h5py, keras-applications, termcolor, gast, protobuf, grpcio, keras-preprocessing, absl-py, mock, tensorflow-estimator, werkzeug, markdown, tensorboard, astor, tensorflow
Successfully installed absl-py-0.7.1 astor-0.8.0 gast-0.2.2 grpcio-1.21.1 h5py-2.9.0 keras-applications-1.0.8 keras-preprocessing-1.1.0 markdown-3.1.1 mock-3.0.5 numpy-1.16.4 protobuf-3.8.0 tensorboard-1.13.1 tensorflow-1.13.1 tensorflow-estimator-1.13.0 termcolor-1.1.0 werkzeug-0.15.4
```

# Step 5
Ok now, we can try it out. Copy & paste the tensorflow hello_world.py code in the editor.
[![](https://rayxyz.github.io/assets/images/general/tensorflow-hello_world-code.png)](https://rayxyz.github.io/assets/images/general/tensorflow-hello_world-code.png)

```
import tensorflow as tf
mnist = tf.keras.datasets.mnist

(x_train, y_train),(x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

model = tf.keras.models.Sequential([
  tf.keras.layers.Flatten(input_shape=(28, 28)),
  tf.keras.layers.Dense(128, activation='relu'),
  tf.keras.layers.Dropout(0.2),
  tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(x_train, y_train, epochs=5)
model.evaluate(x_test, y_test)
```
You can find the code at: 
```
https://www.tensorflow.org/overview/
```

Run it in the terminal, I got errors:
```
ray@ray-pc:~/workspace/python/tflearn$ python3 hello_world.py 
Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz
Traceback (most recent call last):
  File "/usr/lib/python3.5/urllib/request.py", line 1254, in do_open
    h.request(req.get_method(), req.selector, req.data, headers)
  File "/usr/lib/python3.5/http/client.py", line 1106, in request
    self._send_request(method, url, body, headers)
  File "/usr/lib/python3.5/http/client.py", line 1151, in _send_request
    self.endheaders(body)
  File "/usr/lib/python3.5/http/client.py", line 1102, in endheaders
    self._send_output(message_body)
  File "/usr/lib/python3.5/http/client.py", line 934, in _send_output
    self.send(msg)
  File "/usr/lib/python3.5/http/client.py", line 877, in send
    self.connect()
  File "/usr/lib/python3.5/http/client.py", line 1252, in connect
    super().connect()
  File "/usr/lib/python3.5/http/client.py", line 849, in connect
    (self.host,self.port), self.timeout, self.source_address)
  File "/usr/lib/python3.5/socket.py", line 693, in create_connection
    for res in getaddrinfo(host, port, 0, SOCK_STREAM):
  File "/usr/lib/python3.5/socket.py", line 732, in getaddrinfo
    for res in _socket.getaddrinfo(host, port, family, type, proto, flags):
socket.gaierror: [Errno -3] Temporary failure in name resolution

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/local/lib/python3.5/dist-packages/tensorflow/python/keras/utils/data_utils.py", line 248, in get_file
    urlretrieve(origin, fpath, dl_progress)
  File "/usr/lib/python3.5/urllib/request.py", line 188, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/lib/python3.5/urllib/request.py", line 163, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/lib/python3.5/urllib/request.py", line 466, in open
    response = self._open(req, data)
  File "/usr/lib/python3.5/urllib/request.py", line 484, in _open
    '_open', req)
  File "/usr/lib/python3.5/urllib/request.py", line 444, in _call_chain
    result = func(*args)
  File "/usr/lib/python3.5/urllib/request.py", line 1297, in https_open
    context=self._context, check_hostname=self._check_hostname)
  File "/usr/lib/python3.5/urllib/request.py", line 1256, in do_open
    raise URLError(err)
urllib.error.URLError: <urlopen error [Errno -3] Temporary failure in name resolution>

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "hello_world.py", line 4, in <module>
    (x_train, y_train),(x_test, y_test) = mnist.load_data()
  File "/usr/local/lib/python3.5/dist-packages/tensorflow/python/keras/datasets/mnist.py", line 49, in load_data
    file_hash='8a61469f7ea1b51cbae51d4f78837e45')
  File "/usr/local/lib/python3.5/dist-packages/tensorflow/python/keras/utils/data_utils.py", line 250, in get_file
    raise Exception(error_msg.format(origin, e.errno, e.reason))
Exception: URL fetch failure on https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz: None -- [Errno -3] Temporary failure in name resolution
```
What is wrong with the erros? We can obviously see, the program was going to download something in the first line of the log: 
```
Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz
```
The reason of the above errors is my computer cannot directly access the domain storage.googleapis.com because it is being blocked here in China. That's why I set up the socks proxy & proxychains. Now we use proxychains to leverage the socks proxy. You may ask who's so boring to block a tech website, that's another story we don't care here, or go ask the Chinese government. I am here to tell people how to unblock the blocked tech & other websites. As a tech guy I know it's fucking important to access tech resources with no any trouble.

# Step 6
Finally, let's check it out.
We need to add `proxychanins` in the command line or it won't run by socks proxy.
```
sudo proxychains python3 hello_world.py 
```

Here's the result by using proxy:
```
ray@ray-pc:~/workspace/python/tflearn$ sudo proxychains python3 hello_world.py 
[sudo] password for ray: 
ProxyChains-3.1 (http://proxychains.sf.net)
Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz
|DNS-request| storage.googleapis.com 
|S-chain|-<>-127.0.0.1:12345-<><>-4.2.2.2:53-<><>-OK
|DNS-response| storage.googleapis.com is 216.58.195.80
|S-chain|-<>-127.0.0.1:12345-<><>-216.58.195.80:443-<><>-OK
11493376/11490434 [==============================] - 3s 0us/step
WARNING:tensorflow:From /usr/local/lib/python3.5/dist-packages/tensorflow/python/ops/resource_variable_ops.py:435: colocate_with (from tensorflow.python.framework.ops) is deprecated and will be removed in a future version.
Instructions for updating:
Colocations handled automatically by placer.
WARNING:tensorflow:From /usr/local/lib/python3.5/dist-packages/tensorflow/python/keras/layers/core.py:143: calling dropout (from tensorflow.python.ops.nn_ops) with keep_prob is deprecated and will be removed in a future version.
Instructions for updating:
Please use `rate` instead of `keep_prob`. Rate should be set to `rate = 1 - keep_prob`.
2019-06-03 18:23:12.298239: I tensorflow/core/platform/cpu_feature_guard.cc:141] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
2019-06-03 18:23:12.321396: I tensorflow/core/platform/profile_utils/cpu_utils.cc:94] CPU Frequency: 3408000000 Hz
2019-06-03 18:23:12.321764: I tensorflow/compiler/xla/service/service.cc:150] XLA service 0x4205f80 executing computations on platform Host. Devices:
2019-06-03 18:23:12.321798: I tensorflow/compiler/xla/service/service.cc:158]   StreamExecutor device (0): <undefined>, <undefined>
Epoch 1/5
60000/60000 [==============================] - 3s 48us/sample - loss: 0.2945 - acc: 0.9147
Epoch 2/5
60000/60000 [==============================] - 3s 45us/sample - loss: 0.1438 - acc: 0.9564
Epoch 3/5
60000/60000 [==============================] - 3s 45us/sample - loss: 0.1077 - acc: 0.9664
Epoch 4/5
60000/60000 [==============================] - 3s 45us/sample - loss: 0.0889 - acc: 0.9726
Epoch 5/5
60000/60000 [==============================] - 3s 45us/sample - loss: 0.0745 - acc: 0.9772
10000/10000 [==============================] - 0s 18us/sample - loss: 0.0734 - acc: 0.9771
```
Everything goes flawlessly. The program runs smoothly, we can see the programs print the result at the last lines. And we can see the proxy log in the running log above:
```
|DNS-request| storage.googleapis.com 
|S-chain|-<>-127.0.0.1:12345-<><>-4.2.2.2:53-<><>-OK
|DNS-response| storage.googleapis.com is 216.58.195.80
|S-chain|-<>-127.0.0.1:12345-<><>-216.58.195.80:443-<><>-OK
```

Now, our first tensorflow hello_world program is a big success. Cheers!