# Nginx-on-Ubuntu-20.04
Install Nginx on Ubuntu 20.04

<p><a href="https://www.nginx.com/">Nginx</a> is one of the most popular web servers in the world and is responsible for hosting some of the largest and highest-traffic sites on the internet. It is a lightweight choice that can be used as either a web server or reverse proxy.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>Before you begin this guide, you should have a regular, non-root user with sudo privileges configured on your server. You can learn how to configure a regular user account by following our <a href="https://github.com/dogiparthy85/ubuntu20.04-server">Initial server setup guide for Ubuntu 20.04</a>.</p>

When you have an account available, log in as your non-root user to begin.

<h2 id="step-1-–-installing-nginx">Step 1 – Installing Nginx</h2>

Because Nginx is available in Ubuntu’s default repositories, it is possible to install it from these repositories using the apt packaging system.

Since this is our first interaction with the apt packaging system in this session, we will update our local package index so that we have access to the most recent package listings. Afterwards, we can install nginx:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li><li class="line" data-prefix="$">sudo apt install nginx
</li></ul></code></pre>

After accepting the procedure, apt will install Nginx and any required dependencies to your server.

<h2 id="step-2-–-adjusting-the-firewall">Step 2 – Adjusting the Firewall</h2>

Before testing Nginx, the firewall software needs to be adjusted to allow access to the service. Nginx registers itself as a service with ufw upon installation, making it straightforward to allow Nginx access.

List the application configurations that ufw knows how to work with by typing:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw app list
</li></ul></code></pre>

You should get a listing of the application profiles:

<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
</code></pre>

<p>As demonstrated by the output, there are three profiles available for Nginx:</p>

<ul>
<li><strong>Nginx Full</strong>: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)</li>
<li><strong>Nginx HTTP</strong>: This profile opens only port 80 (normal, unencrypted web traffic)</li>
<li><strong>Nginx HTTPS</strong>: This profile opens only port 443 (TLS/SSL encrypted traffic)</li>
</ul>

It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Right now, we will only need to allow traffic on port 80.

You can enable this by typing:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 'Nginx HTTP'
</li></ul></code></pre>

You can verify the change by typing:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>

The output will indicated which HTTP traffic is allowed:

<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
</code></pre>

<h2 id="step-3-–-checking-your-web-server">Step 3 – Checking your Web Server</h2>

At the end of the installation process, Ubuntu 20.04 starts Nginx. The web server should already be up and running.

We can check with the systemd init system to make sure the service is running by typing:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">systemctl status nginx
</li></ul></code></pre>

<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: <span class="highlight">active (running)</span> since Fri 2020-04-20 16:08:19 UTC; 3 days ago
     Docs: man:nginx(8)
 Main PID: 2369 (nginx)
    Tasks: 2 (limit: 1153)
   Memory: 3.5M
   CGroup: /system.slice/nginx.service
           ├─2369 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─2380 nginx: worker process
</code></pre>

As confirmed by this out, the service has started successfully. However, the best way to test this is to actually request a page from Nginx.

You can access the default Nginx landing page to confirm that the software is running properly by navigating to your server’s IP address. If you do not know your server’s IP address, you can find it by using the icanhazip.com tool, which will give you your public IP address as received from another location on the internet:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">curl -4 icanhazip.com
</li></ul></code></pre>

When you have your server’s IP address, enter it into your browser’s address bar:

<pre class="code-pre "><code>http://<span class="highlight">your_server_ip</span>
</code></pre>

You should receive the default Nginx landing page:
<img src="https://github.com/dogiparthy85/Nginx-on-Ubuntu-20.04/blob/main/default_page%5B1%5D.png" alt="Nginx default page">

If you are on this page, your server is running correctly and is ready to be managed.

<h2 id="step-4-–-managing-the-nginx-process">Step 4 – Managing the Nginx Process</h2>

Now that you have your web server up and running, let’s review some basic management commands.

To stop your web server, type:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop nginx
</li></ul></code></pre>

To start the web server when it is stopped, type:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start nginx
</li></ul></code></pre>

To stop and then start the service again, type:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart nginx
</li></ul></code></pre>

If you are only making configuration changes, Nginx can often reload without dropping connections. To do this, type:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl reload nginx
</li></ul></code></pre>



