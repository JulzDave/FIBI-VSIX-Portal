# FIBI VS Code Extension Portal (מתף)

A lightweight, single-page application that queries the official Visual Studio Marketplace, allowing users to search for extensions, view their version history, and directly download the `.vsix` binary files.

## ✨ Features

* **Zero Dependencies:** Pure HTML, CSS, and vanilla JavaScript. No build steps or package managers required.
* **Live Search:** Queries the official Microsoft Marketplace in real-time.
* **Lazy-Loaded History:** Fetches full version history only when requested to save bandwidth.
* **Direct Binary Download:** Fetches the `.vsix` file as a Blob and triggers a native browser download.

## 📡 API Architecture & Network Requests

To comply with network monitoring and firewall whitelisting, below is the complete outline of all external HTTP requests made by this application.

### 1. Extension Search API

* **Endpoint:** `POST https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery`
* **Purpose:** Retrieves a list of extensions matching the user's search query.
* **Explanation:** Triggered when the user clicks "Search". It sends a payload containing the search string (`filterType: 10`) and restricts results strictly to VS Code extensions (`filterType: 8`). It uses the bitwise flag `870` to request only essential metadata (title, publisher, description, latest version, and icon) to ensure a fast initial load time.

### 2. Version History API

* **Endpoint:** `POST https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery`
* **Purpose:** Retrieves the complete release history for a specific extension.
* **Explanation:** Triggered when the user clicks "View Previous Versions". To avoid downloading massive JSON payloads for every search result, this request is lazy-loaded. It searches for the exact extension by its unique identifier (`publisher.extensionName` via `filterType: 7`) and uses the bitwise flag `359`, which instructs Microsoft's servers to include the complete array of historical versions and their release dates.

### 3. VSIX Binary Download Asset

* **Endpoint:** `GET https://{publisher}.gallery.vsassets.io/_apis/public/gallery/publisher/{publisher}/extension/{extensionName}/{version}/assetbyname/Microsoft.VisualStudio.Services.VSIXPackage`
* **Purpose:** Downloads the actual extension package.
* **Explanation:** Triggered when the user clicks "Download" on either the latest version or a historical release. This request hits Microsoft's Azure CDN (`vsassets.io`) to fetch the raw binary `.vsix` file. The application fetches this as a `Blob` in the browser memory and seamlessly converts it into a local file download, ensuring the developer gets the exact package needed for IT sandboxing.

## 🚀 Usage

Simply open `index.html` in any modern web browser. No local server is strictly required unless corporate policies enforce strict CORS blocking on local file protocols.
