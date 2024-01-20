# What is HTTP

HyperText Transfer Protocol

## Important folders

    /var/www
        /var/www/html
    /etc/apache2/sites-available
    /etc/apache2/apache2.conf
    /var/log/apache2
    /var/www/ferre-verheyen.sasm.uclllabs.be/public_html/.htaccess

    sudo htpasswd -c /etc/apache2/.htpasswd username

- AuthType Basic
  AuthName "Restricted Content"
  AuthUserFile /etc/apache2/.htpasswd
  Require valid-user

## Assignments

### create server

Adding a new website (virtual host) on an Apache web server involves a few key steps:

1. **Create a Directory for Your Website:**
   Create a directory where your website's files will reside. For example:

   ```bash
   sudo mkdir -p /var/www/ferre-verheyen.sasm.uclllabs.be/public_html
   ```

2. **Set Permissions:**
   Set appropriate permissions for the directory:

   ```bash
   sudo chown -R www-data:www-data /var/www/ferre-verheyen.sasm.uclllabs.be/public_html
   sudo chmod -R 755 /var/www
   ```

3. **Create a Sample `index.html` Page (Optional):**
   To test, create a simple HTML file in this directory:

   ```bash
   echo "<html><body><h1>Hello from ferre-verheyen.sasm.uclllabs.be</h1></body></html>" | sudo tee /var/www/ferre-verheyen.sasm.uclllabs.be/public_html/index.html
   ```

4. **Create a New Virtual Host File:**
   Copy the default config file to create a new virtual host:

   ```bash
   sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/ferre-verheyen.sasm.uclllabs.be.conf
   ```

5. **Edit the Virtual Host File:**
   Edit the new virtual host file (`ferre-verheyen.sasm.uclllabs.be.conf`) to point to your website's directory and set the `ServerName`:

   ```bash
   sudo nano /etc/apache2/sites-available/ferre-verheyen.sasm.uclllabs.be.conf
   ```

   Inside the file, change the `DocumentRoot` and add `ServerName`:

   ```apache
   <VirtualHost *:80>
       ServerAdmin admin@ferre-verheyen.sasm.uclllabs.be
       ServerName ferre-verheyen.sasm.uclllabs.be
       ServerAlias www.ferre-verheyen.sasm.uclllabs.be
       DocumentRoot /var/www/ferre-verheyen.sasm.uclllabs.be/public_html
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

6. **Enable the Virtual Host:**
   Enable the new virtual host file:

   ```bash
   sudo a2ensite ferre-verheyen.sasm.uclllabs.be.conf
   ```

7. **Restart Apache:**
   Restart Apache to apply the changes:

   ```bash
   sudo systemctl restart apache2
   ```

8. **Test the Website:**
   Open a browser and go to `http://ferre-verheyen.sasm.uclllabs.be`. You should see the content of your `index.html`.

### setting up password protection

Sure thing! To add password protection to your Apache vhosts, you can follow these steps:

Add this to your virtual host in: /etc/apache2/sites-available/

      <Directory "/var/www/your-website">
         AuthType Basic
         AuthName "Restricted Area"
         AuthUserFile /etc/apache2/.htpasswd
         Require valid-user
      </Directory>

1. First, make sure you have the Apache `mod_auth` module enabled. You can check this by running the following command:

   ```bash
   sudo a2enmod auth_basic
   ```

   If it's already enabled, you can skip this step.

2. Now, you'll need to create a password file that will store usernames and passwords for authentication. You can use the `htpasswd` command to create this file. Here's an example command:

   ```bash
   sudo htpasswd -c /etc/apache2/.htpasswd username
   ```

   Replace `username` with the username you want to create, and you'll be prompted to set a password.

3. Next, you'll need to configure your virtual host to require authentication. Open your vhost configuration file using a text editor. For example:

   ```bash
   sudo nano /etc/apache2/sites-available/your-vhost.conf
   ```

   Add the following lines within the `<VirtualHost>` section of your vhost configuration, replacing `/path/to/.htpasswd` with the actual path to your password file:

   ```apache
   <Directory "/path/to/your/directory">
       AuthType Basic
       AuthName "Restricted Content"
       AuthUserFile /etc/apache2/.htpasswd
       Require valid-user
   </Directory>
   ```

   This code sets up basic authentication for the specified directory.

4. Save the configuration file and exit your text editor.

5. Finally, restart Apache to apply the changes:

   ```bash
   sudo systemctl restart apache2
   ```

That's the basic overview! If you'd like a more detailed explanation or have any questions about these steps, feel free to ask.

### specific vhost loggin:

ErrorLog \${APACHE_LOG_DIR}/www1.ferre-verheyen.sasm.uclllabs.be_error.log
CustomLog ${APACHE_LOG_DIR}/www1.ferre-verheyen.sasm.uclllabs.be_access.log combined

## Handy stuff

certbot --staging --apache -d "pieter.sasm.uclllabs.be, secure.pieter.sasm.uclllabs.be, supersecure.pieter.sasm.uclllabs.be
