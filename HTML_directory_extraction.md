# Extracting and Filtering Paths from HTML via Linux CLI

A practical report on performing basic web scraping and path enumeration directly from the Linux terminal by chaining native utilities (`curl`, `tr`, `grep`, `sort`, and `wc`).

---

## Objective

Download the HTML source code of a target website (`https://www.inlanefreight.com`), strip away HTML tag noise, and extract a unique, counted list of all available URLs and internal paths.

---

## Pipeline Executed

```bash
curl -s https://www.inlanefreight.com | tr "\"'" "\n" | grep -E "^(https://www\.inlanefreight\.com/|/)" | sort -u | wc -l
```

---

## Step-by-Step Pipeline Breakdown

| Command / Segment | Function in the Pipeline |
| :--- | :--- |
| **`curl -s https://www.inlanefreight.com`** | Downloads the complete HTML source code of the target page.<br>• `-s` (*silent*): Hides the progress bar and download statistics to keep the standard output (`stdout`) clean. |
| **`tr "\"'" "\n"`** | Modifies the HTML by replacing all double quotes (`"`) and single quotes (`'`) with line breaks (`\n`). This ensures that each `href` or `src` attribute is isolated on its own separate line, making it easier to locate the directories. |
| **`grep -E "^(https://www\.inlanefreight\.com/\|/)"`** | Filters for only the relevant lines using extended regular expressions (`-E`):<br>• `^`: Requires the match to occur at the very beginning of the line.<br>• `https://www\.inlanefreight\.com/\|/`: Captures both absolute links belonging to the target domain and relative paths that begin with `/`. |
| **`sort -u`** | Sorts the extracted paths alphabetically and removes any duplicate entries (`-u` / *unique*), filtering out links that appear repeatedly in navigation menus or footers. |
| **`wc -l`** | Counts the total number of unique lines, providing the final count of discovered paths. |

---


## Key Concepts Learned

1. **Text manipulation (`tr`):** Converting delimiters (such as quotation marks) into line breaks greatly facilitates the subsequent processing of lines—containing elements like `href`, `src`, or `https`—using line-oriented tools such as `grep` and `sort`.
2. **Pattern anchoring (`^`):** Ensuring that a URL or relative path begins exactly at the start of the cleaned line.
3. **Deduplication (`sort -u`):** An essential command when analyzing web code, as it prevents redundant elements or repeated paths from cluttering the terminal output.

---

*Exercise completed as part of my training in Linux System Administration and Cybersecurity (Hack The Box Academy).*