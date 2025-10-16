# Mermaid diagrams

This folder contains Mermaid diagram sources (files with `.mmd` extension). These files are plain-text Mermaid definitions that can be previewed in editors, rendered to image files, or opened in the Mermaid Live Editor.

## Files
- `proposed_design.mmd` — primary network flowchart showing OT/IT networks, RabbitMQ, Consumers, and a vertical separator for Firewall.

## Previewing in VS Code
1. Install a Mermaid preview extension. Recommended:
   - "Mermaid Preview" by vstirbu (search for `vstirbu.vscode-mermaid-preview`).
   - Alternatively, "Markdown Preview Enhanced" or other Markdown extensions that support Mermaid.
2. Open a `.mmd` file in the editor.
3. Use Command Palette (Cmd+Shift+P) → "Open Preview to the Side" or right-click → "Open Mermaid Preview".

Notes:
- Some extensions expect ` ```mermaid ` fenced code blocks inside Markdown; a dedicated Mermaid preview can open `.mmd` files directly.

## Rendering to PNG / SVG via CLI (offline)
Requirements: Node.js and npm/yarn.

Install mermaid-cli globally:

```bash
# macOS zsh
brew install node    # if you don't have node
npm install -g @mermaid-js/mermaid-cli
```

Render commands:

```bash
# render PNG
mmdc -i diagram.mmd -o diagram.png

# render SVG
mmdc -i diagram.mmd -o diagram.svg
```

If you prefer not to install globally use npx from a project directory:

```bash
npm init -y
npm install --save-dev @mermaid-js/mermaid-cli
npx mmdc -i diagram.mmd -o diagram.svg
```

## Live web preview
- Open https://mermaid.live and paste your `.mmd` contents. It shows a live preview and export options.

## Docker (no local Node)
If you prefer Docker, run a container to render diagrams:

```bash
# Example using node image to install mermaid-cli and render
docker run --rm -v "$PWD":/data node:18-buster /bin/bash -c "npm install -g @mermaid-js/mermaid-cli && mmdc -i /data/diagram.mmd -o /data/diagram.svg"
```

## Vertical separator / Firewall label
The `*.mmd` file contains a vertical separator implemented as a small TB subgraph to keep the main flow left-to-right while rendering a vertical edge with a centered label:

```mermaid
flowchart LR
  subgraph OT_Net["OT Network"]
    DGIC["DGIC"]
  end

  subgraph FW_sep[""]
    direction TB
    FW_top[""]
    FW_mid[""]
    FW_bottom[""]
  end

  subgraph IT_Net["IT Network"]
    TLG["Telegraf"]
    RMQ["RabbitMQ"]
    CONS["Consumers"]
    DB["Local InfluxDB"]
  end

  DGIC -->|UDP Logs| FW_top
  FW_top --> FW_mid
  FW_mid -->|Firewall (UDP Allowed)| FW_bottom
  FW_bottom --> TLG
```

If your Mermaid renderer doesn't support `direction` inside a `subgraph`, use a narrow styled node or a pipe-character multiline node as fallback (see `diagram.mmd` for examples).

## Troubleshooting
- Parse errors often come from wrong code-fence headers (e.g. using ````mermaid.flowchart`` instead of ````mermaid``) or putting `direction` on the same line as `subgraph`.
- If `style` sizing (width/height) is ignored, use the pipe-character fallback or the TB subgraph approach.
- If `mmdc` fails due to puppeteer/chromium issues, update Node or install missing system dependencies; using Docker avoids this problem.
