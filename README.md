# n8n Paper Analyst

A comprehensive n8n workflow that automates the "Read Later" process. It fetches bookmarked URLs, converts them to clean Markdown, analyzes them using an AI model (via Fabric), and saves the resulting digest directly to an Obsidian vault.

## 🚀 Workflow Overview

1.  **Schedule Trigger:** Runs every 5 hours.
2.  **Fetch Bookmarks:** Pulls new links from a custom bookmarking API (e.g., Karakeep/Linkding).
3.  **Deduplication:** Checks against previous runs to ensure only new articles are processed.
4.  **Content Extraction:** Uses [Jina AI](https://jina.ai/reader/) to convert the target URL into LLM-friendly Markdown.
5.  **AI Analysis:** Sends the content to a local [Fabric](https://github.com/danielmiessler/fabric) instance (running `grok-4.1-fast` via OpenRouter) to apply the `analyze_paper` pattern.
6.  **File System:** Saves the final AI analysis as a `.md` file in a specified local folder (e.g., Obsidian).

## 🛠️ Prerequisites

* **n8n:** Self-hosted or Cloud version (Note: "Read/Write Files from Disk" node requires a self-hosted instance with filesystem access).
* **Fabric Server:** A running instance of the Fabric API (or any OpenAI-compatible chat endpoint).
* **Jina AI:** An API key for `r.jina.ai` (optional for low volume, but recommended).
* **Bookmark Source:** An API endpoint that returns a list of URLs (workflow is configured for a generic JSON response).

## ⚙️ Configuration

After importing the workflow, you **must update** the following nodes with your specific details:

### 1. Node: `Get Bookmarks`
* **URL:** Replace the placeholder URL with your bookmark manager's API endpoint.
    * *Target Field:* `parameters.url`
* **Authentication:** Setup your Bearer Token or Header Auth in n8n credentials.

### 2. Node: `Send to Fabric`
* **URL:** Update the endpoint to point to your Fabric server.
    * *Current Default:* `http://localhost:8080/chat` (If running n8n in Docker, use the host IP).
* **Model/Pattern:** The workflow currently requests `x-ai/grok-4.1-fast:free` with the `analyze_paper` pattern. Adjust the JSON body if you prefer a different model.

### 3. Node: `Read/Write Files from Disk`
* **File Path:** Update the path to match your local Obsidian vault or storage directory.
    * *Current Default:* `/data/obsidian/`
    * *Note:* Ensure the Docker container (if used) has this volume mounted.

## 📦 Installation

1.  Clone this repository.
2.  Open your n8n dashboard.
3.  Go to **Workflows** -> **Import**.
4.  Select the `workflow.json` file from this repo.
5.  Activate the workflow.

## 📝 Environment Variables (Optional)

To make the workflow portable, you can replace hardcoded values in the n8n nodes with expressions referencing environment variables:

* `BOOKMARK_API_URL` -> URL for the "Get Bookmarks" node.
* `FABRIC_API_URL` -> URL for the "Send to Fabric" node.
* `OBSIDIAN_PATH` -> Base path for the "Read/Write" node.

## 📄 License

[MIT](LICENSE)
