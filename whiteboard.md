# Installing and Configuring the Whiteboard (Excalidraw)

### Step 1: Install Excalidraw Backend

```bash
cd /opt
git clone https://github.com/jitsi/excalidraw-backend.git
cd excalidraw-backend
npm install
npm run build
npm start
```

### Step 2: Keep Excalidraw Running with PM2

```bash
sudo npm install -g pm2
pm2 start npm --name "excalidraw-backend" -- start
```

### Step 3: Set Up Nginx Reverse Proxy

```bash
sudo nano /etc/nginx/sites-available/excalidraw
```

Paste the following configuration:

```nginx
server {
    server_name whiteboard.meet.domain.tld;

    location / {
        proxy_pass http://localhost:5000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable the site and reload Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/excalidraw /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### Step 4: Enable HTTPS with Certbot

```bash
sudo certbot --nginx -d whiteboard.meet.domain.tld
```

### Step 5: Configure Jitsi Meet to Use the Whiteboard

```bash
sudo nano /etc/jitsi/meet/meet.domain.tld-config.js
```

Add the whiteboard settings inside the configuration object:

```javascript
whiteboard: {
    enabled: true,
    collabServerBaseUrl: 'https://whiteboard.meet.domain.tld'
},
```

Restart Jitsi services:

```bash
sudo systemctl restart jitsi-videobridge2 prosody jicofo
```
