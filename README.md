# Open Source Buddy

An AI-assisted web app that helps beginners **find, understand and start** their first open source contribution.

- Browse and search issues, filter by language and topic
- Check a repo's health before you invest a weekend (last merged PR, maintainer response time, CONTRIBUTING.md)
- Press **Explain this issue** to get a plain-English summary, likely files and a starting plan
- Save issues for later, switch between light and dark themes

This repo is the starter project for the **Hacktoberfest Hack Day at NIT Jamshedpur**. See [CONTRIBUTING.md](CONTRIBUTING.md) to take part.

## Run it

You need **Node.js 20 or newer** (works on Windows, macOS and Linux). No database and no API keys are needed.

```bash
git clone <your-fork-url>
cd open-source-buddy
npm install
npm run dev
```

Then open http://localhost:5173. `npm run dev` starts the Express API (port 3001) and the Vite client (port 5173) together.

## Mock mode vs live mode

| | Mock (default) | Live |
|---|---|---|
| GitHub data | Local sample JSON in `server/github/mockData/` | Real GitHub REST API |
| Needs internet | No | Yes |
| UI hint | Yellow **Sample data** banner | No banner |

To change modes, copy `.env.example` to `.env` and edit it:

```bash
cp .env.example .env      # on Windows: copy .env.example .env
```

- `GITHUB_MODE=live` uses the real GitHub API. It works without a token (60 requests/hour). Add `GITHUB_TOKEN` for more.
- `AI_PROVIDER=mock` is the only AI provider that is implemented. It builds the explanation from the issue's own title, labels and body.

Keys and tokens live only in the server's `.env` file. They are never sent to the browser.

## Project structure

```
open-source-buddy/
├── client/                  React + Vite frontend
│   └── src/
│       ├── App.jsx          page layout and shared state
│       ├── api.js           all calls to the backend
│       ├── components/      cards, filters, detail panel, sidebar ...
│       ├── hooks/           data and theme hooks
│       ├── styles/          CSS files
│       └── utils/           small helpers
├── server/                  Node.js + Express backend
│   ├── index.js             app entry point
│   ├── config.js            reads .env
│   ├── routes/              /api/issues, /api/repos, /api/explain, /api/saved
│   ├── ai/                  AI provider interface + mock (Gemini, Groq, Ollama are stubs)
│   ├── github/              GitHub provider interface + mock + live
│   ├── lib/                 filtering, pagination, health score, saved-issue storage
│   ├── auth/                who is the current user
│   ├── middleware/          Express middleware
│   └── data/saved.json     saved issues (created automatically, git-ignored)
├── .github/                 pull request template
├── CONTRIBUTING.md
└── .env.example
```

## API

| Method | Path | Description |
|---|---|---|
| GET | `/api/issues` | List issues. Query: `q`, `language`, `topic`, `sort`, `page` |
| GET | `/api/repos/:owner/:name/health` | Repo health metrics and score |
| POST | `/api/explain` | Body `{ "issue": {...} }`, returns the AI explanation |
| GET / POST / DELETE | `/api/saved` | List, add (`POST`) or remove (`DELETE /api/saved/:id`) saved issues |

## License

[MIT](LICENSE)

---

# Open Source Buddy (हिंदी)

एआई-सहायता वाला एक वेब ऐप है जो शुरुआती लोगों को उनके पहले ओपन सोर्स योगदान को **खोजने, समझने और शुरू करने** में मदद करता है।

- issues को ब्राउज़ करें और खोजें, भाषा और विषय के अनुसार फ़िल्टर करें
- सप्ताहांत में समय लगाने से पहले किसी रेपो की सेहत जांचें (अंतिम merged PR, maintainer response time, CONTRIBUTING.md)
- **Explain this issue** दबाएं ताकि सरल अंग्रेज़ी में सारांश, संभावित फाइलें और शुरू करने का प्लान मिल सके
- issues को बाद के लिए सेव करें, लाइट और डार्क थीम के बीच स्विच करें

यह रेपो **NIT Jamshedpur का Hacktoberfest Hack Day** के लिए स्टार्टर प्रोजेक्ट है। हिस्सा लेने के लिए [CONTRIBUTING.md](CONTRIBUTING.md) देखें।

## इसे चलाएं

आपको **Node.js 20 या उससे नया** चाहिए (Windows, macOS और Linux पर काम करता है)। किसी डेटाबेस और API key की ज़रूरत नहीं है।

```bash
git clone <your-fork-url>
cd open-source-buddy
npm install
npm run dev
```

फिर http://localhost:5173 खोलें। `npm run dev` एक्सप्रेस API (port 3001) और Vite client (port 5173) दोनों को एक साथ शुरू करता है।

## मॉक मोड बनाम लाइव मोड

| | मॉक (डिफ़ॉल्ट) | लाइव |
|---|---|---|
| GitHub डेटा | `server/github/mockData/` में लोकल sample JSON | वास्तविक GitHub REST API |
| इंटरनेट की ज़रूरत | नहीं | हाँ |
| UI संकेत | पीला **Sample data** banner | कोई banner नहीं |

Modes बदलने के लिए, `.env.example` की कॉपी बनाकर `.env` बनाएं और उसे एडिट करें:

```bash
cp .env.example .env      # on Windows: copy .env.example .env
```

- `GITHUB_MODE=live` वास्तविक GitHub API का उपयोग करता है। यह token के बिना काम करता है (60 requests/hour)। अधिक उपयोग के लिए `GITHUB_TOKEN` जोड़ें।
- `AI_PROVIDER=mock` यह एकमात्र लागू AI provider है। यह issue के अपने title, labels और body से व्याख्या बनाता है।

Keys और tokens केवल सर्वर के `.env` फाइल में रहते हैं। उन्हें ब्राउज़र के पास कभी नहीं भेजा जाता।

## प्रोजेक्ट संरचना

```
open-source-buddy/
├── client/                  React + Vite फ्रंटएंड
│   └── src/
│       ├── App.jsx          पेज लेआउट और साझा state
│       ├── api.js           बैकएंड के सभी कॉल
│       ├── components/      cards, filters, detail panel, sidebar ...
│       ├── hooks/           डेटा और थीम hooks
│       ├── styles/          CSS फाइलें
│       └── utils/           छोटा helpers
├── server/                  Node.js + Express बैकएंड
│   ├── index.js             ऐप entry point
│   ├── config.js            .env पढ़ता है
│   ├── routes/              /api/issues, /api/repos, /api/explain, /api/saved
│   ├── ai/                  AI provider interface + mock (Gemini, Groq, Ollama are stubs)
│   ├── github/              GitHub provider interface + mock + live
│   ├── lib/                 फ़िल्टरिंग, pagination, health score, saved-issue storage
│   ├── auth/                वर्तमान उपयोगकर्ता कौन है
│   ├── middleware/          Express middleware
│   └── data/saved.json     सेव की गई issues (स्वचालित रूप से बनाई जाती है, git-ignored)
├── .github/                 pull request template
├── CONTRIBUTING.md
└── .env.example
```

## एपीआई

| विधि | पथ | विवरण |
|---|---|---|
| GET | `/api/issues` | issues सूचीबद्ध करें। क्वेरी: `q`, `language`, `topic`, `sort`, `page` |
| GET | `/api/repos/:owner/:name/health` | रेपो की health metrics और score |
| POST | `/api/explain` | Body `{ "issue": {...} }`, AI व्याख्या लौटाता है |
| GET / POST / DELETE | `/api/saved` | सूची देखें, जोड़ें (`POST`) या हटाएँ (`DELETE /api/saved/:id`) सेव की गई issues |

## लाइसेंस

[MIT](LICENSE)
