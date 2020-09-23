# Install ipyleaflet jupyter  on AWS

## ssh to your  aws instance

1.  Obtain your aws security key and place it in your home directory on your local computer

    From WLS
    
  ```aidl
      cd
      vi myaws.pem
      i
   ```
    
    
    ####copy and pasted the file I sent
     
   ```aidl
    ESCAPE
    ESCAPE
    ZZ
   ```
   
    
    
2.  Change the permissions on the key
    
    `chmod 400 myaws.pem`
    
    Note:  You may place this key in your .ssh directory and make the proper adjustments when
    you ssh to your aws instance

3.  ssh into your instance - obtain addresses from AWS administrator

`ssh -i http://ec2-34-222-75-113.us-west-2.compute.amazonaws.com:8888`

REPLACE THE ADDRESS WITH THE ADDRESS YOUR ADMIN GAVE YOU.

## Install Anaconda
1.  Install Anaconda on your aws instance.
    Per this article
    https://analyticsindiamag.com/setting-up-a-completely-free-jupyter-server-for-data-science-with-aws/

2.  Within the aws instance create a Downloads directory and cd to it

     ```
        mkdir Downloads
        cd Downloads
    ```
     
3.  Download the Anaconda code

`wget https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh`

4.  Unpack the shell installer - this will install Anaconda


`bash Anaconda3-2020.02-Linux-x86_64.sh`   

You will be prompted to agree to terms and asked where to install.  Say 'yes' and 'enter'


```
Do you accept the license terms? [yes|no]
[no] >>> 
Please answer 'yes' or 'no':'
>>> yes

Anaconda3 will now be installed into this location:
/home/ubuntu/anaconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/home/ubuntu/anaconda3] >>> 
PREFIX=/home/ubuntu/anaconda3

```

Eventually the installer will ask you if you want to set up an environment,  say 'yes'

```$xslt
installing: pywavelets-1.0.2-py37hdd07704_0 ...
installing: scipy-1.2.1-py37h7c811a0_0 ...
installing: bkcharts-0.2-py37_0 ...
installing: dask-1.1.4-py37_1 ...
installing: patsy-0.5.1-py37_0 ...
installing: pytables-3.5.1-py37h71ec239_0 ...
installing: pytest-astropy-0.5.0-py37_0 ...
installing: scikit-image-0.14.2-py37he6710b0_0 ...
installing: scikit-learn-0.20.3-py37hd81dba3_0 ...
installing: astropy-3.1.2-py37h7b6447c_0 ...
installing: statsmodels-0.9.0-py37h035aef0_0 ...
installing: seaborn-0.9.0-py37_0 ...
installing: anaconda-2019.03-py37_0 ...
installation finished.
Do you wish the installer to initialize Anaconda3
by running conda init? [yes|no]
[no] >>> yes
```



## Set up Jupyter notebooks with an empty password

1.    cd to your home directory, then set the Jupyter password to the empty string

   `cd`
   
   type `which python`
   
   if you don't see 
   
   /home/ubuntu/anaconda3/bin/python
   
   exit the instance by typing <cntl>d and then ssh back in  or 
   
   `source ~/.bashrc`
      
  
   Enter the python interrupter.
   `python`
    
    and create the password.
    ```
    >>> from IPython.lib import passwd
    >>> passwd()
    Enter password: 
    Verify password: 
    'sha1:3506e7831dd5:822f9372e0c42fbe165ef65d0c8dda33846a8075'
    >>> exit()
    ```
    
   
  
Enter you password. 
SAVE THE PASSWORD HASH!!!  It looks like:

`sha1:.....`

2.  Generate a Jupyter configuration file

`jupyter notebook --generate-config`

3. Create certificates for https
```aidl
   mkdir certs   
   cd certs
```   
`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`
   Answer questions
   
 `sudo chown $USER:$USER /home/ubuntu/certs/mycert.pem`  

4.  Edit the configuration file and add the following lines.  Replace the password hash with
the hash created in step 7.

`vi ~/.jupyter/jupyter_notebook_config.py`

```
c = get_config()

# Kernel config
c.IPKernelApp.pylab = 'inline'  # if you want plotting support always in your notebook

# Notebook config
#c.NotebookApp.certfile = u'/home/ubuntu/certs/mycert.pem' #location of your certificate file
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False  #so that the ipython notebook does not opens up a browser by default
c.NotebookApp.password = u'sha1:684b9d7b1280:568ebc354c4b61527dc3f4d07cee7daaad7d399a'  #the encrypted password we generated above
# Set the port to 8888, the port we set up in the AWS EC2 set-up
c.NotebookApp.port = 8888
```

 
4. You can now load your notebooks from http.

Start jupyter notebooks

`(base) ubuntu@ip-172-31-6-131:~$ jupyter notebook`

Open your browser and point to 

`http://ec2-34-210-8-158.us-west-2.compute.amazonaws.com:8888`

REPLACE THE ec2... with the ip for your instance.

you should see notebooks.

Shut the notebooks down by typing <cntl c>

```
0 active kernels
 The Jupyter Notebook is running at:
 http://(ip-172-31-6-131 or 127.0.0.1):8888/
 Shutdown this notebook server (y/[n])? y
```


## Install node

1.  Node is used everywhere.  Let's install it now.

` ubuntu@ip-172-31-6-131:~$ conda install -c conda-forge nodejs`

check to see if node is installed:

`(base) ubuntu@ip-172-31-6-131:~$ npm --version `

## Install ipyleaflet

https://github.com/jupyter-widgets/ipyleaflet

1.  Install ipyleaflet from the home directory

`conda install -c conda-forge ipyleaflet`

2.  Get the ipyleaflet source in order to use the examples

```$xslt
git clone https://github.com/jupyter-widgets/ipyleaflet.git
cd ipyleaflet
```

3.  Install the nbextension

```$xslt
    jupyter nbextension install --py --symlink --sys-prefix ipyleaflet
    jupyter nbextension enable --py --sys-prefix ipyleaflet
```

3.  Start Jupyter Notebooks

```$xslt
jupyter notebook
```  

4. Open the browser and issue

`http://ec2-52-43-206-172.us-west-2.compute.amazonaws.com:8888'

Replacing the ec2-52... with the address your adminstrator provided.


Your notebook should be running.  Use the empty password.
