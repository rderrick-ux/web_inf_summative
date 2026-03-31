 AniDiscover — Anime Finder

A smart anime discovery web app built with vanilla HTML, CSS, and JavaScript. Powered by the free [Jikan API](https://jikan.moe/) (no API key required).

## Features

- Search: anime by title
- Filter: by genre, minimum score, year, status, and type
- Sort: by top rated, popularity, members, or episodes
- Detail modal: — synopsis, score, rank, episodes, studio, duration, and Japanese title
- Recommendations: section inside each detail view
- Currently Airing: homepage section showing trending titles
- Fully responsive: mobile, tablet, and desktop

## How to Run Locally

No build tools or dependencies required.

```bash
git clone https://github.com/your-username/anidiscover.git
cd anidiscover
```

Open `index.html` directly in your browser, or use a local server:

```bash
# Python 3
python3 -m http.server 8080

# Node.js
npx serve .
```

Visit `http://localhost:8080`.

---

## API Used

**Jikan API v4** — https://api.jikan.moe/v4

- Free, no API key required
- Unofficial MyAnimeList API
- Endpoints used:
  - `GET /anime` — search and filter anime
  - `GET /anime/{id}/full` — full anime details
  - `GET /anime/{id}/recommendations` — related anime

Rate limit: ~3 requests/second.

 Deployment

  Prerequisites

- Two web servers (Web01, Web02) running Nginx
- One load balancer (Lb01)

Step 1 — Upload to Web01 and Web02

```bash
scp -r ./anidiscover/ user@web01:/var/www/html/anidiscover/
scp -r ./anidiscover/ user@web02:/var/www/html/anidiscover/
```

Step 2 — Configure Nginx on each web server

```nginx
server {
    listen 80;
    server_name _;
    root /var/www/html/anidiscover;
    index index.html;
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

```bash
sudo systemctl restart nginx
```

Step 3 — Configure Load Balancer (Lb01)

```nginx
upstream anime_app {
    server web01_ip;
    server web02_ip;
}

server {
    listen 80;
    server_name your-domain.com;
    location / {
        proxy_pass http://anime_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

```bash
sudo systemctl restart nginx
```

### Step 4 — Test

```bash
curl http://your-load-balancer-ip

# Test load distribution
for i in {1..10}; do curl -s -o /dev/null -w "%{http_code}\n" http://your-load-balancer-ip; done
```

## Project Structure

```
anidiscover/
├── index.html      # Single-file app (HTML + CSS + JS)
├── .gitignore
└── README.md
```

## Demo

https://www.youtube.com/watch?v=VtkBhh6nyyE
