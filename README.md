# Photography Portfolio

A minimal, file-based photography portfolio. No build step, no database, no backend — just static HTML, CSS, and a single JSON file for content.

## Features

- Horizontal scroll strip per project with smooth lerp scrolling and drag support
- Lightbox with keyboard navigation
- Admin panel (`admin.html`) for editing content via the [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API) — works entirely in the browser, no server needed
- Mobile-responsive vertical layout
- Clean URLs via `.htaccess` rewrites

## Getting Started

### 1. Set up your content

Copy the example data file and add your own content:

```bash
cp data/projects.example.json data/projects.json
```

Edit `data/projects.json` with your name, projects, and image paths. The structure is documented in the example file.

### 2. Add your images

Create an `images/` folder with one subfolder per project:

```
images/
  your-project-id/
    cover.jpg
    001.jpg
    002.jpg
    ...
  about.jpg        ← optional portrait for the About page
```

### 3. Run locally

Any static file server works. With Node.js installed:

```bash
npx serve .
```

Then open [http://localhost:3000](http://localhost:3000).

### 4. Edit content with the Admin panel

Open `http://localhost:3000/admin` in your browser, click **Open Folder**, and select the project root. The admin panel lets you:

- Edit site name and meta description
- Add, remove, and reorder projects
- Add images and text blocks to projects
- Set cover images and their focal point

After editing, click **Save** to write changes to `data/projects.json`.

> **Note:** The admin panel only works locally. It uses the browser's File System Access API, which requires a user gesture to grant write permissions and does not work over a remote URL. **Do not deploy `admin.html` to a public server.**

## Deploying

### Apache (shared hosting)

Upload all files except:

- `admin.html` (local editing tool, should stay private)
- `data/projects.json` (your real content — upload manually or via FTP)
- `images/` (your real photos — upload manually or via FTP)
- `.claude/` (development tooling)
- `node_modules/`

The included `.htaccess` handles URL rewrites for clean paths (`/work/project-id`, `/about`).

### Netlify / Vercel

The `.htaccess` file has no effect on these platforms. You will need a redirect configuration instead:

**Netlify** — create a `_redirects` file:
```
/work/*   /project.html   200
/about    /about.html     200
/admin    /admin.html     200
```

**Vercel** — add to `vercel.json`:
```json
{
  "rewrites": [
    { "source": "/work/:id", "destination": "/project.html" },
    { "source": "/about",    "destination": "/about.html"   },
    { "source": "/admin",    "destination": "/admin.html"   }
  ]
}
```

> The `serve.json` file is only used by `npx serve` during local development.

## Project JSON structure

```json
{
  "site": {
    "name": "Your Name",
    "description": "Short bio used for meta description and social sharing."
  },
  "about": {
    "text": "Bio text. Double line break creates a new paragraph.",
    "image": "images/about.jpg",
    "links": [
      { "label": "Instagram", "url": "https://www.instagram.com/yourhandle" },
      { "label": "Mail",      "url": "mailto:hello@example.com" }
    ]
  },
  "projects": [
    {
      "id": "project-slug",
      "slug": "optional-custom-url-slug",
      "title": "Project Title",
      "year": "2024",
      "category": "Ongoing",
      "description": "Text shown in the intro panel of the project page.",
      "cover": "images/project-slug/cover.jpg",
      "coverPosition": "center center",
      "imageFolder": "images/project-slug",
      "images": [
        { "src": "images/project-slug/001.jpg", "caption": "Optional caption" },
        { "type": "text", "heading": "Optional heading", "body": "Text block." }
      ]
    }
  ]
}
```

### Field reference

| Field | Required | Notes |
|---|---|---|
| `site.name` | Yes | Displayed in header and page title |
| `site.description` | No | Meta description for search and social sharing |
| `project.id` | Yes | Used as URL slug and default image folder name |
| `project.slug` | No | Overrides the URL (e.g. `/work/custom-slug`) |
| `project.imageFolder` | No | Overrides the image folder path (default: `images/{id}`) |
| `project.category` | No | Groups projects on the home page |
| `project.coverPosition` | No | CSS `object-position` value for cover crop, e.g. `top center` |

## Browser support

Requires a modern browser (Chrome, Firefox, Safari, Edge). The admin panel additionally requires a Chromium-based browser (Chrome, Edge, Arc) for the File System Access API.
