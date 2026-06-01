# PixelSurvivor

A browser-based survival game built with Vue 3 for PixelForge Studio.

## Student Info
- Name: [Your Name]
- Student ID: [Your ID]

## Live URL
[Paste your Netlify URL here after deploying]

## GitHub Repo
[Paste your GitHub URL here]

## How to Run Locally

```bash
npm install
npm run dev
```

Then open http://localhost:5173 in your browser.

## How to Build for Deployment

```bash
npm run build
```

## Interaction Model

Click-to-move mechanic: the player clicks anywhere on the board to move
their character to that position. Hazards spawn from random edges and home
in on the player. This suits a non-technical audience because it requires
zero keyboard knowledge — anyone can click. It creates instant tension
without complex controls, ideal for a 60-second session.

## Challenge Encountered

Making hazards home in on the player smoothly while scaling difficulty.
Solved by recalculating velocity vectors at spawn time based on the
player's current position, then multiplying by a difficulty multiplier
that grows every 10 points. Using watch(score) in Vue made difficulty
scaling declarative and clean.

## Bonus Features
- High score tracker stored in Vue state (resets on page refresh)
- Health bar turns red when health drops below 30%
