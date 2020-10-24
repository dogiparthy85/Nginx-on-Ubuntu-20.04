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


By default, Nginx is configured to start automatically when the server boots. If this is not what you want, you can disable this behavior by typing:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable nginx
</li></ul></code></pre>

To re-enable the service to start up at boot, you can type:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable nginx
</li></ul></code></pre>

You have now learned basic management commands and should be ready to configure the site to host more than one domain.

<h2 id="step-5-–-setting-up-server-blocks-recommended">Step 5 – Setting Up Server Blocks (Recommended)</h2>

When using the Nginx web server, server blocks (similar to virtual hosts in Apache) can be used to encapsulate configuration details and host more than one domain from a single server. We will set up a domain called your_domain, but you should replace this with your own domain name.  

Nginx on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become unwieldy if you are hosting multiple sites. Instead of modifying /var/www/html, let’s create a directory structure within /var/www for our your_domain site, leaving /var/www/html in place as the default directory to be served if a client request doesn’t match any other sites.

Create the directory for your_domain as follows, using the -p flag to create any necessary parent directories:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir -p /var/www/<span class="highlight">your_domain</span>/html
</li></ul></code></pre>

Next, assign ownership of the directory with the $USER environment variable:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo chown -R $USER:$USER /var/www/<span class="highlight">your_domain</span>/html
</li></ul></code></pre>

The permissions of your web roots should be correct if you haven’t modified your umask value, which sets default file permissions. To ensure that your permissions are correct and allow the owner to read, write, and execute the files while granting only read and execute permissions to groups and others, you can input the following command:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo chmod -R 755 /var/www/<span class="highlight">your_domain</span>
</li></ul></code></pre>

Next, create a sample index.html page using nano or your favorite editor:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /var/www/<span class="highlight">your_domain</span>/html/index.html
</li></ul></code></pre>

Inside, add the following sample HTML:
<div class="code-label " title="/var/www/your_domain/html/index.html">/var/www/your_domain/html/index.html</div>
<div class="code-toolbar"><pre class="code-pre  language-html"><code class="code-highlight  language-html"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Welcome to <span class="highlight">your_domain</span>!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Success!  The <span class="highlight">your_domain</span> server block is working!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre><div class="toolbar"><div class="toolbar-item"><button>Copy</button></div></div></div>

Save and close the file by typing CTRL and X then Y and ENTER when you are finished.

In order for Nginx to serve this content, it’s necessary to create a server block with the correct directives. Instead of modifying the default configuration file directly, let’s make a new one at /etc/nginx/sites-available/your_domain:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/nginx/sites-available/<span class="highlight">your_domain</span>
</li></ul></code></pre>

Paste in the following configuration block, which is similar to the default, but updated for our new directory and domain name:

<div class="code-label " title="/etc/nginx/sites-available/your_domain">/etc/nginx/sites-available/your_domain</div>

<pre class="code-pre  language-html"><code class="code-highlight  language-html">server {
        listen 80;
        listen [::]:80;

        root /var/www/<span class="highlight">your_domain</span>/html;
        index index.html index.htm index.nginx-debian.html;

        server_name <span class="highlight">your_domain</span> www.<span class="highlight">your_domain</span>;

        location / {
                try_files $uri $uri/ =404;
        }
}</code></pre>

Notice that we’ve updated the root configuration to our new directory, and the server_name to our domain name.

Next, let’s enable the file by creating a link from it to the sites-enabled directory, which Nginx reads from during startup:

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo ln -s /etc/nginx/sites-available/<span class="highlight">your_domain</span> /etc/nginx/sites-enabled/
</li></ul></code></pre>

Two server blocks are now enabled and configured to respond to requests based on their listen and server_name directives (you can read more about how Nginx processes these directives here):

your_domain: Will respond to requests for your_domain and www.your_domain.
default: Will respond to any requests on port 80 that do not match the other two blocks.
To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the /etc/nginx/nginx.conf file. Open the file:

<li class="line" data-prefix="$">sudo nano /etc/nginx/nginx.conf
</li>

Find the server_names_hash_bucket_size directive and remove the # symbol to uncomment the line. If you are using nano, you can quickly search for words in the file by pressing CTRL and w.

<p>Find the <code>server_names_hash_bucket_size</code> directive and remove the <code>#</code> symbol to uncomment the line. If you are using nano, you can quickly search for words in the file by pressing <code>CTRL</code> and <code>w</code>.</p>

  <div class="code-label " title="/etc/nginx/nginx.conf">/etc/nginx/nginx.conf</div>
  <pre class="code-pre "><code>...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
</code></pre>

Save and close the file when you are finished.

Next, test to make sure that there are no syntax errors in any of your Nginx files:

<li class="line" data-prefix="$">sudo nginx -t
</li>

If there aren’t any problems, restart Nginx to enable your changes:

<p>If there aren’t any problems, restart Nginx to enable your changes:</p>

<pre class="code-pre command prefixed"><code><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart nginx
</li></ul></code></pre>

Nginx should now be serving your domain name. You can test this by navigating to http://your_domain, where you should see something like this:

<img src="https://github.com/dogiparthy85/Nginx-on-Ubuntu-20.04/blob/main/first_block%5B1%5D.png" alt="Nginx first server block">

<h2 id="step-6-–-getting-familiar-with-important-nginx-files-and-directories">Step 6 – Getting Familiar with Important Nginx Files and Directories</h2>

Now that you know how to manage the Nginx service itself, you should take a few minutes to familiarize yourself with a few important directories and files.

<h3 id="content">Content</h3>

<li><code>/var/www/html</code>: The actual web content, which by default only consists of the default Nginx page you saw earlier, is served out of the <code>/var/www/html</code> directory. This can be changed by altering Nginx configuration files.</li>

<h3 id="server-configuration">Server Configuration</h3>

<ul>
<li><code>/etc/nginx</code>: The Nginx configuration directory. All of the Nginx configuration files reside here.</li>
<li><code>/etc/nginx/nginx.conf</code>: The main Nginx configuration file. This can be modified to make changes to the Nginx global configuration.</li>
<li><code>/etc/nginx/sites-available/</code>: The directory where per-site server blocks can be stored. Nginx will not use the configuration files found in this directory unless they are linked to the <code>sites-enabled</code> directory. Typically, all server block configuration is done in this directory, and then enabled by linking to the other directory.</li>
<li><code>/etc/nginx/sites-enabled/</code>: The directory where enabled per-site server blocks are stored.  Typically, these are created by linking to configuration files found in the <code>sites-available</code> directory.</li>
<li><code>/etc/nginx/snippets</code>: This directory contains configuration fragments that can be included elsewhere in the Nginx configuration. Potentially repeatable configuration segments are good candidates for refactoring into snippets.</li>
</ul>

<h3 id="server-logs">Server Logs</h3>

<ul>
<li><code>/var/log/nginx/access.log</code>: Every request to your web server is recorded in this log file unless Nginx is configured to do otherwise.</li>
<li><code>/var/log/nginx/error.log</code>: Any Nginx errors will be recorded in this log.</li>
</ul>

<h2 id="conclusion">Conclusion</h2>

Now that you have your web server installed, you have many options for the type of content to serve and the technologies you want to use to create a richer experience.

If you’d like to build out a more complete application stack, check out the article How To Install Linux, Nginx, MySQL, PHP (LEMP stack) on Ubuntu 20.04.
