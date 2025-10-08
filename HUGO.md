# Hugo Migration Plan

## Project Overview
Migrate the Szarvas Pongrác portfolio site from iframe-based navigation to a modern Hugo static site generator with pure HTML templating (no Markdown).

## Current State Analysis

### Problems with Current Setup:
- **Iframe navigation**: Outdated, poor SEO, accessibility issues
- **Fragmented content**: Multiple HTML files with duplicated layout code
- **No templating**: Header/footer/navigation duplicated across files
- **Poor URLs**: Everything loads in iframe, no proper page URLs
- **Mobile unfriendly**: Iframe layout doesn't work well on mobile

### Current File Structure:
```
szarvasp/
├── index.html           # Main layout with iframe
├── nyitolap.html        # Home content (loaded in iframe)
├── eletrajz.html        # Biography content
├── akva.html            # Watercolor gallery
├── pasztell.html        # Pastel gallery
├── grafika.html         # Graphics gallery
├── olaj.html            # Oil paintings gallery
├── kmuveszet.html       # Art overview
├── fotok.html           # Photos
├── iterjesztes.html     # Educational content
├── csalad.html          # Family photos
├── stilus.css           # Main layout styles
├── tartalom.css         # Content styles
├── kepek/               # Image gallery folder
└── *.jpg, *.ico, *.bmp  # Various images and assets
```

## Target Hugo Structure

```
szarvasp-hugo/
├── hugo.yaml                    # Site configuration
├── content/
│   ├── _index.html             # Home page (nyitolap content)
│   ├── eletrajz.html           # Biography page
│   ├── kepzomuveszet/
│   │   ├── _index.html         # Art overview page
│   │   ├── akvarell.html       # Watercolors
│   │   ├── pasztell.html       # Pastels
│   │   ├── grafika.html        # Graphics
│   │   └── olaj.html           # Oil paintings
│   ├── fotok.html              # Photos
│   ├── ismeretterjesztes.html  # Educational content
│   └── csalad.html             # Family photos
├── layouts/
│   ├── _default/
│   │   ├── baseof.html         # Base layout (main structure)
│   │   ├── single.html         # Single page template
│   │   └── list.html           # Section listing template
│   ├── partials/
│   │   ├── head.html           # HTML head section
│   │   ├── header.html         # Site header with title/contact
│   │   ├── navigation.html     # Navigation menu
│   │   └── footer.html         # Site footer
│   └── index.html              # Homepage template
├── static/
│   ├── css/
│   │   └── styles.css          # Combined and optimized CSS
│   ├── images/
│   │   └── kepek/              # Image galleries
│   ├── ferde.bmp               # Background image
│   ├── lececske.jpg            # Header background
│   ├── alja.jpg                # Footer background
│   ├── separ.jpg               # Menu separator
│   ├── drot.ico                # Email icon
│   ├── szarvasok.jpg           # Home page image
│   ├── szarvasp.jpg            # Portrait image
│   ├── font.jpg                # Font background
│   └── favicon.ico             # Site favicon
└── public/                     # Generated static site (created by Hugo)
```

## Migration Steps

### Phase 1: Setup Hugo Project

1. **Install Hugo**
   ```bash
   # macOS
   brew install hugo
   
   # Or download from https://gohugo.io/installation/
   ```

2. **Create New Hugo Site**
   ```bash
   hugo new site szarvasp-hugo
   cd szarvasp-hugo
   git init
   ```

3. **Configure Hugo** (`hugo.yaml`)
   ```yaml
   baseURL: 'https://szarvaspongrac.hu'
   languageCode: 'hu'
   title: 'Szarvas Pongrác - Grafikus, Festő, Illusztrátor'
   
   markup:
     goldmark:
       renderer:
         unsafe: true  # Allow raw HTML in content
   
   params:
     description: 'Szarvas Pongrác grafikus, festő, illusztrátor honlapja'
     author: 'Szarvas Pongrác'
   ```

### Phase 2: Extract Layout Components

4. **Create Base Layout** (`layouts/_default/baseof.html`)
   - Extract main structure from `index.html`
   - Remove iframe, replace with content block
   - Convert static paths to Hugo functions
   - Add proper meta tags and SEO

5. **Create Partials**
   - `layouts/partials/head.html` - Meta tags, CSS links
   - `layouts/partials/header.html` - Site title and contact info
   - `layouts/partials/navigation.html` - Menu with Hugo URLs
   - `layouts/partials/footer.html` - Footer content

6. **Create Page Templates**
   - `layouts/_default/single.html` - Individual pages
   - `layouts/index.html` - Homepage
   - `layouts/_default/list.html` - Section pages (like art galleries)

### Phase 3: Convert Content

7. **Prepare Content Files**
   - Create HTML files in `content/` with front matter
   - Extract content from existing HTML files
   - Remove layout code, keep only content
   - Add proper front matter (title, description, etc.)

8. **Content Structure Example**
   ```html
   ---
   title: "Életrajz"
   description: "Szarvas Pongrác életrajza - grafikus, festő, illusztrátor"
   weight: 1
   ---
   
   <div class="content-wrapper">
     <p class="lapcim">Életrajz</p>
     <!-- Rest of content HTML -->
   </div>
   ```

### Phase 4: Assets and Styling

9. **Combine CSS Files**
   - Merge `stilus.css` and `tartalom.css`
   - Optimize and modernize CSS
   - Remove duplicate styles
   - Add responsive design improvements

10. **Copy Static Assets**
    - Move all images to `static/`
    - Organize gallery images in `static/images/kepek/`
    - Copy icons and background images

### Phase 5: URL Structure

11. **Design Clean URLs**
    ```
    Current (iframe):        New (Hugo):
    index.html               /
    index.html#eletrajz      /eletrajz/
    index.html#akva          /kepzomuveszet/akvarell/
    index.html#pasztell      /kepzomuveszet/pasztell/
    index.html#grafika       /kepzomuveszet/grafika/
    index.html#olaj          /kepzomuveszet/olaj/
    index.html#fotok         /fotok/
    index.html#iterjesztes   /ismeretterjesztes/
    index.html#csalad        /csalad/
    ```

### Phase 6: Gallery Implementation

12. **Modern Image Galleries**
    - Use Hugo's image processing
    - Implement responsive image galleries
    - Add lightbox functionality (optional)
    - Optimize images for web

13. **Gallery Template Example**
    ```html
    {{ range .Resources.ByType "image" }}
      {{ $image := .Resize "300x300" }}
      {{ $full := .Resize "1200x" }}
      <a href="{{ $full.RelPermalink }}">
        <img src="{{ $image.RelPermalink }}" 
             alt="{{ .Title }}" 
             loading="lazy">
      </a>
    {{ end }}
    ```

### Phase 7: Testing and Optimization

14. **Development Testing**
    ```bash
    hugo server -D  # Start development server
    # Test all pages and navigation
    # Check responsive design
    # Validate HTML and CSS
    ```

15. **Build and Deploy**
    ```bash
    hugo  # Generate static site in public/
    # Test the public/ folder
    # Deploy to web server
    ```

## Technical Details

### Hugo Template Functions Used:
- `{{ .Title }}` - Page title
- `{{ .Content }}` - Page content
- `{{ .Description }}` - Page description
- `{{ "path" | absURL }}` - Absolute URLs
- `{{ partial "name.html" . }}` - Include partials
- `{{ range .Resources }}` - Loop through page resources

### CSS Modernization:
- Combine `stilus.css` and `tartalom.css`
- Convert table layouts to CSS Grid/Flexbox
- Add responsive breakpoints
- Optimize for mobile devices
- Use modern CSS features

### SEO Improvements:
- Proper page titles and descriptions
- Structured data for artist portfolio
- Sitemap generation
- Open Graph meta tags
- Twitter Card meta tags

## Benefits of Migration

### Technical Benefits:
- **No iframe**: Proper page navigation
- **SEO-friendly**: Individual page URLs, proper meta tags
- **Mobile responsive**: Modern CSS layout
- **Fast loading**: Static files, optimized assets
- **Maintainable**: Templating system, no code duplication

### User Experience:
- **Better navigation**: Back button works, bookmarkable URLs
- **Accessibility**: Screen readers can navigate properly
- **Mobile friendly**: Responsive design
- **Fast**: No iframe loading delays

### Development Benefits:
- **Live reload**: Instant development feedback
- **Templating**: Shared layouts and components
- **Asset optimization**: Built-in minification and processing
- **Version control**: All content in Git
- **Easy deployment**: Static files work anywhere

## Deployment Options

1. **Netlify** (Recommended)
   - Automatic builds from Git
   - Free SSL certificate
   - Global CDN
   - Form handling

2. **Vercel**
   - Similar to Netlify
   - Excellent performance
   - Easy domain setup

3. **GitHub Pages**
   - Free hosting
   - GitHub Actions for builds
   - Custom domains supported

4. **Traditional Hosting**
   - Upload `public/` folder to any web server
   - Works with any hosting provider

## Timeline Estimate

- **Phase 1-2**: 1-2 hours (Setup and layout extraction)
- **Phase 3**: 2-3 hours (Content conversion)
- **Phase 4**: 1-2 hours (Assets and styling)
- **Phase 5-6**: 2-3 hours (URLs and galleries)
- **Phase 7**: 1-2 hours (Testing and deployment)

**Total**: ~8-12 hours for complete migration

## Next Steps

1. Install Hugo
2. Create new Hugo site
3. Start with base layout extraction
4. Convert one content page as proof of concept
5. Gradually migrate remaining content
6. Test and deploy

This migration will modernize the site while preserving all existing content and visual design.