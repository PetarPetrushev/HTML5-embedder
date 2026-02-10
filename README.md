# URL Embedder Technical Documentation

## Overview

The **URL Embedder** is a lightweight, client-side web application designed to host external content within a secure, customizable <iframe> wrapper. It leverages URL query parameters to handle configuration, allowing for dynamic content loading, UI customization, and automated failover logic without requiring server-side state.

## Deployment & Usage

The application is entirely self-contained within a single HTML file. To use the embedder, append query parameters to the base URL.

**Base URL Pattern:** https://petarpetrushev.github.io/HTML5-embedder/?urls[]=https://example.com

## Configuration Parameters

### Core Parameters

| **Parameter** | **Type** | **Description** |
| :--- | :--- | :--- |
| urls[] | Array | Required. An array of URLs to embed. Supports plain URLs or Markdown-style named links: \[Label](https://url.com). |
| timeout | Integer | The duration (in milliseconds) to wait for a page load before attempting to load the next URL in the array. Defaults to 8000. |
| title | String | Sets the browser tab title. |
| selectorName | String | Sets the display text for the URL selection button. Defaults to "Select URL". |

### UI & Theming

| **Parameter** | **Type** | **Description** |
| :--- | :--- | :--- |
| accentColor | CSS Color | Overrides the primary UI color (buttons, loader, icons). |
| backgroundColor | CSS Color | Overrides the background color of the container (visible during loading/errors). |
| textColor | CSS Color | Overrides the primary text color. |
| colorScheme | String | Forces a specific theme. Options: light, dark. Defaults to system preference. |
| favicon | URL | Sets a custom favicon for the page. |

### Iframe Security & Permissions

| **Parameter** | **Type** | **Description** |
| :--- | :--- | :--- |
| sandbox | String | Applies sandbox attribute restrictions (e.g., allow-forms allow-scripts). |
| allow | String | Sets the iframe allow attribute for feature policies (e.g., camera; microphone). |

## Features

### 1. Dynamic URL Resolution

The application parses the urls[] parameter to extract both the target destination and the display name. If the Markdown syntax \[Name](URL) is detected, the application populates the selector menu with the "Name" while maintaining the "URL" as the source.

### 2. Automated Failover (Auto-Loading)

Upon initial load, the application attempts to resolve the first URL in the provided list.

- If the onload event is not triggered within the specified timeout, the script invokes loadNext().
- The process continues sequentially through the array until a successful load occurs or the list is exhausted.

### 3. Security & Sanitization

The application implements two layers of input protection:

- Input Sanitization: Removes sensitive characters (;, {, }) from CSS-related parameters to prevent basic CSS injection.
- URL Protocol Validation: Blocks javascript: and data:text/html protocols to mitigate Cross-Site Scripting (XSS) risks.

### 4. Responsive UI

The interface utilizes a "UI Layer" with pointer-events: none to allow interaction with the underlying iframe while maintaining a persistent, high-z-index control menu. The UI is built using the Noto Sans typeface and Material Symbols for a modern aesthetic.

## Implementation Example

<https://petarpetrushev.github.io/HTML5-embedder/?urls[]=[Wikipedia](https://wikipedia.org)&urls[]=[Useful%20tools](https://tools.petar.petrushev.u.is-my.space)&urls[]=https://wikimedia.org&timeout=5000&title=My%20URLs&selectorName=Pick%20a%20URL&sandbox=allow-scripts>

## Constraints & Limitations

- X-Frame-Options: This tool cannot bypass SAMEORIGIN or DENY headers set by external websites. If a target site forbids framing, the iframe will remain blank or show a browser-level connection error.
- Cross-Domain Scripting: Due to the Same-Origin Policy, the parent wrapper cannot interact with the JavaScript context inside the iframe unless both share the same domain.
