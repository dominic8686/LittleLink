# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

LittleLink is a DIY self-hosted LinkTree alternative - a simple, static HTML/CSS website with 100+ branded button styles. It's built with vanilla HTML/CSS (no build process, no frameworks) and emphasizes performance, accessibility, and minimalism. The project achieved perfect 100/100 scores on Google PageSpeed Insights in all categories.

### Architecture Philosophy

- **Zero dependencies**: No npm, gulp, webpack, or build tools required
- **Static files only**: Pure HTML/CSS served by any web server
- **Custom CSS architecture**: Moved away from Skeleton CSS (v3.0.0) to custom, purpose-built modular CSS
- **Theme system**: Built-in `auto`, `light`, and `dark` themes using CSS custom properties
- **Accessibility-first**: WCAG contrast compliance, keyboard navigation, screen reader support

## File Structure

```
├── index.html              # Main page with all button examples (edit this for your site)
├── privacy.html            # Privacy policy template
├── css/
│   ├── reset.css          # CSS reset
│   ├── style.css          # Core layout, typography, themes
│   └── brands.css         # Brand-specific button styles (100+ buttons)
├── images/
│   ├── avatar.png         # User avatar (customize this)
│   └── icons/             # SVG icons for each brand button
├── fonts/                 # Custom fonts if needed
├── docker/                # Docker configuration for deployment
│   ├── Dockerfile
│   ├── compose.yaml
│   └── README.md
└── .github/
    ├── workflows/
    │   └── contrast-check.yml  # Automated WCAG contrast checking
    └── PULL_REQUEST_TEMPLATE.md
```

## Development Commands

### Local Development

No build process needed. Simply open `index.html` in a browser or use any local server:

```powershell
# Using Python
python -m http.server 8000

# Using PHP
php -S localhost:8000

# Using Node.js http-server (if installed)
npx http-server -p 8000
```

### Docker Development

```powershell
# Start development server (with live file mounting)
docker compose -f docker/compose.yaml up

# Start in background
docker compose -f docker/compose.yaml up -d

# Stop container
docker compose -f docker/compose.yaml down

# View logs
docker compose -f docker/compose.yaml logs -f
```

Access at http://localhost:8080

### Pre-commit Hooks (Optional)

If pre-commit is configured, it will run:
- Trailing whitespace fixes
- End-of-file fixes
- YAML validation
- String quote consistency
- Gitleaks (secret detection)
- Talisman (security checks)

Run manually:
```bash
pre-commit run --all-files
```

## CSS Architecture

### Three-layer CSS system

1. **reset.css** - Normalizes browser defaults
2. **style.css** - Core styles:
   - CSS custom properties for theming
   - Typography scale (1.25 modular scale)
   - Container/grid system
   - Button base styles
   - Theme definitions (auto/light/dark)
3. **brands.css** - Brand-specific button styles using CSS custom properties

### Theme System

Themes are set via the `<html>` class attribute in `index.html`:
- `theme-auto` - Respects system preference (default)
- `theme-light` - Forces light theme
- `theme-dark` - Forces dark theme

### CSS Custom Properties Pattern

Each button uses CSS variables for easy theming:

```css
.button-brandname {
  --button-text: #ffffff;
  --button-background: #000000;
  --button-border: 1px solid #ffffff;  /* Optional, for contrast */
}
```

### Contrast Requirements

Buttons must meet WCAG 4.5:1 contrast ratio for:
- Text vs button background
- Button vs light theme background (#ffffff)
- Button vs dark theme background (#121212)

Add a border stroke (`1px solid #000000` or `1px solid #ffffff`) when contrast fails.

## Adding a New Button

When adding a new brand button, follow this workflow:

### 1. Add CSS to `css/brands.css`

Insert alphabetically:

```css
/* Brand Name */
.button-brandname {
  --button-text: #FFFFFF;
  --button-background: #000000;
  --button-border: 1px solid #FFFFFF;  /* If needed for contrast */
}
```

For alternate styles, use `-alt` suffix:
```css
/* Brand Name Alt */
.button-brandname-alt {
  --button-text: #000000;
  --button-background: #FFFFFF;
  --button-border: 1px solid #000000;
}
```

### 2. Add Icon to `images/icons/`

- Format: SVG only
- Size: Optimized for 24x24px display
- Naming: `brandname.svg` or `brandname-alt.svg`

### 3. Add HTML to `index.html`

Insert alphabetically in the button stack:

```html
<!-- Brand Name -->
<a class="button button-brandname" href="#" target="_blank" rel="noopener" role="button">
  <img class="icon" aria-hidden="true" src="images/icons/brandname.svg" alt="Brand Name Logo">
  Brand Name
</a>
```

### 4. Test Contrast

Manually test with all three themes or use the [LittleLink Button Builder](https://builder.littlelink.io) contrast checker.

The GitHub Actions workflow will automatically check contrast on PRs that modify `css/brands.css`.

### 5. Review Checklist

Reference `.github/PULL_REQUEST_TEMPLATE.md` for complete contribution guidelines:
- Widespread brand recognition (niche brands go to LittleLink Extended)
- Follow official brand guidelines
- Icon clarity at 24x24px
- Alphabetical ordering in both CSS and HTML
- Proper capitalization in comments and code
- WCAG contrast compliance

## Deployment

### Quick Deploy Options

LittleLink can be deployed to static hosting with zero configuration:

- **Vercel**: One-click deploy button in README
- **Netlify**: One-click deploy button in README
- **Amplify**: One-click deploy button in README
- **GitHub Pages**: Enable in repository settings
- **Any static host**: Upload files via FTP/SFTP

### Docker Production Deployment

```bash
# Build production image
docker build -f docker/Dockerfile -t littlelink:latest .

# Run production container
docker run -d --name littlelink -p 80:80 --restart unless-stopped littlelink:latest

# With custom content volume
docker run -d --name littlelink -p 80:80 \
  -v /path/to/custom:/usr/share/nginx/html \
  littlelink:latest
```

See `docker/README.md` for multi-site deployment patterns.

## Customization

### Basic Customization

Edit `index.html`:

1. Change theme: Update `<html class="theme-auto">` to `theme-light` or `theme-dark`
2. Update title: `<title>Your Name</title>`
3. Replace avatar: Update `images/avatar.png` and `images/avatar@2x.png`
4. Edit description: Update the `<p>` tag content
5. Add/remove buttons: Keep only the buttons you need
6. Update button URLs: Change `href="#"` to your actual links

### Theme Color Customization

Edit CSS custom properties in `css/style.css`:

```css
:root {
  /* Customize base colors */
  --primary-color: #2457F5;
  --text-color: #1a1a1a;
  /* etc. */
}
```

### Button Customization

Modify brand colors in `css/brands.css` by changing the CSS custom properties for specific buttons.

## Related Projects

- **LittleLink Extended**: Community repository for niche/less common brand buttons
- **LittleLink Button Builder**: Visual tool for designing custom buttons with automatic contrast checking
- **LittleLink Server**: Docker implementation by Techno Tim
- **LinkStack**: PHP admin panel fork

## Version Information

Current version is tracked in `VERSION.md`. Major version 3.0.0 (Nov 2024) was a complete rebuild focusing on:
- Custom CSS architecture (removed Skeleton CSS)
- Enhanced accessibility (keyboard nav, screen readers, focus management)
- Modernized HTML structure
- Improved dark mode implementation

## Important Notes

- This is a static site - no backend, no database, no build process
- All button definitions use CSS custom properties for consistent theming
- Icons must be SVG format for scalability
- Accessibility is a core principle - maintain WCAG compliance
- Keep the repository minimal - niche brands belong in LittleLink Extended
