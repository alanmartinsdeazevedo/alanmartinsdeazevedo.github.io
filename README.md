# Alan Martins - Portfolio & Knowledge Base

A clean, minimalist static website combining a personal portfolio with a technical knowledge base. Built with Jekyll and hosted on GitHub Pages.

## 🌐 Live Site

Visit: [https://alanmartins.github.io](https://alanmartins.github.io)

## 🚀 Features

- **Portfolio**: Showcase your projects and skills
- **Knowledge Base**: Organized technical guides and tutorials
- **Dark Mode**: Automatic theme switching with user preference
- **Responsive Design**: Works on all devices
- **Search**: Filter knowledge base articles
- **Fast Loading**: Static site generation for optimal performance
- **SEO Friendly**: Built-in SEO optimization

## 📁 Project Structure

```
.
├── _config.yml           # Jekyll configuration
├── _layouts/             # Page layouts
│   ├── default.html      # Base layout
│   └── article.html      # Knowledge base article layout
├── _includes/            # Reusable components
│   ├── header.html       # Site header and navigation
│   └── footer.html       # Site footer
├── _kb/                  # Knowledge base articles (markdown)
│   ├── nodejs-ubuntu-install.md
│   ├── git-workflow-guide.md
│   └── ...
├── assets/
│   ├── css/
│   │   └── style.css     # Main stylesheet
│   └── js/
│       └── main.js       # JavaScript functionality
├── index.html            # Homepage
├── knowledge-base.html   # Knowledge base index
├── Gemfile               # Ruby dependencies
└── README.md             # This file
```

## 🛠️ Local Development

### Prerequisites

- Ruby 3.x or later
- Bundler
- Git

### Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/alanmartins/alanmartins.github.io.git
   cd alanmartins.github.io
   ```

2. **Install dependencies**:
   ```bash
   bundle install
   ```

3. **Run local server**:
   ```bash
   bundle exec jekyll serve
   ```

4. **Open in browser**:
   ```
   http://localhost:4000
   ```

### Development with Live Reload

```bash
bundle exec jekyll serve --livereload
```

Changes to files will automatically refresh the browser.

## ✍️ Adding Content

### Adding a New Knowledge Base Article

1. **Create a new markdown file** in `_kb/` directory:
   ```bash
   touch _kb/my-new-article.md
   ```

2. **Add front matter** at the top of the file:
   ```markdown
   ---
   layout: article
   title: Your Article Title
   description: Brief description of the article
   category: Server Setup
   date: 2025-10-28
   ---

   ## Your Content Here

   Write your article content using Markdown...
   ```

3. **Add the article link** to `knowledge-base.html`:
   ```html
   <li><a href="{{ '/kb/my-new-article/' | relative_url }}">Your Article Title</a></li>
   ```

### Article Categories

Use one of these categories for consistency:
- `Development Environment`
- `Server Setup`
- `Tools & CLI`
- `Quick References`
- `Best Practices`

### Markdown Features

The articles support:

**Code blocks with syntax highlighting:**
````markdown
```bash
sudo apt update
```
````

**Lists:**
```markdown
- Item 1
- Item 2
  - Nested item

1. First
2. Second
```

**Tables:**
```markdown
| Header 1 | Header 2 |
|----------|----------|
| Cell 1   | Cell 2   |
```

**Blockquotes:**
```markdown
> Important note or tip
```

**Links:**
```markdown
[Link text](https://example.com)
```

### Adding Projects

Edit `index.html` to add your projects in the projects section:

```html
<div class="project-card">
    <h3>Your Project Name</h3>
    <p>Description of your project...</p>
    <div class="project-links">
        <a href="https://demo-url.com" class="btn btn-secondary">View Demo</a>
        <a href="https://github.com/user/repo" class="btn btn-secondary">GitHub</a>
    </div>
</div>
```

### Updating Skills

Edit the skills section in `index.html`:

```html
<div class="skill-card">
    <h3>Skill Category</h3>
    <p>Technologies, tools, languages...</p>
</div>
```

## 🎨 Customization

### Colors and Theme

Edit CSS variables in `assets/css/style.css`:

```css
:root {
    --accent-color: #0066cc;  /* Primary accent color */
    --link-color: #0066cc;    /* Link color */
    /* ... more variables */
}
```

### Site Information

Update `_config.yml`:

```yaml
title: Your Name
description: Your description
author:
  name: Your Name
  email: your.email@example.com
  github: yourusername
```

### Navigation

Edit `_includes/header.html` to modify navigation links.

## 🚢 Deployment

### GitHub Pages (Automatic)

The site automatically deploys to GitHub Pages when you push to the `main` branch.

1. **Enable GitHub Pages**:
   - Go to repository Settings
   - Navigate to Pages
   - Source: GitHub Actions

2. **Push your changes**:
   ```bash
   git add .
   git commit -m "Update content"
   git push origin main
   ```

3. **Check deployment**:
   - Go to Actions tab
   - Monitor deployment progress
   - Visit your site at `https://yourusername.github.io`

### Manual Build

To build the site locally:

```bash
bundle exec jekyll build
```

Output will be in `_site/` directory.

## 🧪 Testing

### Check for Broken Links

```bash
bundle exec jekyll build
bundle exec htmlproofer ./_site --disable-external
```

### Validate HTML

Use [W3C Validator](https://validator.w3.org/) or:

```bash
bundle exec jekyll build
html-prover _site
```

## 📱 Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Mobile browsers

## 🤝 Contributing

This is a personal site, but feel free to fork and adapt for your own use!

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🔗 Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Liquid Template Language](https://shopify.github.io/liquid/)

## 📧 Contact

- GitHub: [@alanmartins](https://github.com/alanmartins)
- Email: your.email@example.com

## 🙏 Acknowledgments

Design inspired by [thunderysteak.github.io](https://github.com/thunderysteak/thunderysteak.github.io)

---

Built with ❤️ using Jekyll and GitHub Pages
