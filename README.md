# Tickmark

**Check a finished deliverable against its QA checklist, item by item.**

**Live app:** https://ajsims23.github.io/tickmark

Load a QA checklist and the files it applies to, such as a deck, memo, fact sheet or financials. Tickmark works through every checklist item and marks it **Passed**, **Failed**, **Needs review** or **Confirm yourself**. Each result shows where in the files it looked.

---

## How it works

- **Rule checks run in your browser.** Fonts, footers, slide numbers, figures that tie across files: anything that can be read straight from the files is checked on the page itself.
- **Judgment checks go to a model you choose.** Items that need reading and reasoning are sent to a model running on your own machine, such as Ollama or LM Studio. No cloud API key is needed.
- **Your files are never uploaded.** They are read inside the browser. The only thing that leaves the page is a text excerpt sent to the model address you set, and that is normally your own computer.

You can switch the model off and run rule checks only.

## Quick start

1. Open **https://ajsims23.github.io/tickmark**
2. **Checklist:** drop in the QA checklist (Excel or CSV). Pick the sheet and the column that holds the check items.
3. **Files to check:** drop in the deliverables (Excel, PowerPoint, Word, PDF, CSV or text). Add the source workbook too, so figures can be traced back to it.
4. **Run checks.**
5. Review the results. You can override any result with your own call, then **Download filled checklist** or **Print report**.

### Try it with the sample files

The [`tickmark-sample-files`](tickmark-sample-files) folder has a complete example to try first:

| File | Role |
|---|---|
| `Investor_Pack_QA_Checklist.xlsx` | The checklist |
| `HarborRidge_Q3-2026_Investor_Deck.pptx` | Deliverable |
| `HarborRidge_Q3-2026_Investor_Memo.docx` | Deliverable |
| `HarborRidge_Q3-2026_Fact_Sheet.pdf` | Deliverable |
| `HarborRidge_Q3-2026_Financials.xlsx` | Source workbook |

## Setting up a local model

Judgment checks need a model server running on your machine. Click **Set up a model** in the app for the same steps.

### Ollama (recommended)

1. Install Ollama from [ollama.com](https://ollama.com) and pull an instruct model:
   ```
   ollama pull qwen2.5:7b
   ```
   Bigger models (14B and up) make better judgment calls if your machine can run them.
2. Allow Tickmark to call Ollama, then **quit and reopen Ollama**:

   **Windows (PowerShell)**
   ```
   setx OLLAMA_ORIGINS "https://ajsims23.github.io"
   ```
   **macOS**
   ```
   launchctl setenv OLLAMA_ORIGINS "https://ajsims23.github.io"
   ```
   **Linux (systemd):** run `sudo systemctl edit ollama`, add the lines below, then run `sudo systemctl restart ollama`:
   ```
   [Service]
   Environment="OLLAMA_ORIGINS=https://ajsims23.github.io"
   ```
3. If your browser asks whether the site can reach devices on your local network, allow it.
4. In Tickmark, open **Set up a model**, choose **Ollama**, pick your model and click **Test connection**.

### Other options

| Server | Default address | Notes |
|---|---|---|
| LM Studio | `http://localhost:1234/v1` | Start the local server from the Developer tab and turn on CORS |
| llama.cpp | `http://localhost:8080/v1` | `llama-server -m your-model.gguf --port 8080` |
| Any OpenAI-compatible server | your base URL ending in `/v1` | Jan, vLLM, LocalAI and others; add an API key if the server needs one |

## Checklist format

Any Excel or CSV sheet with one check per row works. Tickmark lets you pick which column holds the check items and, optionally, a column for sections. Other columns, such as owner or status, are kept as they are. A typical layout:

| # | Section | Check item | Owner | Status |
|---|---|---|---|---|
| 1 | Formatting | All slides in the deck use Calibri font. | AM | |
| 2 | Formatting | Footer reads 'Strictly Private & Confidential' on every slide. | AM | |

Write each item as one clear, testable statement. Specific items ("Footer reads X on every slide") get firmer results than vague ones ("Deck looks professional").

## Running it offline

`index.html` is the whole app. Download it and open it directly in a browser. In that case Ollama has to allow every origin (`OLLAMA_ORIGINS="*"`), because a local file has no web address to allow. The page still needs internet access the first time it loads, to fetch its file-reading libraries from cdnjs.

## Troubleshooting

- **"Not reachable" next to the model:** check that the model server is running, that `OLLAMA_ORIGINS` is set and that Ollama was restarted after setting it. Then click **Test connection**.
- **"The file readers couldn't load":** the page couldn't reach cdnjs.cloudflare.com. Check your connection and reload.
- **Slow or patchy judgment checks:** lower *Checks at a time*, raise *Context window*, or use a larger model.
