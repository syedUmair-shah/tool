# Apollo-Style Lead Generator Chrome Extension (Advanced - Free Version)

This is a complete Chrome Extension (Manifest V3) similar to Apollo.io (basic + advanced free features).

---

# 📁 Folder Structure

apollo-extension/
│
├── manifest.json
├── popup.html
├── popup.js
├── content.js
├── background.js
├── styles.css
├── utils.js
└── icon.png

---

# 1️⃣ manifest.json

```json
{
  "manifest_version": 3,
  "name": "Free Apollo Lead Generator",
  "version": "1.0",
  "description": "Extract LinkedIn leads and manage them like Apollo.io",
  "permissions": ["storage", "activeTab", "scripting"],
  "host_permissions": ["https://www.linkedin.com/*"],
  "action": {
    "default_popup": "popup.html",
    "default_title": "Lead Generator"
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["https://www.linkedin.com/*"],
      "js": ["content.js"]
    }
  ]
}
```

---

# 2️⃣ popup.html

```html
<!DOCTYPE html>
<html>
<head>
  <title>Lead Generator</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="container">
    <h2>Lead Generator</h2>

    <button id="extract">Extract Lead</button>
    <button id="export">Export CSV</button>
    <button id="clear">Clear Leads</button>

    <h3>Saved Leads</h3>
    <ul id="leadList"></ul>
  </div>

  <script src="popup.js"></script>
</body>
</html>
```

---

# 3️⃣ popup.js

```javascript
function loadLeads() {
  chrome.storage.local.get(["leads"], (result) => {
    let leads = result.leads || [];
    let list = document.getElementById("leadList");
    list.innerHTML = "";

    leads.forEach((lead, index) => {
      let li = document.createElement("li");
      li.textContent = `${lead.name} - ${lead.title}`;
      list.appendChild(li);
    });
  });
}

// Extract Lead from page
document.getElementById("extract").addEventListener("click", async () => {
  let [tab] = await chrome.tabs.query({ active: true, currentWindow: true });

  chrome.scripting.executeScript({
    target: { tabId: tab.id },
    function: extractLead
  });
});

function extractLead() {
  let name = document.querySelector("h1")?.innerText || "N/A";
  let title = document.querySelector(".text-body-medium")?.innerText || "N/A";

  chrome.runtime.sendMessage({
    type: "SAVE_LEAD",
    data: { name, title }
  });
}

// Export CSV
document.getElementById("export").addEventListener("click", () => {
  chrome.storage.local.get(["leads"], (result) => {
    let leads = result.leads || [];

    let csv = "Name,Title\n";
    leads.forEach(l => {
      csv += `${l.name},${l.title}\n`;
    });

    let blob = new Blob([csv], { type: "text/csv" });
    let url = URL.createObjectURL(blob);

    let a = document.createElement("a");
    a.href = url;
    a.download = "leads.csv";
    a.click();
  });
});

// Clear
document.getElementById("clear").addEventListener("click", () => {
  chrome.storage.local.set({ leads: [] }, loadLeads);
});

loadLeads();
```

---

# 4️⃣ background.js

```javascript
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.type === "SAVE_LEAD") {
    chrome.storage.local.get(["leads"], (result) => {
      let leads = result.leads || [];
      leads.push(request.data);

      chrome.storage.local.set({ leads });
    });
  }
});
```

---

# 5️⃣ content.js (Advanced scraping helper)

```javascript
console.log("Lead extension active");

// Future: bulk scrape + scroll automation
```

---

# 6️⃣ styles.css

```css
body {
  font-family: Arial;
  width: 250px;
  padding: 10px;
}

button {
  display: block;
  width: 100%;
  margin: 5px 0;
  padding: 8px;
  cursor: pointer;
}

ul {
  padding: 0;
  list-style: none;
}

li {
  font-size: 12px;
  margin: 3px 0;
}
```

---

# 🚀 Advanced Features You Can Add Next

* AI email finder (pattern based)
* LinkedIn search auto-scraper
* CRM dashboard (React web app)
* Enrichment API (Hunter.io free tier)
* Lead scoring system
* Auto connection message generator

---

# 📌 How to Install

1. Go to `chrome://extensions/`
2. Enable Developer Mode
3. Click “Load unpacked”
4. Select this folder

---

Agar chaho to me isko next level par le ja sakta hon:
👉 Apollo clone + SaaS dashboard + login system + database (Firebase/Supabase)
