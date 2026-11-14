# ⚡ Ohmyssey

**Sail from Troy to Ithaca. The only way home is electronics.**

Ohmyssey is a browser game where you play Odysseus and learn electronics one island at a time. Each stop teaches one idea, then has you build a small working circuit using real component values. Athena is an AI guide powered by Gemini. She looks at your circuit and nudges you toward the fix without handing you the answer. In the final level you design a 555 timer beacon, and the lighthouse of Ithaca blinks at the rate you calculated to guide your ship home.

Someone who has never touched a resistor can finish in about 20 minutes and come away able to read a simple schematic, size an LED resistor, and time a blinker.

> Built in 24 hours at **hackUTA** (Nov 14–15, 2026).

🔗 **Live:** [ohmyssey.tech](https://ohmyssey.tech) · 🎥 **Demo video:** _link_ · 📄 **Devpost:** _link_

---

## 🗺️ The Voyage

| # | Episode | Concept | You build |
|---|---------|---------|-----------|
| 0 | **Leaving Troy** | Voltage, current, closed loops, LED polarity | Ship's lantern: battery + switch + resistor + LED |
| 1 | **The Cyclops' Cave** | Ohm's law, V = I × R, and why LEDs need resistors | A torch to blind Polyphemus (pick the right resistor or burn out the LED) |
| 2 | **The Sirens** | Series vs. parallel, equivalent resistance | Crew lanterns in parallel, so one going dark doesn't darken the rest |
| 3 | **Aeolus' Bag of Winds** | Capacitors, RC time constant τ = R × C | An LED that fades slowly as the capacitor discharges |
| 4 | **Charybdis** | Inductors, flyback voltage, diodes | A coil protected by a correctly oriented flyback diode |
| 5 | **The Lighthouse of Ithaca** | 555 timer in astable mode | **FINAL:** a blinking beacon at the frequency you designed |

Each level has 3 short lesson cards, 1 quiz question, and 1 circuit build, and takes about 3–4 minutes.

### The math under the hood

The game uses exact formulas instead of a full simulator, which keeps it instant and reliable.

```
LED current:      I = (V_supply − V_LED) / R      (red LED: V_LED = 2.0 V, safe range 10–20 mA)
Series:           R_eq = R1 + R2
Parallel:         R_eq = R1·R2 / (R1 + R2)
RC time constant: τ = R × C                       (63% charged at 1τ, ~full at 5τ)
Cap charging:     V(t) = V·(1 − e^(−t/RC))
555 astable:      t_high = 0.693·(R1 + R2)·C
                  t_low  = 0.693·R2·C
                  f      ≈ 1.44 / ((R1 + 2·R2)·C)
```

Example: in Level 1, (9 V − 2 V) / 390 Ω ≈ 18 mA ✅. In Level 5, R1 = 1 kΩ, R2 = 68 kΩ, C = 10 µF gives roughly 1 Hz 🔦.

---

## 🛠️ Tech Stack

- **Frontend:** Vite + React (plain JavaScript), plain CSS
- **Circuit builder:** SVG with fixed, drag-and-drop part slots (no free wiring)
- **Simulation:** small pure check functions in `src/engine/checks.js`
- **AI guide:** Google Gemini API via a Vercel serverless function (`api/athena.js`)
- **Hosting:** Vercel, auto-deployed from `main`
- **Domain:** GoDaddy Registry
- **Save state:** `localStorage` (no accounts, no database)
- **AI dev tools:** Cursor, Claude Code, Grok Bot (Jarvis)

## 📁 Project Structure

```
ohmyssey/
├── api/
│   └── athena.js          # Serverless function that calls Gemini (holds the API key)
├── src/
│   ├── components/        # LevelScreen, CircuitBoard, PartTray, StoryPanel, AthenaPanel, WinModal
│   ├── engine/
│   │   └── checks.js      # closedLoop, ledCurrent, seriesParallel, rcTime, flyback, astable555
│   ├── levels/            # level0.json … level5.json + index.js loader
│   ├── App.jsx            # Voyage map + current level
│   └── styles.css         # Navy sea + bronze/gold Greek theme
├── AI_TEAM.md             # Log of how AI tools were used to build the project
└── README.md
```

Level content (story, lessons, quizzes, parts, targets, and canned hints) is stored as JSON, so you can edit it without touching code.

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- A Gemini API key from [Google AI Studio](https://aistudio.google.com/) (optional, since the game falls back to canned hints)

### Run locally

```bash
git clone https://github.com/<your-org>/ohmyssey.git
cd ohmyssey
npm install
npm run dev
```

Then open `http://localhost:5173`.

To test Athena locally with the serverless function, create a `.env.local` file:

```
GEMINI_API_KEY=your_key_here
```

Then run with the Vercel CLI:

```bash
npx vercel dev
```

> ⚠️ Never commit `.env.local`. It's already listed in `.gitignore`.

### Deploy

1. Import the repo into Vercel with the **Vite** preset (build: `npm run build`, output: `dist`).
2. Add `GEMINI_API_KEY` under Project Settings → Environment Variables (Production + Preview).
3. Every push to `main` deploys to production, and every branch gets its own preview URL.

---

## 🦉 How Athena Works

The browser sends `POST /api/athena` with the level, the parts you placed, and the computed check result. Athena then gives a **graded hint**:

- **Attempts 1–2:** a nudge that names the concept and the number that's off
- **Attempt 3+:** the exact fix, with the math

She's instructed to use only the numbers the engine provides, so she can't invent component values. If the API is down, slow (8 s timeout), or errors out, the game automatically switches to each level's canned hints, so it always works.

---

## 🔦 Bonus: The Real Lighthouse

The Level 5 answer works on a real breadboard: a 555 timer, a 9 V battery, R1 = 1 kΩ, R2 = 68 kΩ, C = 10 µF, an LED, and a current-limiting resistor. It blinks at about 1 Hz, matching the game.

---

## 👥 Team

| Role | Name |
|------|------|
| Content & physics lead | _Person A_ |
| Frontend & game builder | _Person B_ |
| Integration & deploy | _Person C_ |
| Story, design & demo | _Person D_ |

## 🧭 What's Next

- Free-wiring mode
- More parts and islands: transistors, op-amps, Scylla
- Teacher mode for classrooms

## 🏆 Prize Tracks

- Best Use of Gemini API
- SpaceXSI: Cursor + Grok Bot A-team
- Best Domain from GoDaddy Registry

---

_Made with ⚡ and a little help from the gods at hackUTA 2026._
