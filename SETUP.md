# Getting this online — GitHub Pages + your domain

You've got two files: `index.html` and an `img/` folder. Keep them in that
same relative structure everywhere they go.

## 1. Put it on GitHub

1. Create a free account at github.com if you don't have one.
2. Click **New repository**. Name it whatever you like (e.g. `field-notes`).
   Keep it **Public** (required for free GitHub Pages) and don't add a
   README.
3. On the empty repo page, click **uploading an existing file**.
4. Drag in `index.html` and the whole `img` folder together. Commit.

## 2. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages**.
2. Under "Build and deployment", set **Source** to `Deploy from a branch`.
3. Branch: `main`, folder: `/ (root)`. Save.
4. Wait ~1 minute, then GitHub gives you a URL like
   `https://yourusername.github.io/field-notes/` — that's your site, live.

## 3. Point your domain at it

You said you already own a domain. Two ways to connect it, pick based on
whether you want the *root* domain (`yourdomain.com`) or a *subdomain*
(`gallery.yourdomain.com`) to point here.

**Subdomain (easier, recommended if you're not sure):**
1. In your domain registrar's DNS settings, add a **CNAME record**:
   - Host: `gallery` (or whatever subdomain you want)
   - Value: `yourusername.github.io`
2. Back in GitHub → Settings → Pages → **Custom domain**, enter
   `gallery.yourdomain.com` and save.

**Root/apex domain (`yourdomain.com` with no prefix):**
1. Add four **A records** at your registrar, all pointing the root (`@`) to
   GitHub's IPs:
   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```
2. Same as above — enter your domain under GitHub → Settings → Pages →
   Custom domain.

Either way, GitHub will auto-provision an SSL certificate for you within a
few minutes to an hour (tick "Enforce HTTPS" once it's available).

DNS changes can take anywhere from a few minutes to 24 hours to propagate,
depending on your registrar.

## 4. Adding new photos later

No build step — it's plain HTML/CSS/JS. To add something:

1. Drop the new image file into the `img/` folder.
2. Open `index.html` in any text editor (VS Code, Notepad, TextEdit — doesn't
   matter). GitHub also lets you edit files directly in the browser: open the
   file in your repo, click the pencil icon.
3. Find the `DATA` array near the bottom of the `<script>` section — it's a
   list of entries like this:
   ```js
   {id:11, img:'img/your-new-photo.jpg', type:'photo', category:'street-art',
    artist:null, title:'Your title', location:'Shoreditch, London',
    notes:"Your notes here.", tags:['tag1','tag2']},
   ```
4. Copy one existing entry, paste it as a new line, change the `id` to the
   next number, and fill in your own details.
5. Save, commit/upload the change on GitHub. The live site updates within a
   minute or two, automatically — no redeploy step needed.

That's the whole workflow going forward: edit `DATA`, save, done.
