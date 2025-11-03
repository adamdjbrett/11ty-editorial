# PR #2 Summary and Author.njk Documentation

## Overview
This document summarizes the changes made in Pull Request #2 (merged on November 3, 2025) and provides a detailed explanation of the author.njk template setup.

---

## PR #2: Complete Project Setup - Summary of Changes

PR #2 introduced a comprehensive Eleventy (11ty) static site generator setup with Bootstrap styling, featuring a multi-author blog system. The PR added **61 files** with **5,044 additions**, transforming the repository from an empty state to a fully functional editorial/blog platform.

### Major Components Added

#### 1. **Core Configuration Files**
- **`.editorconfig`**: Editor configuration for consistent coding style
- **`.gitattributes`**: Git attributes for line ending normalization
- **`.gitignore`**: Excludes node_modules, build artifacts, and system files
- **`.nvmrc`**: Specifies Node.js version requirement
- **`package.json`**: Project dependencies and build scripts
- **`eleventy.config.js`**: Main Eleventy configuration (240 lines)
- **`netlify.toml`** & **`vercel.json`**: Deployment configuration

#### 2. **Data Layer** (`_data/`)
- **`authors.yaml`**: Central author registry with keys, names, bios, and images
  - Example authors: `john_dhoe`, `jon_jones`
- **`metadata.yaml`**: Site-wide metadata (title, description, social links, etc.)
- **`eleventyDataSchema.js`**: Data validation schema

#### 3. **Configuration** (`_config/`)
- **`filters.js`**: Custom Eleventy filters including:
  - Date formatting (`readableDate`, `htmlDateString`)
  - Array manipulation (`limit`, `head`)
  - Tag filtering (`filterTagList`)
  - Alphabetical sorting (`sortAlphabetically`)

#### 4. **Templates/Layouts** (`_includes/`)
- **Base Layouts**:
  - `base.njk`: Root template with HTML structure
  - `home.njk`: Homepage layout
  - `blog.njk`: Blog listing layout
  - `post.njk`: Individual post layout (80 lines)
  - **`authors.njk`**: Author profile page layout (49 lines) ⭐

- **Partials**:
  - `content.njk`: Main content wrapper
  - `home_hero.njk`: Homepage hero section
  - `home_section.njk`: Homepage content sections
  - `nav_desktop.njk` & `nav_mobile.njk`: Navigation components
  - `post_list.njk`: Blog post listing component
  - `searchmodal.njk`: Search interface
  - `seo.njk`: SEO meta tags
  - `sidebar.njk`: Sidebar component (53 lines)

- **Collection Templates**:
  - `postslist.njk`: Posts collection template

#### 5. **Content** (`content/`)
- **Pages**:
  - `index.md`: Homepage content
  - `about.md`: About page
  - `blog.md`: Blog listing page
  - **`authors.njk`**: Authors listing page
  - `tags.njk`: Tag listing page
  - `404.md`: Error page
  - `pages/elements.md`: UI elements showcase (180 lines)
  - `pages/humans.txt.njk` & `pages/robots.txt.njk`: Metadata files

- **Blog Posts** (`content/blog/`):
  - `firstpost.md`, `secondpost.md`, `thirdpost.md`, `blackcat.md`
  - `blog.11tydata.js`: Collection configuration

- **Author Profiles** (`content/authors/`):
  - **`authors.11tydata.js`**: Sets layout and tags for all author pages
  - `john_dhoe.md`: John Doe's profile with social links and bio
  - `jon_jones.md`: Jon Jones' profile with social links and bio

- **Special Pages**:
  - `sitemap.xml.njk`: XML sitemap
  - `tag-pages.njk`: Dynamic tag pages
  - `feed/.virtual`: Feed configuration
  - `feed/pretty-atom-feed.xsl`: Atom feed styling

#### 6. **Assets**
- **CSS** (`css/` & `public/css/`):
  - `bs.css`: Bootstrap framework
  - `index.css`: Custom styles (317 lines) with sidebar, navigation, and layout styles
  - `pagefinds-ui.css`: Search UI styling

- **JavaScript** (`public/js/`):
  - `bs.js`: Bootstrap bundle with menu toggle functionality (27 lines)

- **Images** (`public/img/`):
  - Empty directory with `.gitkeep` for future images

- **Backup Files** (`public/backup/`):
  - `authors.json` & `authors.njk`: Legacy author templates

#### 7. **GitHub Actions** (`.github/workflows/`)
- `gh-pages.yml.sample`: Sample workflow for GitHub Pages deployment (48 lines)

---

## The Author.njk Template System: Complete Explanation

The multi-author system in this project is a sophisticated setup involving multiple interconnected files. Here's the complete architecture:

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Multi-Author System                       │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
   Data Layer          Template Layer         Content Layer
        │                     │                     │
  _data/authors.yaml   _includes/authors.njk   content/authors/
        │                     │                     │
        ├─ author entries     ├─ profile layout     ├─ *.md files
        └─ key, name, bio     └─ post filtering     └─ front matter
```

### 1. **Data Layer: `_data/authors.yaml`**

This file serves as the **central author registry**:

```yaml
- key: john_dhoe          # Unique identifier (becomes URL slug)
  name: John Doe          # Display name
  bio: This is me john... # Author biography
  image: https://...      # Profile image URL

- key: jon_jones
  name: Jon Bon Jones
  bio: This is me jon...
  image: https://...
```

**Purpose**: 
- Central source of truth for all authors
- Provides basic metadata accessible throughout the site
- Keys are used to link posts to authors

### 2. **Template Layer: `_includes/authors.njk`**

This is the **author profile page layout** (49 lines):

```nunjucks
---
layout: base.njk
---
<div class="col-md-10 mx-auto">
{% include "partials/nav_desktop.njk" %}
<div class="container-fluid">
<div class="col-md-12 p-3 p-md-5">

{# Get author key from the page filename (fileSlug) #}
{% set authorKey = page.fileSlug %}

{# Filter all posts by this author #}
{% set postsByAuthor = collections.all | byAuthor(authorKey) %}

{% if postsByAuthor.length %}
  {# Display author profile information #}
  <div class="text-center text-secondary">
    <img class="img-thumb rounded-circle mb-3" 
         alt="{{title}}" 
         width="150" 
         height="150" 
         style="object-fit:cover;" 
         src="{{image}}"/>
    <h1><strong><span class="none text-dark">{{title}}</span></strong></h1>
    <h2 class="lead text-uppercase mb-3">{{description}}</h2>
    
    {# Post count and social links #}
    <p>
      {{ postsByAuthor.length }} Posts |
      {% if social %}{% for s in social %}
        <a href="{{s.url}}" title="{{s.title}}">
          <i class="{{s.icon}} me-2"></i>
        </a>
      {% endfor %}{% endif %}
    </p>
    
    {# Contact buttons #}
    <p>{% for b in button %}
      <a href="{{b.url}}" class="btn btn-pink me-2 text-white">
        {{b.title}}
      </a>
    {% endfor %}</p>
  </div>
</div>
<hr/>

{# Display all posts by this author #}
<div class="row mb-3">
  {% for b in postsByAuthor | reverse %}
    <div class="col-md-4 col-lg-4 p-3 small p-md-5"> 
      <div class="img-zoom-container">
        <a href="{{b.url}}" title="{{b.data.title}}">
          <img class="img-post rounded" 
               width="100%" 
               height="100%" 
               alt="{{b.data.title}}" 
               src="{{b.data.image or metadata.image}}"/>
        </a>
      </div>
      <h3>{{b.data.title}}</h3>
      <p>{{b.data.description}}</p>
      <p>
        <a href="{{b.url}}" class="btn btn-sm btn-outline-pink p-2">
          Learn More
        </a>
      </p>
    </div>
  {% endfor %}
{% else %}
  <p>Nothing Post by **{{ authorKey }}**.</p>
{% endif %}
</div>
</div>
</div>
```

**Key Features**:
1. **Dynamic Author Key**: Uses `page.fileSlug` to get the author identifier from the filename
2. **Post Filtering**: Uses the `byAuthor` custom filter to get all posts by this author
3. **Profile Display**: Shows author image, name, description, social links, and contact buttons
4. **Posts Grid**: Displays all posts by the author in a responsive grid layout
5. **Fallback**: Shows a message if the author has no posts

### 3. **Content Layer: `content/authors/`**

#### A. **`authors.11tydata.js`** - Collection Configuration

```javascript
export default {
	tags: [
		"authors"
	],
	"layout": "authors.njk",
};
```

**Purpose**:
- Applies to ALL `.md` files in the `content/authors/` directory
- Automatically tags them as "authors" for collection access
- Sets the default layout to `authors.njk`

#### B. **Individual Author Profiles** (e.g., `john_dhoe.md`)

```markdown
---
title: John Doe
description: "John Doe"
date: 2025-04-07
image: "https://images.unsplash.com/photo-..."
social: 
 - title: Jan Facebook
   icon: fa-solid fa-envelope
   url: mailto:jan@fordv8foundation.org
button:
 - title: Send Jan Message
   url: "jan@fordv8foundation.org"
---
This is my dream job after retiring as a home economics teacher...
```

**Front Matter Breakdown**:
- `title`: Author's display name
- `description`: Short description/tagline
- `date`: Profile creation date
- `image`: Profile photo URL
- `social`: Array of social media links with icons
- `button`: Array of call-to-action buttons

**Important**: The filename (e.g., `john_dhoe.md`) **must match** the `key` in `authors.yaml`!

### 4. **Custom Filters in `eleventy.config.js`**

The system uses three custom filters to power the author functionality:

#### A. **`byAuthor` Filter** (Lines 87-109)

```javascript
eleventyConfig.addFilter("byAuthor", (posts, authorKey) => {
  if (!posts || !Array.isArray(posts)) {
    return [];
  }
  
  const targetKey = String(authorKey).trim();

  return posts.filter(post => {
    const postAuthorData = post.data.author;

    if (!postAuthorData || typeof postAuthorData !== 'string') {
      return false; 
    }

    // Split comma-separated authors and trim each
    const authors = postAuthorData.split(',')
      .map(a => a.trim());
    
    // Check if targetKey is in the post's author list
    return authors.includes(targetKey);
  });
});
```

**Purpose**: Filters posts collection to find all posts written by a specific author.

**Supports**:
- Single author: `author: john_dhoe`
- Multiple authors: `author: john_dhoe, jon_jones`

#### B. **`getPostsByAuthor` Filter** (Lines 110-139)

Similar to `byAuthor` but with additional validation - used as a backup/alternative method.

#### C. **`getAuthors` Filter** (Lines 141-144)

```javascript
eleventyConfig.addFilter("getAuthors", (authors, label) => {
    let labels = label.split(','); 
    return authors.filter(a => labels.includes(a.key));
});
```

**Purpose**: Retrieves author objects from the authors data based on comma-separated keys.

#### D. **`findAuthorByKey` Filter** (Lines 145-151)

```javascript
eleventyConfig.addFilter("findAuthorByKey", (authors, authorKey) => {
    if (!authorKey || !authors || !Array.isArray(authors)) return null;
    const key = String(authorKey).trim().toLowerCase();
    return authors.find(author => 
        String(author.key || '').trim().toLowerCase() === key
    );
});
```

**Purpose**: Finds a single author object by key (case-insensitive).

### 5. **How to Use the Multi-Author System**

#### Adding a New Author:

1. **Register in `_data/authors.yaml`**:
   ```yaml
   - key: jane_smith
     name: Jane Smith
     bio: Software developer and writer
     image: https://example.com/jane.jpg
   ```

2. **Create profile page** `content/authors/jane_smith.md`:
   ```markdown
   ---
   title: Jane Smith
   description: "Software Developer"
   date: 2025-11-03
   image: "https://example.com/jane.jpg"
   social: 
    - title: Twitter
      icon: fab fa-twitter
      url: https://twitter.com/janesmith
   ---
   Bio content here...
   ```

3. **Assign to posts** in any blog post's front matter:
   ```yaml
   ---
   title: My Blog Post
   author: jane_smith
   ---
   ```

   Or for multiple authors:
   ```yaml
   ---
   title: Collaborative Post
   author: jane_smith, john_dhoe
   ---
   ```

### 6. **URL Structure**

The author pages are generated at:
- `/authors/john_dhoe/` - John Doe's profile
- `/authors/jon_jones/` - Jon Jones' profile
- `/authors/` - List of all authors (from `content/authors.njk`)

### 7. **Integration Points**

The author system integrates with:

1. **Blog Posts** (`content/blog/*.md`):
   - Use `author: key` in front matter
   - Posts are automatically linked to author profiles

2. **Post Templates** (`_includes/post.njk`):
   - Can display author information
   - Link to author profile pages

3. **Sidebars/Widgets** (`_includes/partials/sidebar.njk`):
   - Can show author lists
   - Display author bio in post sidebar

4. **Navigation**:
   - Authors page accessible from main navigation
   - Individual author profiles are browsable

---

## Key Technical Decisions

### Why Two Author Files?

1. **`_data/authors.yaml`**: 
   - Global data source
   - Quick lookup for author metadata
   - Used in filters and templates

2. **`content/authors/*.md`**: 
   - Individual author profile pages
   - Rich content with full HTML/Markdown support
   - Custom front matter for social links, buttons, etc.

### Why This Architecture?

- **Separation of Concerns**: Data vs. presentation
- **Flexibility**: Authors can have profiles without being in the YAML
- **Scalability**: Easy to add new authors and posts
- **Maintainability**: Changes to author data don't require rebuilding all pages

---

## Build & Development

### Available Scripts (from `package.json`):

- `npm run build`: Build the site for production
- `npm run start`: Start development server with live reload
- `npm run build-ghpages`: Build with GitHub Pages path prefix
- `npm run start-ghpages`: Develop with GitHub Pages path prefix

### Dependencies:

**Core**:
- `@11ty/eleventy`: Static site generator (v3.1.2)
- `markdown-it`: Markdown parser with plugins
- `luxon`: Date handling

**Plugins**:
- `@11ty/eleventy-plugin-rss`: RSS/Atom feed generation
- `@11ty/eleventy-plugin-syntaxhighlight`: Code syntax highlighting
- `@11ty/eleventy-navigation`: Navigation helpers
- `eleventy-plugin-toc`: Table of contents generation
- `pagefind`: Site search functionality

**Styling**:
- Bootstrap (via `css/bs.css` and `js/bs.js`)
- Custom CSS with sidebar, navigation, and responsive design

---

## Summary

PR #2 transformed the repository into a fully functional, modern static site with:

✅ **Complete Eleventy setup** with configuration and build scripts  
✅ **Multi-author blog system** with profile pages and post attribution  
✅ **Bootstrap-based responsive design** with custom styling  
✅ **Content management** with pages, posts, tags, and authors  
✅ **Advanced features** including search, RSS feeds, and SEO optimization  
✅ **Deployment configurations** for Netlify and Vercel  

The **author.njk** system provides a robust, flexible way to manage multiple authors with individual profile pages, social links, and automatic post filtering - all while maintaining clean separation between data, templates, and content.
