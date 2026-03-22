# TinyAuth Deployment with Docker Compose

This repository provides a simple and ready-to-use setup for deploying **TinyAuth v4** behind **Traefik**, enabling lightweight authentication using the **ForwardAuth** mechanism.

## ✨ Features

- Deploy TinyAuth via Docker Compose
- Seamless integration with Traefik
- HTTPS exposure using a local domain (`tinyauth.test.local`)
- ForwardAuth middleware for protecting services
- Support for bcrypt password hashing
- Custom background image
- Environment-based configuration
- Debug logging enabled

---

## 📁 Project Structure

```
.  
├── docker-compose.yml  
├── .env                # Contains USERS and SECRET  
├── wallpaper.jpg       # Custom background image

```

---

##  How It Works

### 👉 TinyAuth Role

TinyAuth acts as an authentication gateway using Traefik's **ForwardAuth** feature.  
Incoming requests are intercepted and validated before reaching your protected services.

### 👉 Traefik Integration

TinyAuth is exposed through Traefik with the following configuration:

- Domain: `https://tinyauth.test.local`
- EntryPoint: `websecure` (HTTPS)
- ForwardAuth endpoint:

```
http://tinyauth:3000/api/auth/traefik
```

---

##  Environment Variables (.env)

Example:

```
USERS=admin:password  
SECRET=your32charsecretkey
```

---

## Generating a Secure SECRET

Generate a random 32-character secret:

```
openssl rand -base64 32 | tr -dc 'a-zA-Z0-9' | head -c 32
```

---

## Generating User Passwords (bcrypt)

For better security, you can store passwords as **bcrypt hashes**.

### 🛠 Method using Docker

```bash
docker run --rm -it debian:trixie-backports  
apt update  
apt install whois -y  
mkpasswd --method=bcrypt

```

Then:
- Enter your password interactively
- Copy the generated hash

Example output:

```
$2y$05$6I4oC8nE9zl9VcM7xWXY5.5vgeY45Yy0s4O36N0qjra75qFQeCiey
```

Use it in `.env`:

```
USERS=admin:$2y$05$6I4oC8nE9zl9VcM7xWXY5...
```

---

## Custom Background

You can customize the login page background:

1. Replace:

```
wallpaper.jpg
```

2. Restart the container:

```
docker compose restart
```

---

## Access TinyAuth

Open in your browser:

```
https://tinyauth.test.local
```

⚠️ If you're using a self-signed certificate, your browser will show a warning.

---

## Protecting a Service with TinyAuth

To secure a service using TinyAuth, add this label:

```yaml
labels:  
  - traefik.http.routers.myapp.middlewares=tinyauth
```


All requests to the service will require authentication.

---

## Notes

- Users must be defined as:

```
username:password_or_hash
```
   
- Multiple users are separated by commas:

```
user1:hash1,user2:hash2
```
   
- The `traefik_network` must already exist (created by your Traefik stack)

---

## License

This project is provided for personal use, testing, and development purposes.