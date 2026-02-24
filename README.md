# The Internet of Stuff and Things

Your Blogger blog has been successfully migrated to Jekyll!

## 📂 Structure

```
my-jekyll-blog/
├── _posts/              # All 40 blog posts (Markdown)
├── assets/
│   └── images/          # 435 downloaded images
├── _config.yml          # Jekyll configuration
├── Gemfile              # Ruby dependencies
└── README.md            # This file
```

## 🚀 Quick Start (Local Testing)

### Prerequisites
- Ruby 2.7+ installed
- Jekyll installed

### Setup & Run Locally

```bash
# Navigate to the blog directory
cd my-jekyll-blog

# Install dependencies
bundle install

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000 in your browser
```

The blog will auto-rebuild when you edit files.

## 📱 Deploy to GitHub Pages

### Step 1: Create GitHub Repository

1. Go to [github.com/new](https://github.com/new)
2. Name it: `yourusername.github.io` (replace with your actual username)
3. Make it public
4. Click "Create repository"

### Step 2: Push Your Blog

```bash
cd my-jekyll-blog

# Initialize git repo
git init

# Add GitHub as remote
git remote add origin https://github.com/yourusername/yourusername.github.io.git
git branch -M main

# Add all files
git add .
git commit -m "Initial commit: Blogger migration"

# Push to GitHub
git push -u origin main
```

### Step 3: Wait & Visit

- GitHub will auto-run Jekyll and build your site (takes ~1-2 minutes)
- Visit: `https://yourusername.github.io`
- Done! 🎉

## 🔧 Customization

### Change Site Title
Edit `_config.yml`:
```yaml
title: Your New Title
description: Your new description
url: https://yourusername.github.io
```

### Change Theme
Popular Jekyll themes:
- `minima` (default, clean)
- `jekyll-theme-cayman`
- `jekyll-theme-slate`
- `jekyll-theme-midnight`

Update `_config.yml`:
```yaml
theme: jekyll-theme-cayman
```

### Add Custom CSS
Create `assets/css/style.css` and link it in `_config.yml`.

### Enable Comments (Optional)
Add Disqus or GitHub Discussions. Update `_config.yml` and theme.

## 📝 Writing New Posts

1. Create file: `_posts/YYYY-MM-DD-title.md`
2. Add frontmatter:
```markdown
---
layout: post
title: My New Post
date: 2026-02-24
author: Igor
---

Your content here...
```

3. Commit and push:
```bash
git add _posts/
git commit -m "New post: My New Post"
git push
```

Site updates automatically in ~1 minute.

## ⚠️ Known Issues

**9 Images Failed to Download:**
- Some external images (YouTube, kringlecon.com) couldn't be downloaded
- These posts still have links to external URLs
- To fix: Manually download and add to `/assets/images/` and update the Markdown

## 🎨 Customization Ideas

1. **Add Navigation Menu** — Edit theme or create custom header
2. **Add Search** — Use Jekyll Search or Google Custom Search
3. **SEO Optimization** — Add meta tags for better Google ranking
4. **Analytics** — Add Google Analytics or Plausible
5. **Custom Domain** — Point your domain to GitHub Pages
6. **Subscribe Button** — Add email subscription via Substack/Mailchimp

## 📚 Resources

- [Jekyll Official Docs](https://jekyllrb.com/)
- [GitHub Pages Documentation](https://pages.github.com/)
- [Markdown Guide](https://www.markdownguide.org/)
- [Minima Theme](https://github.com/jekyll/minima)

## ✅ Migration Stats

- **Posts converted:** 40
- **Images downloaded:** 435 / 444 (98%)
- **Total image size:** 1.4 MB
- **Date range:** 2016-2023

---

**Next Step:** Follow "Deploy to GitHub Pages" above to go live! 🚀
