---
title: Building an OSM Tools Quick Access Browser Extension
description: This post was 95% written by AI. This is an absolutely terrible idea, as you can see. None of my other posts are written in whole or in part by AI.
date: 2025-03-30
tags:
  - openstreetmap
  - development
  - ai-written
card: summary_large_image
draft: false
toc: false
---

# Building an OSM Tools Quick Access Browser Extension

As an active OpenStreetMap (OSM) contributor, I often find myself switching between various OSM-related tools while mapping. Each tool serves a specific purpose - from editing with different editors to analyzing data with specialized visualization tools. I frequently found myself manually copying coordinates from the OSM website URL and pasting them into other tools, which was tedious and time-consuming.

To solve this problem, I decided to [build](https://github.com/mvexel/osm-tools-extension) a browser extension that would provide quick access to these tools directly from any OSM map page. In this post, I'll walk through the technical aspects of building the OSM Tools Quick Access extension, explaining the architecture, key features, and challenges encountered along the way.

## The Problem

When working with OpenStreetMap, you often need to view the same geographic area in different tools. For example:

- You might want to edit an area in iD or Rapid Editor
- Analyze data quality with OSM Inspector
- Compare different map styles with MapCompare
- Create a custom map with uMap
- Check street-level imagery with Mapillary

Each of these tools uses a slightly different URL format to specify the map location. Manually copying coordinates and reformatting them for each tool is inefficient.

## Solution Overview

The OSM Tools Quick Access extension solves this problem by:

1. Detecting when you're viewing an OSM map or changeset
2. Extracting the relevant coordinates or changeset ID from the URL
3. Providing a popup menu with links to various tools, automatically formatted with the correct coordinates or changeset ID

## Technical Architecture

The extension is built using standard web technologies:

- **HTML/CSS**: For the user interface
- **JavaScript**: For the core functionality
- **Chrome/Firefox Extension APIs**: For browser integration

The extension follows the browser extension architecture with these key components:

- **Manifest**: Defines the extension metadata and permissions
- **Popup**: The UI that appears when clicking the extension icon
- **Settings Page**: For customizing the tools list
- **Storage API**: For saving user preferences

Let's dive into each component in detail.

## Manifest Configuration

The extension uses Manifest V3, the latest extension manifest format supported by modern browsers:

```json
{
  "manifest_version": 3,
  "name": "OSM Tools Quick Access",
  "description": "A menu for quick access to OSM Tools",
  "author": "Martijn van Exel",
  "version": "1.0",
  "permissions": ["activeTab", "storage"],
  "action": {
    "default_popup": "popup.html"
  },
  "browser_specific_settings": {
    "gecko": {
      "id": "osm-tools-quick-access@osm.lol",
      "strict_min_version": "109.0"
    }
  }
}
```

The manifest requests minimal permissions:
- `activeTab`: To access the current tab's URL (for extracting coordinates)
- `storage`: To save user preferences

## Core Functionality

The extension's core functionality is implemented in popup.js, which handles:

1. **URL Parsing**: Extracting coordinates or changeset IDs from the current tab's URL
2. **Link Generation**: Creating links to various tools with the correct parameters
3. **Settings Management**: Loading and applying user preferences

### URL Parsing

The extension uses regular expressions to extract coordinates or changeset IDs from OSM URLs:

```javascript
const coordMatch = url.match(/#?map=([0-9.]+)\/([\-0-9.]+)\/([\-0-9.]+)/);
const changesetMatch = url.match(/(?:openstreetmap\.org\/changeset\/|osmcha\.org\/changesets\/)([0-9]+)/);
```

This allows it to work with URLs like:
- `https://www.openstreetmap.org/#map=12/14.6208/121.0470` (coordinates)
- `https://www.openstreetmap.org/changeset/12345` (changeset)

### Link Generation

Once the coordinates or changeset ID are extracted, the extension generates links to various tools by replacing placeholders in URL templates:

```javascript
const formattedUrl = url
    .replace('{zoom}', zoom)
    .replace('{lat}', lat)
    .replace('{lon}', lon);
```

For changeset tools:

```javascript
const formattedUrl = url.replace('{changesetId}', changesetId);
```

### Tool Configuration

The extension comes with a set of default tools defined in defaults.js:

```javascript
const defaultTools = {
    'Rapid Editor': {
        url: 'https://rapideditor.org/edit#map={zoom}/{lat}/{lon}',
        enabled: true,
        type: 'coordinate'
    },
    'OSMCha': {
        url: 'https://osmcha.org/changesets/{changesetId}',
        enabled: true,
        type: 'changeset'
    },
    // More tools...
};
```

Each tool has:
- A name
- A URL template with placeholders
- An enabled flag
- A type (coordinate or changeset)

## User Interface

The extension has two main UI components:

1. **Popup**: The menu that appears when clicking the extension icon
2. **Settings Page**: For customizing the tools list

### Popup Interface

The popup is a simple HTML page that displays a list of links based on the current URL. It's styled with CSS to provide a clean, user-friendly interface.

When the popup loads, it:
1. Extracts coordinates or changeset ID from the current tab's URL
2. Loads user settings from storage
3. Generates links based on the extracted data and user settings
4. Renders the links as clickable buttons

### Settings Interface

The settings page allows users to:
- Enable/disable tools
- Reorder tools using drag-and-drop
- Add custom tools with their own URL templates

The settings are implemented using standard HTML form elements and JavaScript event handlers. The drag-and-drop functionality is implemented using the HTML5 Drag and Drop API.

## Data Storage

The extension uses the Chrome Storage API to save user preferences:

```javascript
chrome.storage.sync.set({
    tools: updatedTools,
    order: updatedOrder
});
```

This ensures that settings are:
1. Persistent across browser sessions
2. Synchronized across devices (if the user is signed in to the browser)

## Cross-Browser Compatibility

To ensure the extension works on both Chrome and Firefox, I included browser-specific settings in the manifest:

```json
"browser_specific_settings": {
  "gecko": {
    "id": "osm-tools-quick-access@osm.lol",
    "strict_min_version": "109.0"
  }
}
```

This allows the extension to be published on both the Chrome Web Store and Firefox Add-ons.

## Technical Challenges

### Challenge 1: URL Format Variations

Different OSM-related websites use slightly different URL formats for specifying coordinates. For example:
- OpenStreetMap: `#map=zoom/lat/lon`
- Mapillary: `?lat=lat&lng=lon&z=zoom`

To handle this, I created a flexible placeholder system that can adapt to different URL formats.

### Challenge 2: Maintaining State

Browser extensions have limited persistent storage options. I needed to ensure that user settings were properly saved and loaded, even after browser restarts.

The solution was to use the Chrome Storage API with a robust initialization system that:
1. Checks if settings exist
2. Loads defaults if not
3. Merges new default tools with existing user settings when updating

### Challenge 3: Cross-Browser Compatibility

Making the extension work on both Chrome and Firefox required some adjustments, though I aimed to keep the codebase as unified as possible. The key differences I encountered included:

1. **Manifest Configuration**: Firefox requires the `browser_specific_settings` field with a unique extension ID (`osm-tools-quick-access@osm.lol`) and minimum version specification, while Chrome simply ignores this field. This allowed me to maintain a single manifest file that works for both browsers.

2. **Extension Loading**: During development, the process for loading an unpacked extension differs between browsers. Chrome uses the 'Load Unpacked' option and points to the extension directory, while Firefox uses 'Load Temporary Add-on' and requires selecting a file within the extension directory.

3. **Testing Workflow**: Firefox's temporary extensions are removed when the browser closes, requiring more frequent reloading during development compared to Chrome, which persists the extension until explicitly removed.

4. **Storage Behavior**: While both browsers implement the `chrome.storage.sync` API, I noticed slight differences in synchronization speed and behavior when testing across multiple devices, requiring additional error handling to ensure a consistent experience.

5. **UI Rendering**: Firefox and Chrome have subtle differences in how they render CSS, particularly with the popup menu's dimensions and scrolling behavior. I had to use more flexible CSS to accommodate both browsers' rendering engines.

## Future Improvements

There are several ways the extension could be improved:

1. **Support for more URL formats**: Add support for more OSM-related websites
2. **Tool categories**: Group tools by category for better organization
3. **Import/export settings**: Allow users to share their tool configurations
4. **Keyboard shortcuts**: Add keyboard navigation for power users

--- 
If you're an OpenStreetMap contributor, I hope you find this extension useful. And if you're a developer interested in browser extensions, I hope this technical overview provides some insights into the development process.

Happy mapping!
