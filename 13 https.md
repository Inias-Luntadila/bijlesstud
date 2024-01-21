# HTTPS

## Important folders

## Assignments

Oh, you caught me! Let's not skip any steps and make sure you have the full picture, from DNS to Apache to Certbot, with all the HTTPS and port 443 glory!

### Step 1: DNS Setup

1. **Update DNS Records**:
   - Access your domain registrar's website.
   - Find the DNS settings.
   - Add or update an A record:
     - Type: A
     - Name: `yourdomain.com` (or a subdomain like `www`)
     - Value: Your server's public IP address
   - Save the changes.

### Step 2: Apache Configuration

2. **Install Apache** (if not already installed):

   ```bash
   sudo apt update
   sudo apt install apache2
   ```

3. **Set Up Apache Virtual Host for Port 80**:

   - Create a new config file in `/etc/apache2/sites-available/`:
     ```bash
     sudo nano /etc/apache2/sites-available/yourdomain.com.conf
     ```
   - Add the following configuration:
     ```
     <VirtualHost *:80>
         ServerAdmin admin@yourdomain.com
         ServerName yourdomain.com
         ServerAlias www.yourdomain.com
         DocumentRoot /var/www/html
         ErrorLog ${APACHE_LOG_DIR}/error.log
         CustomLog ${APACHE_LOG_DIR}/access.log combined
     </VirtualHost>
     ```

4. **Enable the New Site and Rewrite Module**:
   ```bash
   sudo a2ensite yourdomain.com.conf
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   ```

### Step 3: Certbot for SSL on Port 443

5. **Install Certbot and the Apache Plugin**:

   ```bash
   sudo apt-get install certbot python3-certbot-apache
   ```

6. **Run Certbot**:

   - Use the `--staging` flag for testing:
     ```bash
     sudo certbot --apache --staging --agree-tos --email your-email@example.com -d yourdomain.com -d www.yourdomain.com
     ```
   - Replace `your-email@example.com` with your email.
   - Replace `yourdomain.com` with your actual domain.

7. **Certbot's Magic**:

   - Certbot modifies the Apache configuration for you:
     - It sets up a new virtual host for port 443 with the SSL configuration.
     - It should automatically add a redirect from port 80 to port 443 if you approve the redirection when prompted.

8. **Check and Restart Apache**:
   - After Certbot finishes, double-check the new configuration files in `/etc/apache2/sites-available/`.
   - Specifically, look for your domain's SSL config file, typically named `yourdomain.com-le-ssl.conf`.
   - Restart Apache to apply all changes:
     ```bash
     sudo systemctl restart apache2
     ```

### Step 4: Test Everything

9. **Test HTTPS and Redirection**:
   - Open a browser and go to `http://yourdomain.com`. You should automatically be redirected to `https://yourdomain.com`.
   - Remember, if you used the `--staging` flag with Certbot, your certificate is a test certificate and will show as invalid in the browser.

### Step 5: Going Live (Optional)

10. **Switch from Staging to Production** (when ready):
    - Re-run Certbot without the `--staging` flag:
      ```bash
      sudo certbot --apache --agree-tos --email your-email@example.com -d yourdomain.com -d www.yourdomain.com
      ```

And there you have it! The full journey from DNS to a securely encrypted site via HTTPS on port 443, not missing a beat. If you hit a bump or want to dive deeper into any of the steps, just give me a shout!

## Handy stuff

**show dates of certificate**

    openssl x509 -noout -dates -in /etc/letsencrypt/live/ferre-verheyen.sasm.uclllabs.be/cert.pem

**self-signed certificate through SSL**

Generating an SSL certificate yourself, known as a self-signed certificate, can be useful for testing or internal applications. It won't be trusted by browsers like a certificate from a recognized Certificate Authority (CA), but it will provide the same level of encryption. Here's how to generate a self-signed SSL certificate using OpenSSL:

### Step 1: Generate a Private Key

First, you need to create a private key. Open a terminal and run:

```bash
openssl genrsa -out yourdomain.key 2048
```

This command generates a 2048-bit RSA private key and saves it to a file named `yourdomain.key`. You can replace `yourdomain.key` with any name you prefer.

### Step 2: Generate a CSR (Certificate Signing Request)

Next, you need to create a CSR using the private key:

```bash
openssl req -new -key yourdomain.key -out yourdomain.csr
```

You'll be prompted to enter some information like your country, state, organization, etc. The most important part is the "Common Name" which should match the domain name (or IP address) where you plan to use the certificate.

### Step 3: Generate the SSL Certificate

Now, you can create the self-signed SSL certificate using the CSR and the private key:

```bash
openssl x509 -req -days 365 -in yourdomain.csr -signkey yourdomain.key -out yourdomain.crt
```

This command creates an SSL certificate (`yourdomain.crt`) that is valid for 365 days. You can change the number of days as per your requirement.

### Step 4: Configuring the Certificate with Your Web Server

After generating your certificate and key, you need to configure your web server to use them for HTTPS.

#### For Apache:

1. Copy the `yourdomain.crt` and `yourdomain.key` files to the appropriate directory (e.g., `/etc/ssl/`).
2. Edit your Apache configuration file for your site (often found in `/etc/apache2/sites-available/`).
3. Make sure these lines are in the configuration (adjust paths if needed):

```apache
SSLEngine on
SSLCertificateFile /path/to/yourdomain.crt
SSLCertificateKeyFile /path/to/yourdomain.key
```

4. Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

#### For Nginx:

1. Copy the `yourdomain.crt` and `yourdomain.key` files to the appropriate directory (e.g., `/etc/ssl/`).
2. Edit your Nginx configuration file for your site (often found in `/etc/nginx/sites-available/`).
3. Make sure these lines are in the server block:

```nginx
ssl_certificate /path/to/yourdomain.crt;
ssl_certificate_key /path/to/yourdomain.key;
```

4. Restart Nginx to apply the changes:

```bash
sudo systemctl restart nginx
```

Remember, self-signed certificates are great for development and internal use, but they'll cause browsers to show a warning because they're not signed by a recognized CA. For production environments, it's always recommended to obtain a certificate from a trusted CA.

If you have any questions or need further guidance, feel free to ask!

https://wiki.uclllabs.be/index.php/SaSM_Lab_https

TSHARK

Using tshark to inspect HTTP traffic and saving the data to a file for later analysis can indeed be more manageable. Here’s a sparky rundown:

**Quick Summary:**

1. Capture HTTP traffic using tshark and save it to a file: `tshark -i <interface_name> -f 'tcp port 80 or tcp port 443' -w http_traffic.pcap`
2. Open the saved file for analysis: `tshark -r http_traffic.pcap`

find interface_name with `ifconfig`, it's probably `eth0`

Certainly! Let's dive into the details of those tshark commands:

### 1. Command: `tshark -r http.pcapng -Y http.server -T fields -e ip.src -e http.server | sort -u`

- `tshark`: Invokes the Tshark program, a terminal-based version of Wireshark.
- `-r http.pcapng`: Reads data from the specified file (`http.pcapng`). This file contains the packet data captured during your network session.
- `-Y http.server`: Uses a display filter to show only the packets where the HTTP server field is present. This usually filters for responses from servers.
- `-T fields`: Sets the output format to fields, allowing you to specify which fields of the packet you want to see.
- `-e ip.src -e http.server`: Specifies the fields to be displayed. `ip.src` shows the source IP address (where the packets are coming from), and `http.server` shows the HTTP server field (usually containing server information like Apache, nginx).
- `| sort -u`: Pipes the output to `sort -u`, which sorts the results and removes any duplicates (`-u` stands for unique).

This command basically extracts and lists unique pairs of source IP addresses and HTTP server types from the captured traffic.

### 2. Command: `tshark -r http.pcapng -Y http -T fields -e tcp.port | sort -u`

- `tshark`: Invokes the Tshark program.
- `-r http.pcapng`: Reads data from the specified file (`http.pcapng`).
- `-Y http`: Uses a display filter to show only HTTP packets. This filters both requests and responses related to HTTP.
- `-T fields`: Sets the output format to fields.
- `-e tcp.port`: Specifies that the TCP port field is to be displayed. This field shows which TCP ports were used in the communication (both source and destination).
- `| sort -u`: Pipes the output to `sort -u`, sorting the results and removing any duplicates.

This command is used to list all unique TCP ports that were involved in HTTP traffic, giving you insight into what ports were used during the browsing session on both the client and server sides.

Both commands are powerful for analyzing specific aspects of HTTP traffic, providing focused insights into the source of traffic and the transport layer details. If you're navigating the stormy seas of network analysis, these commands are your compass and map! 🌐✨
