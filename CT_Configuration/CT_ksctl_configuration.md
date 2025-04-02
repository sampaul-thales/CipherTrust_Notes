## Configuring KSCTL utility.

In this section, I will show you how to configure the KSCTL utility. KSCTL is a command-line utility that serves as a toolkit, allowing users to manage CipherTrust Manager. It uses RestAPI to communicate with CipherTrust Manager and carry out all management-related tasks.

> [!NOTE]
> This section assumes that you have fully configured your CipherTrust Manager and can access it on your network.

<br>

### How to download KSCTL.

#### Easiest way to dowload ksctl is by using the link specified below:
> https://<CipherTrust_Manager_IP_or_HOSTNAME>/downloads/ksctl_images.zip
<pre>
	sampaul@ct-ub2204:~$ wget https://vciphertrust-i.daenerys.home/downloads/ksctl_images.zip --no-check-certificate
	sampaul@ct-ub2204:~$ ls -l
	total 46724
	-rw-rw-r-- 1 sampaul sampaul 47843281 Jan  6 08:22 ksctl_images.zip
</pre>

#### Another way to download ksctl.
- Open your CipherTrust Manager's user interface.
- Look for the "API & CLI Documentation" link at the bottom-right corner of the page and click on it.
- On the API playground page, find the "CLI Guide" option in the top-left corner and click on it.
- You should now see a download option labelled "CLI" in the top-right corner of your screen. Click on this to download the CLI.
- The downloaded ZIP file contains executables for Windows, Linux, and Darwin. Copy the one you need.
<pre>
	sampaul@ct-ub2204:~$ unzip ksctl_images.zip
	Archive:  ksctl_images.zip
  	inflating: ksctl-darwin-amd64
  	inflating: ksctl-linux-amd64
  	inflating: ksctl-win-amd64.exe
  	inflating: config_example.yaml
	sampaul@ct-ub2204:~$ ls -l
	total 151260
	-rw-r--r-- 1 sampaul sampaul      170 Jan  6 08:17 config_example.yaml
	-rwxr-xr-x 1 sampaul sampaul 35394400 Jan  6 08:22 ksctl-darwin-amd64
	-rw-rw-r-- 1 sampaul sampaul 47843281 Jan  6 08:22 ksctl_images.zip
	-rwxr-xr-x 1 sampaul sampaul 35453538 Jan  6 08:21 ksctl-linux-amd64
	-rwxr-xr-x 1 sampaul sampaul 36185600 Jan  6 08:21 ksctl-win-amd64.exe
</pre>

<br>

### Configuring KSCTL

#### - Create .ksctl directory in your home directory.
<pre>
	sampaul@ct-ub2204:~$ pwd
	/home/sampaul
	sampaul@ct-ub2204:~$ mkdir .ksctl
</pre>


#### - Move the executable and config_example.yaml file into .ksctl.
<pre>
	sampaul@ct-ub2204:~$ mv ksctl-linux-amd64 config_example.yaml .ksctl/
	sampaul@ct-ub2204:~$ mv .ksctl/ksctl-linux-amd64 .ksctl/ksctl
	sampaul@ct-ub2204:~$ chmod 500 .ksctl/ksctl

</pre>

#### - Rename or make a copy of config_example.yaml as config.yaml.
<pre>
	sampaul@ct-ub2204:~$ mv .ksctl/config_example.yaml .ksctl/config.yaml
</pre>

#### - Edit the config.yaml file and enter all the required information.
> [!TIP]
> Password can be left as blank.
<pre>
	sampaul@ct-ub2204:~$ cat .ksctl/config.yaml
	KSCTL_VERBOSITY: false
	KSCTL_RESP: json
	KSCTL_USERNAME: sampaul
	KSCTL_PASSWORD:
	KSCTL_URL: https://vciphertrust-i.daenerys.home
	KSCTL_JWT:
	KSCTL_NOSSLVERIFY: true
	KSCTL_TIMEOUT: 30
</pre>

#### - Update the path to ksctl executable to PATH environment variable.

#### - Check if ksctl can connect to your CipherTrust.
<pre>
	sampaul@ct-ub2204:~$ ksctl version
	password: --YOUR_PASSWORD--
	{
    	    "version": "2.19.0+15144",
        	"build_date": "Mon Jan  6 16:21:26 UTC 2025",
        	"server_model": "CipherTrust Manager k170v",
        	"server_version": "2.19.0+14195"
	}

	sampaul@ct-ub2204:~$ ksctl system info get
	password:
	{
		"name": "",
		"version": "2.19.0+14195",
		"version_suffix": "null",
		"model": "CipherTrust Manager k170v",
		"vendor": "Thales Group",
		"crypto_version": "1.7.0",
		"uptime": "0 days, 3 hours, 22 minutes",
		"system_time": "2025-03-26T13:17:33-07:00"
	}
</pre>

<br>

### KSCTL configuration parameters.

There are four ways to provider configuration parameters to ksctl. The list below ordered by precedence:

1. Using command line argument. (Highest precedence)
2. Using a config file specified via command line.
3. Using environment variables.
4. The default configuration file. (Lowest prededence).


#### - Default configuration file in use.

<pre>
sampaul@ct-ub2204:~$ cat .ksctl/config.yaml
KSCTL_VERBOSITY: false
KSCTL_RESP: json
KSCTL_USERNAME: sampaul
KSCTL_PASSWORD:
KSCTL_URL: https://vciphertrust-i.daenerys.home
KSCTL_JWT:
KSCTL_NOSSLVERIFY: true
KSCTL_TIMEOUT: 30

sampaul@ct-ub2204:~$ ksctl system info get
password:
{
        "name": "vCipherTrust-I",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 0 hours, 53 minutes",
        "system_time": "2025-04-02T10:22:22-07:00"
}
</pre>


#### - Using a command line argument to override default config file.
<pre>
sampaul@ct-ub2204:~$ ksctl system info get --url https://vciphertrust-ii.daenerys.home
password:
{
        "name": "vCipherTrust-II",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 0 hours, 55 minutes",
        "system_time": "2025-04-02T10:24:14-07:00"
}
</pre>

#### - Using environment variable.

URL as environment variable.
<pre>
sampaul@ct-ub2204:~$ KSCTL_URL=https://vciphertrust-ii.daenerys.home ksctl system info get
password:
{
        "name": "vCipherTrust-II",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 0 hours, 57 minutes",
        "system_time": "2025-04-02T10:25:54-07:00"
}
</pre>

URL and PASSWORD as environment variable.
<pre>
sampaul@ct-ub2204:~$ KSCTL_PASSWORD=CT_P@$$w0rd KSCTL_URL=https://vciphertrust-ii.daenerys.home ksctl system info get
{
        "name": "vCipherTrust-II",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 1 hours, 59 minutes",
        "system_time": "2025-04-02T11:27:42-07:00"
}
</pre>


#### - Using a separate configuration file.
<pre>
sampaul@ct-ub2204:~$ cat my.yaml
KSCTL_URL: https://vciphertrust-ii.daenerys.home
KSCTL_USERNAME: sampaul
KSCTL_VERBOSITY: false
KSCTL_RESP: json
KSCTL_NOSSLVERIFY: true
KSCTL_TIMEOUT: 30

sampaul@ct-ub2204:~$ ksctl system info get --configfile my.yaml
password:
{
        "name": "vCipherTrust-II",
        "version": "2.19.0+14195",
        "version_suffix": "null",
        "model": "CipherTrust Manager k170v",
        "vendor": "Thales Group",
        "crypto_version": "1.7.0",
        "uptime": "0 days, 3 hours, 12 minutes",
        "system_time": "2025-04-02T12:41:12-07:00"
}
</pre>

<br>

[Back to previous page](README.md)
