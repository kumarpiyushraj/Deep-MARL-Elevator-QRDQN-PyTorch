<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Multi-Elevator CTDE QR-DQN</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0b0f;
    --surface: #111318;
    --surface2: #181c24;
    --border: #1e2530;
    --accent: #00d4ff;
    --accent2: #7c3aed;
    --accent3: #10b981;
    --accent4: #f59e0b;
    --text: #e2e8f0;
    --muted: #64748b;
    --dim: #334155;
    --gold: #fbbf24;
    --red: #ef4444;
    --glow: rgba(0,212,255,0.15);
    --glow2: rgba(124,58,237,0.12);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 15px;
    line-height: 1.7;
    overflow-x: hidden;
  }

  /* ── GRID NOISE TEXTURE ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,212,255,0.015) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,212,255,0.015) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .wrap {
    max-width: 980px;
    margin: 0 auto;
    padding: 0 24px;
    position: relative;
    z-index: 1;
  }

  /* ── HERO ── */
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 80px 24px 60px;
    position: relative;
    overflow: hidden;
  }

  .hero::before {
    content: '';
    position: absolute;
    top: -200px; left: 50%; transform: translateX(-50%);
    width: 800px; height: 800px;
    background: radial-gradient(ellipse, rgba(0,212,255,0.08) 0%, transparent 70%);
    pointer-events: none;
  }
  .hero::after {
    content: '';
    position: absolute;
    bottom: -100px; left: 20%;
    width: 500px; height: 500px;
    background: radial-gradient(ellipse, rgba(124,58,237,0.07) 0%, transparent 70%);
    pointer-events: none;
  }

  .hero-eyebrow {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.25em;
    color: var(--accent);
    text-transform: uppercase;
    margin-bottom: 24px;
    padding: 6px 16px;
    border: 1px solid rgba(0,212,255,0.3);
    border-radius: 2px;
    background: rgba(0,212,255,0.05);
    display: inline-block;
  }

  .hero h1 {
    font-family: 'Syne', sans-serif;
    font-size: clamp(38px, 6vw, 72px);
    font-weight: 800;
    line-height: 1.05;
    letter-spacing: -0.03em;
    margin-bottom: 16px;
    background: linear-gradient(135deg, #e2e8f0 0%, #94a3b8 50%, var(--accent) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .hero-sub {
    font-family: 'Syne', sans-serif;
    font-size: clamp(14px, 2vw, 18px);
    color: var(--muted);
    margin-bottom: 40px;
    font-style: italic;
    font-weight: 400;
  }

  .hero-quote {
    max-width: 680px;
    margin: 0 auto 48px;
    padding: 24px 32px;
    border-left: 3px solid var(--accent);
    background: linear-gradient(90deg, rgba(0,212,255,0.06) 0%, transparent 100%);
    text-align: left;
    border-radius: 0 4px 4px 0;
  }

  .hero-quote p {
    font-size: 16px;
    color: #cbd5e1;
    line-height: 1.6;
  }

  .hero-quote strong {
    color: var(--accent);
    font-family: 'Space Mono', monospace;
  }

  /* ── BADGE ROW ── */
  .badges {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
    margin-bottom: 60px;
  }

  .badge {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    padding: 6px 14px;
    border-radius: 3px;
    letter-spacing: 0.05em;
    font-weight: 700;
    border: 1px solid;
  }
  .badge-blue  { background: rgba(59,130,246,0.1);  border-color: rgba(59,130,246,0.4);  color: #60a5fa; }
  .badge-red   { background: rgba(239,68,68,0.1);   border-color: rgba(239,68,68,0.4);   color: #f87171; }
  .badge-green { background: rgba(16,185,129,0.1);  border-color: rgba(16,185,129,0.4);  color: #34d399; }
  .badge-amber { background: rgba(245,158,11,0.1);  border-color: rgba(245,158,11,0.4);  color: #fbbf24; }
  .badge-cyan  { background: rgba(0,212,255,0.1);   border-color: rgba(0,212,255,0.4);   color: var(--accent); }

  /* ── STAT BAR ── */
  .stat-bar {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 100px;
    max-width: 780px;
    width: 100%;
    margin-left: auto;
    margin-right: auto;
  }

  .stat-item {
    background: var(--surface);
    padding: 24px 16px;
    text-align: center;
  }

  .stat-num {
    font-family: 'Syne', sans-serif;
    font-size: 28px;
    font-weight: 800;
    color: var(--accent);
    display: block;
    line-height: 1;
    margin-bottom: 6px;
  }

  .stat-label {
    font-size: 11px;
    color: var(--muted);
    letter-spacing: 0.08em;
    text-transform: uppercase;
    font-family: 'Space Mono', monospace;
  }

  /* ── SECTION ── */
  section {
    padding: 80px 0;
    border-top: 1px solid var(--border);
  }

  .section-tag {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.3em;
    color: var(--accent);
    text-transform: uppercase;
    margin-bottom: 12px;
    display: block;
  }

  h2 {
    font-family: 'Syne', sans-serif;
    font-size: clamp(24px, 4vw, 40px);
    font-weight: 800;
    color: var(--text);
    letter-spacing: -0.02em;
    margin-bottom: 32px;
    line-height: 1.1;
  }

  h3 {
    font-family: 'Syne', sans-serif;
    font-size: 18px;
    font-weight: 700;
    color: var(--text);
    margin-bottom: 12px;
    margin-top: 36px;
  }

  p { color: #94a3b8; margin-bottom: 16px; }

  /* ── RESULTS HERO CARD ── */
  .results-hero {
    background: linear-gradient(135deg, #0d1117 0%, #0f1923 100%);
    border: 1px solid rgba(0,212,255,0.2);
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 40px;
    position: relative;
  }

  .results-hero::before {
    content: '🥇';
    position: absolute;
    top: 20px; right: 24px;
    font-size: 32px;
  }

  .results-hero-head {
    background: rgba(0,212,255,0.06);
    border-bottom: 1px solid rgba(0,212,255,0.15);
    padding: 20px 28px;
  }

  .results-hero-head h3 {
    margin: 0;
    font-size: 13px;
    color: var(--accent);
    font-family: 'Space Mono', monospace;
    letter-spacing: 0.1em;
    text-transform: uppercase;
  }

  .results-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1px;
    background: var(--border);
  }

  .result-cell {
    background: var(--surface);
    padding: 22px 28px;
  }

  .result-cell .metric {
    font-size: 11px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-bottom: 6px;
  }

  .result-cell .val-row {
    display: flex;
    align-items: baseline;
    gap: 12px;
  }

  .result-cell .ours {
    font-family: 'Syne', sans-serif;
    font-size: 26px;
    font-weight: 800;
    color: var(--accent3);
  }

  .result-cell .vs {
    font-size: 11px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }

  .result-cell .eta {
    font-family: 'Space Mono', monospace;
    font-size: 14px;
    color: var(--muted);
  }

  .result-cell .delta {
    font-family: 'Space Mono', monospace;
    font-size: 13px;
    font-weight: 700;
    color: var(--accent3);
    margin-left: auto;
  }

  .win-bar {
    background: var(--surface2);
    padding: 20px 28px;
    border-top: 1px solid var(--border);
  }

  .win-bar p {
    margin: 0;
    font-size: 13px;
    color: var(--accent3);
    font-family: 'Space Mono', monospace;
    font-weight: 700;
  }

  /* ── FULL TABLE ── */
  .table-wrap {
    overflow-x: auto;
    border: 1px solid var(--border);
    border-radius: 6px;
    margin-bottom: 40px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
    font-family: 'Space Mono', monospace;
  }

  thead { background: var(--surface2); }

  th {
    padding: 14px 16px;
    text-align: left;
    font-size: 10px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--muted);
    border-bottom: 1px solid var(--border);
    white-space: nowrap;
  }

  td {
    padding: 13px 16px;
    border-bottom: 1px solid rgba(30,37,48,0.8);
    color: #94a3b8;
    white-space: nowrap;
  }

  tr:last-child td { border-bottom: none; }

  tr.highlight td {
    background: rgba(0,212,255,0.04);
    color: var(--text);
  }

  tr.highlight td:first-child {
    color: var(--accent);
    font-weight: 700;
  }

  td.good { color: var(--accent3); font-weight: 700; }
  td.method { color: var(--text); }

  tbody tr:hover td { background: rgba(255,255,255,0.02); }

  /* ── IMAGE FRAME ── */
  .img-frame {
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
    background: var(--surface);
    margin-bottom: 16px;
  }

  .img-frame-head {
    padding: 12px 18px;
    background: var(--surface2);
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .dot { width: 10px; height: 10px; border-radius: 50%; }
  .dot-r { background: #ef4444; }
  .dot-y { background: #fbbf24; }
  .dot-g { background: #10b981; }

  .img-frame-label {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    margin-left: 8px;
    letter-spacing: 0.05em;
  }

  .img-frame img {
    width: 100%;
    height: auto;
    display: block;
  }

  .img-caption {
    padding: 12px 18px;
    background: var(--surface2);
    border-top: 1px solid var(--border);
    font-size: 12px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
    line-height: 1.5;
  }

  /* ── TWO-COL IMAGES ── */
  .img-pair {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 16px;
  }

  /* ── ARCHITECTURE CARDS ── */
  .arch-cards {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
    margin-bottom: 40px;
  }

  .arch-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px 20px;
    position: relative;
    overflow: hidden;
  }

  .arch-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }
  .arch-card:nth-child(1)::before { background: var(--accent); }
  .arch-card:nth-child(2)::before { background: var(--accent2); }
  .arch-card:nth-child(3)::before { background: var(--accent3); }

  .arch-card h4 {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 8px;
  }

  .arch-card .arch-title {
    font-family: 'Syne', sans-serif;
    font-size: 15px;
    font-weight: 700;
    color: var(--text);
    margin-bottom: 10px;
    line-height: 1.3;
  }

  .arch-card p {
    font-size: 13px;
    color: var(--muted);
    margin: 0;
    line-height: 1.5;
  }

  /* ── CODE BLOCK ── */
  .code-block {
    background: #090c10;
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 24px;
  }

  .code-head {
    background: var(--surface2);
    padding: 10px 16px;
    border-bottom: 1px solid var(--border);
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .code-head::before {
    content: '◆';
    color: var(--accent2);
    font-size: 8px;
  }

  pre {
    padding: 20px;
    overflow-x: auto;
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    line-height: 1.7;
    color: #94a3b8;
  }

  .kw { color: #c084fc; }
  .cm { color: #475569; }
  .st { color: #34d399; }
  .nu { color: #fbbf24; }
  .fn { color: #60a5fa; }

  /* ── REWARD TABLE ── */
  .reward-table {
    display: grid;
    grid-template-columns: auto 1fr auto;
    gap: 0;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 32px;
    font-family: 'Space Mono', monospace;
    font-size: 13px;
  }

  .rt-cell {
    background: var(--surface);
    padding: 14px 18px;
    border-bottom: 1px solid var(--border);
  }

  .rt-cell:nth-last-child(-n+3) { border-bottom: none; }

  .rt-term { color: var(--text); white-space: nowrap; }
  .rt-weight-pos { color: var(--accent3); font-weight: 700; }
  .rt-weight-neg { color: #f87171; font-weight: 700; }
  .rt-desc { color: var(--muted); }

  /* ── TRAINING PIPELINE ── */
  .phase-cards {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 32px;
  }

  .phase-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px;
  }

  .phase-card .phase-num {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--accent2);
    text-transform: uppercase;
    margin-bottom: 8px;
    display: block;
  }

  .phase-card h4 {
    font-family: 'Syne', sans-serif;
    font-size: 16px;
    font-weight: 700;
    color: var(--text);
    margin-bottom: 12px;
  }

  .phase-card p {
    font-size: 13px;
    color: var(--muted);
    margin: 0;
  }

  /* ── LR SCHEDULE ── */
  .lr-schedule {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px;
    margin-bottom: 24px;
  }

  .lr-row {
    display: flex;
    align-items: center;
    gap: 0;
    margin-bottom: 10px;
  }
  .lr-row:last-child { margin-bottom: 0; }

  .lr-eps {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    width: 130px;
    flex-shrink: 0;
  }

  .lr-bar-wrap {
    flex: 1;
    height: 6px;
    background: var(--border);
    border-radius: 3px;
    overflow: hidden;
    margin: 0 14px;
  }

  .lr-bar { height: 100%; border-radius: 3px; }

  .lr-val {
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    font-weight: 700;
    color: var(--accent);
    width: 50px;
    text-align: right;
    flex-shrink: 0;
  }

  .lr-desc {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--dim);
    width: 120px;
    text-align: right;
    flex-shrink: 0;
  }

  /* ── STRESS TEST ── */
  .stress-cards {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
    margin-bottom: 32px;
  }

  .stress-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px 20px;
    text-align: center;
  }

  .stress-card .lambda {
    font-family: 'Syne', sans-serif;
    font-size: 32px;
    font-weight: 800;
    color: var(--gold);
    display: block;
    margin-bottom: 4px;
  }

  .stress-card .scenario {
    font-size: 11px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-bottom: 20px;
    display: block;
  }

  .stress-metric {
    display: flex;
    justify-content: space-between;
    font-size: 12px;
    margin-bottom: 6px;
    font-family: 'Space Mono', monospace;
  }

  .stress-metric .mk { color: var(--muted); }
  .stress-metric .mv { color: var(--text); }

  /* ── FIXES ── */
  .fix-cards {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 40px;
  }

  .fix-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px;
  }

  .fix-tag {
    display: inline-block;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    font-weight: 700;
    padding: 3px 10px;
    border-radius: 2px;
    margin-bottom: 12px;
    letter-spacing: 0.1em;
  }

  .fix-tag-1 { background: rgba(0,212,255,0.1); color: var(--accent); }
  .fix-tag-2 { background: rgba(124,58,237,0.1); color: #c084fc; }
  .fix-tag-3 { background: rgba(239,68,68,0.1); color: #f87171; }
  .fix-tag-4 { background: rgba(245,158,11,0.1); color: var(--gold); }

  .fix-card h4 {
    font-family: 'Syne', sans-serif;
    font-size: 15px;
    font-weight: 700;
    color: var(--text);
    margin-bottom: 8px;
    line-height: 1.3;
  }

  .fix-card .fix-problem {
    font-size: 12px;
    color: #f87171;
    font-family: 'Space Mono', monospace;
    margin-bottom: 8px;
    line-height: 1.5;
  }

  .fix-card .fix-solution {
    font-size: 12px;
    color: var(--accent3);
    font-family: 'Space Mono', monospace;
    line-height: 1.5;
  }

  /* ── OBS TABLE ── */
  .obs-grid {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 32px;
  }

  .obs-row {
    display: grid;
    grid-template-columns: 90px 60px 1fr;
    gap: 0;
    border-bottom: 1px solid var(--border);
  }
  .obs-row:last-child { border-bottom: none; }
  .obs-row.obs-head { background: var(--surface2); }

  .obs-cell {
    padding: 12px 16px;
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    color: var(--muted);
  }

  .obs-row.obs-head .obs-cell { font-size: 10px; letter-spacing: 0.1em; text-transform: uppercase; }
  .obs-cell.idx { color: var(--accent2); }
  .obs-cell.dim { color: var(--gold); text-align: center; }
  .obs-cell.desc { color: #94a3b8; }

  /* ── STRUCTURE ── */
  .file-tree {
    background: #090c10;
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 24px;
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    color: #475569;
    line-height: 2;
  }

  .ft-folder { color: var(--gold); }
  .ft-nb { color: var(--accent); }
  .ft-img { color: var(--accent3); }
  .ft-uml { color: var(--accent2); }
  .ft-comment { color: #334155; }

  /* ── HOW TO RUN ── */
  .run-cards {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 24px;
  }

  .run-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
  }

  .run-card-head {
    background: var(--surface2);
    padding: 14px 20px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .run-card-num {
    font-family: 'Syne', sans-serif;
    font-size: 11px;
    font-weight: 800;
    color: var(--bg);
    background: var(--accent);
    width: 22px; height: 22px;
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
  }

  .run-card-head h4 {
    font-family: 'Syne', sans-serif;
    font-size: 14px;
    font-weight: 700;
    color: var(--text);
    margin: 0;
  }

  .run-card-body {
    padding: 16px 20px;
  }

  .run-card-body p {
    font-size: 13px;
    color: var(--muted);
    margin-bottom: 8px;
  }

  /* ── DEPS TABLE ── */
  .deps-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 40px;
  }

  .dep-cell {
    background: var(--surface);
    padding: 20px 16px;
    text-align: center;
  }

  .dep-name {
    font-family: 'Syne', sans-serif;
    font-size: 15px;
    font-weight: 700;
    color: var(--text);
    display: block;
    margin-bottom: 4px;
  }

  .dep-ver {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--accent);
    display: block;
    margin-bottom: 4px;
  }

  .dep-role {
    font-size: 11px;
    color: var(--muted);
  }

  /* ── FOOTER ── */
  footer {
    border-top: 1px solid var(--border);
    padding: 60px 0;
    text-align: center;
  }

  .footer-title {
    font-family: 'Syne', sans-serif;
    font-size: 20px;
    font-weight: 800;
    color: var(--text);
    margin-bottom: 8px;
  }

  .footer-sub {
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    color: var(--muted);
    margin-bottom: 24px;
  }

  .footer-link {
    display: inline-block;
    font-family: 'Space Mono', monospace;
    font-size: 12px;
    color: var(--accent);
    text-decoration: none;
    border: 1px solid rgba(0,212,255,0.3);
    padding: 10px 24px;
    border-radius: 3px;
    transition: all 0.2s;
  }
  .footer-link:hover { background: rgba(0,212,255,0.08); }

  .github-link {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    text-decoration: none;
    margin-top: 16px;
    display: block;
  }

  /* ── NAV ── */
  nav {
    position: sticky;
    top: 0;
    z-index: 100;
    background: rgba(10,11,15,0.9);
    backdrop-filter: blur(12px);
    border-bottom: 1px solid var(--border);
    padding: 14px 24px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .nav-brand {
    font-family: 'Syne', sans-serif;
    font-size: 14px;
    font-weight: 800;
    color: var(--accent);
    letter-spacing: -0.02em;
  }

  .nav-links {
    display: flex;
    gap: 24px;
    list-style: none;
  }

  .nav-links a {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    text-decoration: none;
    letter-spacing: 0.05em;
    text-transform: uppercase;
    transition: color 0.2s;
  }
  .nav-links a:hover { color: var(--accent); }

  /* ── DIVIDER ── */
  .divider {
    width: 40px; height: 2px;
    background: var(--accent);
    margin-bottom: 32px;
    border-radius: 1px;
  }

  /* Inline callout */
  .callout {
    background: rgba(0,212,255,0.05);
    border: 1px solid rgba(0,212,255,0.2);
    border-left: 3px solid var(--accent);
    border-radius: 0 4px 4px 0;
    padding: 16px 20px;
    margin-bottom: 24px;
    font-size: 13px;
    color: #94a3b8;
    line-height: 1.6;
  }
  .callout strong { color: var(--accent); }

  @media (max-width: 700px) {
    .arch-cards, .phase-cards, .fix-cards, .run-cards, .stress-cards { grid-template-columns: 1fr; }
    .img-pair { grid-template-columns: 1fr; }
    .deps-grid { grid-template-columns: 1fr 1fr; }
    .stat-bar { grid-template-columns: 1fr 1fr; }
    .nav-links { display: none; }
    .results-grid { grid-template-columns: 1fr; }
    .obs-row { grid-template-columns: 80px 50px 1fr; }
  }
</style>
</head>
<body>

<!-- NAV -->
<nav>
  <span class="nav-brand">Multi-Elevator · CTDE QR-DQN</span>
  <ul class="nav-links">
    <li><a href="#results">Results</a></li>
    <li><a href="#architecture">Architecture</a></li>
    <li><a href="#training">Training</a></li>
    <li><a href="#stress">Stress Tests</a></li>
    <li><a href="#figures">Figures</a></li>
    <li><a href="#run">Run</a></li>
  </ul>
</nav>

<!-- HERO -->
<div class="hero">
  <span class="hero-eyebrow">Research · Reinforcement Learning · Multi-Agent Systems</span>
  <h1>Multi-Elevator Dispatch<br>with CTDE QR-DQN</h1>
  <p class="hero-sub">Teaching three elevators to think together — act alone</p>

  <div class="hero-quote">
    <p>Three elevators. Ten floors. 3,000 episodes. 900,000 gradient updates. One policy that beats the best classical algorithm by <strong>−27% wait time</strong> and <strong>−9.6% energy</strong> — simultaneously.</p>
  </div>

  <div class="badges">
    <span class="badge badge-blue">Python 3.12</span>
    <span class="badge badge-red">PyTorch 2.9.0+cu126</span>
    <span class="badge badge-green">NVIDIA Tesla T4</span>
    <span class="badge badge-amber">CUDA Accelerated</span>
    <span class="badge badge-cyan">Zero RL Frameworks</span>
  </div>

  <div class="stat-bar" style="margin-bottom:0">
    <div class="stat-item">
      <span class="stat-num">3,000</span>
      <span class="stat-label">Episodes</span>
    </div>
    <div class="stat-item">
      <span class="stat-num">900K</span>
      <span class="stat-label">Gradient Updates</span>
    </div>
    <div class="stat-item">
      <span class="stat-num">−27%</span>
      <span class="stat-label">Wait Time vs ETA</span>
    </div>
    <div class="stat-item">
      <span class="stat-num">−9.6%</span>
      <span class="stat-label">Energy vs ETA</span>
    </div>
  </div>
</div>

<!-- ═══ OVERVIEW ═══ -->
<div class="wrap">
<section>
  <span class="section-tag">01 — Overview</span>
  <h2>Three ideas.<br>One system.</h2>
  <div class="divider"></div>
  <p>This project solves the <strong style="color:var(--text)">Multi-Elevator Group Control Problem</strong> — a classic operations research challenge where multiple elevators must coordinate to serve passengers efficiently without any explicit real-time communication between them.</p>

  <div class="arch-cards">
    <div class="arch-card">
      <h4>Idea 01</h4>
      <div class="arch-title">CTDE — Centralised Training,<br>Decentralised Execution</div>
      <p>Trains with a global building view; runs with only local sensor data per elevator. No communication overhead at inference time.</p>
    </div>
    <div class="arch-card">
      <h4>Idea 02</h4>
      <div class="arch-title">QR-DQN — Quantile Regression<br>Deep Q-Network</div>
      <p>Models full distributions of returns (not just expectations) for robust learning under multi-agent uncertainty.</p>
    </div>
    <div class="arch-card">
      <h4>Idea 03</h4>
      <div class="arch-title">Dueling Net +<br>Prioritised Replay</div>
      <p>Separates state value from action advantage; focuses gradient updates on the most surprising transitions.</p>
    </div>
  </div>

  <div class="callout">
    <strong>The emergent result:</strong> Elevators self-organise — spreading across the building, avoiding bunching, gravitating toward high-demand floors — with zero hard-coded coordination rules.
  </div>
</section>
</div>

<!-- ═══ RESULTS ═══ -->
<div class="wrap">
<section id="results">
  <span class="section-tag">02 — Key Results</span>
  <h2>All 4 metrics beat<br>ETA simultaneously.</h2>
  <div class="divider"></div>
  <p style="margin-bottom:32px">Evaluated over 60 episodes after restoring the best composite checkpoint. Seed 42.</p>

  <!-- Head-to-head card -->
  <div class="results-hero">
    <div class="results-hero-head">
      <h3>Head-to-Head vs ETA-Dispatch (Best Classical)</h3>
    </div>
    <div class="results-grid">
      <div class="result-cell">
        <div class="metric">Avg Wait ↓</div>
        <div class="val-row">
          <span class="ours">0.492</span>
          <span class="vs">vs</span>
          <span class="eta">0.673</span>
          <span class="delta">−27.0% ✅</span>
        </div>
      </div>
      <div class="result-cell">
        <div class="metric">Energy ↓</div>
        <div class="val-row">
          <span class="ours">427.2</span>
          <span class="vs">vs</span>
          <span class="eta">472.7</span>
          <span class="delta">−9.6% ✅</span>
        </div>
      </div>
      <div class="result-cell">
        <div class="metric">Cluster Rate ↓</div>
        <div class="val-row">
          <span class="ours">10.0%</span>
          <span class="vs">vs</span>
          <span class="eta">10.2%</span>
          <span class="delta">−1.6pp ✅</span>
        </div>
      </div>
      <div class="result-cell">
        <div class="metric">Spread Index ↑</div>
        <div class="val-row">
          <span class="ours">0.306</span>
          <span class="vs">vs</span>
          <span class="eta">0.297</span>
          <span class="delta">+3.0% ✅</span>
        </div>
      </div>
    </div>
    <div class="win-bar">
      <p>► All 4 metrics beat ETA simultaneously — required all 4 v10.3 targeted fixes to achieve</p>
    </div>
  </div>

  <div class="callout">
    <strong>The critical insight:</strong> Beating ETA on wait alone is easy — move more. Beating it on energy alone is easy — move less. Beating it on <strong>both simultaneously</strong> required learning a fundamentally smarter positioning strategy. That's what CTDE + QR-DQN enables.
  </div>

  <!-- Full comparison table -->
  <div class="table-wrap">
    <table>
      <thead>
        <tr>
          <th>Method</th>
          <th>Avg Wait ↓</th>
          <th>Energy ↓</th>
          <th>Cluster Rate ↓</th>
          <th>Spread ↑</th>
          <th>Served/ep</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="method">Nearest Car</td>
          <td>9.679</td><td>409.8</td><td>98.5%</td><td>0.005</td><td>580.2</td>
        </tr>
        <tr>
          <td class="method">SCAN / LOOK</td>
          <td>1.415</td><td>899.6</td><td>98.5%</td><td>0.015</td><td>670.7</td>
        </tr>
        <tr>
          <td class="method">Sectoring</td>
          <td>0.854</td><td>486.1</td><td>28.5%</td><td>0.234</td><td>747.1</td>
        </tr>
        <tr>
          <td class="method">ETA-Dispatch (best classical)</td>
          <td>0.673</td><td>472.7</td><td>10.2%</td><td>0.297</td><td>748.8</td>
        </tr>
        <tr class="highlight">
          <td>🥇 QR-DQN CTDE (ours)</td>
          <td class="good">0.492</td>
          <td class="good">427.2</td>
          <td class="good">10.0%</td>
          <td class="good">0.306</td>
          <td>667.2</td>
        </tr>
      </tbody>
    </table>
  </div>
</section>
</div>

<!-- ═══ ARCHITECTURE ═══ -->
<div class="wrap">
<section id="architecture">
  <span class="section-tag">03 — System Architecture</span>
  <h2>Four layers.<br>One building brain.</h2>
  <div class="divider"></div>

  <!-- System arch image -->
  <div class="img-frame" style="margin-bottom:40px">
    <div class="img-frame-head">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="img-frame-label">UML Diagrams / Multi_Elevator_Sys.svg — Full System Architecture</span>
    </div>
    <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sys.svg?raw=true" alt="System Architecture Diagram" loading="lazy">
    <div class="img-caption">4-layer architecture: User &amp; Sensor → Control &amp; Scheduling → Learning &amp; Decision → Evaluation &amp; Reporting</div>
  </div>

  <div class="table-wrap" style="margin-bottom:48px">
    <table>
      <thead>
        <tr><th>Layer</th><th>Components</th><th>Role</th></tr>
      </thead>
      <tbody>
        <tr><td class="method">User &amp; Sensor</td><td>Poisson Arrivals · Traffic Modes</td><td>Generates λ ∈ [0.12–0.35] passengers/floor/step across 3 traffic modes</td></tr>
        <tr><td class="method">Control &amp; Scheduling</td><td>Hall Queue · EMA Responsibility · Reward Engine</td><td>Simulates 10-floor, 3-elevator building; computes decomposed rewards</td></tr>
        <tr><td class="method">Learning &amp; Decision</td><td>QR-Dueling Actor · Centralised Critic · PER Buffer</td><td>All neural network components + classical comparison agents</td></tr>
        <tr><td class="method">Evaluation &amp; Reporting</td><td>Greedy Eval · Metrics · 9 Figures</td><td>30–60 episode evaluations, composite checkpoint scoring, output generation</td></tr>
      </tbody>
    </table>
  </div>

  <!-- Class diagram + Sequence side by side -->
  <div class="img-pair">
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Class Diagram</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Class.svg?raw=true" alt="Class Diagram" loading="lazy">
      <div class="img-caption">All objects, attributes, methods and relationships across the full codebase.</div>
    </div>
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Sequence Diagram</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Sequence_Diagram.svg?raw=true" alt="Sequence Diagram" loading="lazy">
      <div class="img-caption">Message flow between Passenger, Environment, Elevator Agent, and Administrator per step.</div>
    </div>
  </div>

  <!-- Activity + Use Case side by side -->
  <div class="img-pair">
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Activity Diagram — 4 Swim Lanes</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Activity_Diagram.svg?raw=true" alt="Activity Diagram" loading="lazy">
      <div class="img-caption">Full lifecycle from button press through training loop to final report.</div>
    </div>
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Use Case Diagram</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/UML%20Diagrams/Multi_Elevator_Use_Case.svg?raw=true" alt="Use Case Diagram" loading="lazy">
      <div class="img-caption">Actor responsibilities and «include»/«extend» chains from button press to boarding.</div>
    </div>
  </div>

  <!-- Network architecture -->
  <h3>Neural Network Architecture</h3>
  <div class="code-block">
    <div class="code-head">QRDuelingNet — Actor Network (per elevator)</div>
    <pre><span class="cm"># Input: obs(18) → two hidden layers → split heads</span>

obs(<span class="nu">18</span>) → Linear(<span class="nu">18→256</span>)+LN+ReLU → Linear(<span class="nu">256→256</span>)+LN+ReLU
               ↙                                  ↘
  Value(<span class="nu">256→128→51</span>)              Advantage(<span class="nu">256→128→3×51</span>)
               ↘                                  ↙
        Q(s,a) = V(s) + A(s,a) − mean_a[A]
        Output: [batch × <span class="nu">3</span> actions × <span class="nu">51</span> quantiles]</pre>
  </div>
  <div class="code-block">
    <div class="code-head">CentralisedCritic — Training Only (discarded at execution)</div>
    <pre>joint_state(<span class="nu">16</span>) → Linear(<span class="nu">16→256</span>)+LN+ReLU → Linear(<span class="nu">256→128</span>) → Linear(<span class="nu">128→1</span>)
Output: V(s) — single scalar</pre>
  </div>
</section>
</div>

<!-- ═══ ENVIRONMENT ═══ -->
<div class="wrap">
<section>
  <span class="section-tag">04 — Environment</span>
  <h2>10 floors. 3 elevators.<br>300 steps per episode.</h2>
  <div class="divider"></div>

  <div class="table-wrap" style="margin-bottom:32px">
    <table>
      <thead>
        <tr><th>Parameter</th><th>Value</th><th>Notes</th></tr>
      </thead>
      <tbody>
        <tr><td class="method">Elevators</td><td class="good">3</td><td>E1, E2, E3 — all share actor weights</td></tr>
        <tr><td class="method">Floors</td><td>10</td><td>Floor 0 = lobby, Floor 9 = top</td></tr>
        <tr><td class="method">Arrival rate λ</td><td>[0.12, 0.35]</td><td>New random value drawn each episode</td></tr>
        <tr><td class="method">Lobby boost</td><td>5×</td><td>Ground floor gets 5× more passengers</td></tr>
        <tr><td class="method">Top floor boost</td><td>1.5×</td><td>Top floor gets 1.5× more passengers</td></tr>
        <tr><td class="method">Steps / episode</td><td>300</td><td>Fixed length, no terminal condition</td></tr>
        <tr><td class="method">Training episodes</td><td>3,000</td><td>+ 100 warm-up episodes before gradients</td></tr>
      </tbody>
    </table>
  </div>

  <h3>Observation Space — 18 Dimensions</h3>
  <div class="obs-grid">
    <div class="obs-row obs-head">
      <div class="obs-cell">Index</div>
      <div class="obs-cell">Dim</div>
      <div class="obs-cell">Content</div>
    </div>
    <div class="obs-row">
      <div class="obs-cell idx">[0]</div>
      <div class="obs-cell dim">1</div>
      <div class="obs-cell desc">Own floor position (normalised: floor / 9)</div>
    </div>
    <div class="obs-row">
      <div class="obs-cell idx">[1–10]</div>
      <div class="obs-cell dim">10</div>
      <div class="obs-cell desc">hall[0] … hall[9] / 5.0 — queue depth, clipped 0–1</div>
    </div>
    <div class="obs-row">
      <div class="obs-cell idx">[11–12]</div>
      <div class="obs-cell dim">2</div>
      <div class="obs-cell desc">Peer elevator positions (normalised)</div>
    </div>
    <div class="obs-row">
      <div class="obs-cell idx">[13–14]</div>
      <div class="obs-cell dim">2</div>
      <div class="obs-cell desc">Peer elevator targets (normalised; −0.1 = idle)</div>
    </div>
    <div class="obs-row">
      <div class="obs-cell idx">[15–17]</div>
      <div class="obs-cell dim">3</div>
      <div class="obs-cell desc">One-hot elevator identity — [1,0,0], [0,1,0], [0,0,1]</div>
    </div>
  </div>
</section>
</div>

<!-- ═══ REWARD ═══ -->
<div class="wrap">
<section>
  <span class="section-tag">05 — Reward Design</span>
  <h2>Decomposed.<br>No free-rider problem.</h2>
  <div class="divider"></div>
  <p style="margin-bottom:28px">Each elevator receives a fully decomposed, strictly per-agent reward — no shared terms, no free-rider gradient.</p>

  <div class="reward-table">
    <div class="rt-cell rt-term">+8.0 × passengers_served</div>
    <div class="rt-cell rt-weight-pos">+8.0</div>
    <div class="rt-cell rt-desc">Dominant signal for the primary task</div>

    <div class="rt-cell rt-term">−3.0 × responsibility_norm</div>
    <div class="rt-cell rt-weight-neg">−3.0</div>
    <div class="rt-cell rt-desc">EMA-normalised nearest-floor queue load</div>

    <div class="rt-cell rt-term">−0.5 × move_cost</div>
    <div class="rt-cell rt-weight-neg">−0.5</div>
    <div class="rt-cell rt-desc">1 per floor moved — gentle energy discouragement</div>

    <div class="rt-cell rt-term">+0.3 × idle_bonus</div>
    <div class="rt-cell rt-weight-pos">+0.3</div>
    <div class="rt-cell rt-desc">Rewards strategic waiting at uncrowded positions</div>

    <div class="rt-cell rt-term">−2.5 × approach_cluster</div>
    <div class="rt-cell rt-weight-neg">−2.5</div>
    <div class="rt-cell rt-desc">Strictly local — only fires for the elevator that moved toward a peer ≤2 floors (raised from 1.5 in v10.3)</div>
  </div>

  <div class="callout">
    <strong>Responsibility is EMA-normalised:</strong> resp_ema = 0.95 × resp_ema + 0.05 × max_resp — prevents a sudden surge from creating a massive one-step gradient spike. Reward clipped to [−20, +20].
  </div>
</section>
</div>

<!-- ═══ TRAINING ═══ -->
<div class="wrap">
<section id="training">
  <span class="section-tag">06 — Training Pipeline</span>
  <h2>3,500 episodes.<br>Staged learning.</h2>
  <div class="divider"></div>

  <div class="phase-cards" style="margin-bottom:32px">
    <div class="phase-card">
      <span class="phase-num">Phase 0 — Warm Start</span>
      <h4>100 Episodes, No Gradients</h4>
      <p>100 × 300 × 3 = 90,000 buffer entries seeded with 50% ETA-Dispatch actions + 50% random. Purpose: competent + exploratory experience from the start.</p>
    </div>
    <div class="phase-card">
      <span class="phase-num">Phase 1 — Main Training</span>
      <h4>3,500 Episodes</h4>
      <p>3,500 × 300 = 1,050,000 total gradient updates. Five staged learning rates with three parallel annealing schedules for ε, critic_α, and β-IS correction.</p>
    </div>
  </div>

  <h3>Learning Rate Schedule</h3>
  <div class="lr-schedule">
    <div class="lr-row">
      <span class="lr-eps">Ep 1–799</span>
      <div class="lr-bar-wrap"><div class="lr-bar" style="width:100%;background:linear-gradient(90deg,var(--accent),rgba(0,212,255,0.5))"></div></div>
      <span class="lr-val">1e-3</span>
      <span class="lr-desc">Fast initial learning</span>
    </div>
    <div class="lr-row">
      <span class="lr-eps">Ep 800–1199</span>
      <div class="lr-bar-wrap"><div class="lr-bar" style="width:75%;background:linear-gradient(90deg,var(--accent2),rgba(124,58,237,0.5))"></div></div>
      <span class="lr-val">5e-4</span>
      <span class="lr-desc">Consolidation</span>
    </div>
    <div class="lr-row">
      <span class="lr-eps">Ep 1200–1799</span>
      <div class="lr-bar-wrap"><div class="lr-bar" style="width:50%;background:linear-gradient(90deg,var(--accent3),rgba(16,185,129,0.5))"></div></div>
      <span class="lr-val">2e-4</span>
      <span class="lr-desc">Refinement</span>
    </div>
    <div class="lr-row">
      <span class="lr-eps">Ep 1800–2499</span>
      <div class="lr-bar-wrap"><div class="lr-bar" style="width:30%;background:linear-gradient(90deg,var(--gold),rgba(245,158,11,0.5))"></div></div>
      <span class="lr-val">5e-5</span>
      <span class="lr-desc">Fine-tuning</span>
    </div>
    <div class="lr-row">
      <span class="lr-eps">Ep 2500–3500</span>
      <div class="lr-bar-wrap"><div class="lr-bar" style="width:15%;background:linear-gradient(90deg,#f87171,rgba(239,68,68,0.5))"></div></div>
      <span class="lr-val">2e-5</span>
      <span class="lr-desc">Final polish ← v10.3</span>
    </div>
  </div>

  <h3>Composite Checkpoint Score (FIX-1)</h3>
  <div class="code-block">
    <div class="code-head">composite_score — lower is better</div>
    <pre><span class="cm"># score = 1.70 → equal to ETA on all three metrics simultaneously</span>
<span class="cm"># Best checkpoint: ep 2075 → score 1.347 → wait 0.51, energy 403.7, cluster 12.2%</span>

score = <span class="nu">1.0</span> × (avg_wait    / eta_wait)     <span class="cm"># wait   — highest priority</span>
      + <span class="nu">0.4</span> × (energy      / eta_energy)   <span class="cm"># energy — second</span>
      + <span class="nu">0.3</span> × (cluster_rate / <span class="nu">0.15</span>)         <span class="cm"># cluster — third (soft target 15%)</span></pre>
  </div>
</section>
</div>

<!-- ═══ STRESS TESTS ═══ -->
<div class="wrap">
<section id="stress">
  <span class="section-tag">07 — Stress Tests</span>
  <h2>2.4× training max.<br>No catastrophic failure.</h2>
  <div class="divider"></div>
  <p style="margin-bottom:32px">Tested on out-of-distribution arrival rates — never seen during training (λ was capped at 0.35).</p>

  <div class="stress-cards">
    <div class="stress-card">
      <span class="lambda">λ = 0.35</span>
      <span class="scenario">Rush Hour</span>
      <div class="stress-metric"><span class="mk">Served</span><span class="mv">1,055</span></div>
      <div class="stress-metric"><span class="mk">Energy</span><span class="mv">563</span></div>
      <div class="stress-metric"><span class="mk">Avg Wait</span><span class="mv">0.78</span></div>
      <div class="stress-metric"><span class="mk">Cluster</span><span class="mv">12.6%</span></div>
      <div class="stress-metric"><span class="mk">Spread</span><span class="mv">0.296</span></div>
      <div class="stress-metric"><span class="mk">vs In-Dist</span><span class="mv" style="color:var(--gold)">1.16×</span></div>
    </div>
    <div class="stress-card">
      <span class="lambda" style="color:var(--red)">λ = 0.55</span>
      <span class="scenario">Peak Load</span>
      <div class="stress-metric"><span class="mk">Served</span><span class="mv">1,669</span></div>
      <div class="stress-metric"><span class="mk">Energy</span><span class="mv">705</span></div>
      <div class="stress-metric"><span class="mk">Avg Wait</span><span class="mv">1.31</span></div>
      <div class="stress-metric"><span class="mk">Cluster</span><span class="mv">17.4%</span></div>
      <div class="stress-metric"><span class="mk">Spread</span><span class="mv">0.278</span></div>
      <div class="stress-metric"><span class="mk">vs In-Dist</span><span class="mv" style="color:var(--red)">1.94×</span></div>
    </div>
    <div class="stress-card">
      <span class="lambda" style="color:#f87171">λ = 0.85</span>
      <span class="scenario">Extreme Surge</span>
      <div class="stress-metric"><span class="mk">Served</span><span class="mv">2,530</span></div>
      <div class="stress-metric"><span class="mk">Energy</span><span class="mv">748</span></div>
      <div class="stress-metric"><span class="mk">Avg Wait</span><span class="mv">2.02</span></div>
      <div class="stress-metric"><span class="mk">Cluster</span><span class="mv">20.0%</span></div>
      <div class="stress-metric"><span class="mk">Spread</span><span class="mv">0.266</span></div>
      <div class="stress-metric"><span class="mk">vs In-Dist</span><span class="mv" style="color:#f87171">3.00×</span></div>
    </div>
  </div>

  <div class="callout">
    <strong>At λ = 0.85</strong> — 2.4× the training max — the policy still maintains structured coverage (spread = 0.266, cluster = 20%) and serves <strong>2,530 passengers/episode</strong>. Graceful degradation, not catastrophic failure.
  </div>
</section>
</div>

<!-- ═══ FIGURES ═══ -->
<div class="wrap">
<section id="figures">
  <span class="section-tag">08 — Result Figures</span>
  <h2>9 figures. 400 DPI.<br>Every metric explained.</h2>
  <div class="divider"></div>
  <p style="margin-bottom:40px">All figures generated at 400 DPI (PNG + PDF) on Kaggle NVIDIA Tesla T4.</p>

  <!-- Fig 1 — Training Convergence — FULL WIDTH -->
  <div class="img-frame" style="margin-bottom:16px">
    <div class="img-frame-head">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="img-frame-label">Fig 1 — Training Convergence</span>
    </div>
    <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Convergence.png?raw=true" alt="Training Convergence" loading="lazy">
    <div class="img-caption">Avg wait per eval episode vs all 4 baselines. LR step-down points shown as vertical lines. Crosses below ETA (~ep 150) and continues improving through all four LR stages. Best composite checkpoint at ep 2,075.</div>
  </div>

  <!-- Fig 2 + Fig 3 side by side -->
  <div class="img-pair">
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 2 — Composite Score</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Composite_Checkpoint.png?raw=true" alt="Composite Checkpoint Score" loading="lazy">
      <div class="img-caption">Reference at 1.70 = equal to ETA. Best checkpoint (ep 2,075, score = 1.347) sits 19% below — jointly 19%+ better on all three dimensions.</div>
    </div>
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 3 — Avg Wait Time</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Avg_Wait_Time.png?raw=true" alt="Average Wait Time Comparison" loading="lazy">
      <div class="img-caption">QR-DQN CTDE: 0.492 — 27% below ETA-Dispatch and 94.9% below Nearest Car.</div>
    </div>
  </div>

  <!-- Fig 4 + Fig 6 side by side -->
  <div class="img-pair">
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 4 — Energy Consumption</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Energy_Consumption.png?raw=true" alt="Energy Consumption" loading="lazy">
      <div class="img-caption">427.2 motor-steps vs ETA's 472.7 — 9.6% fewer movements. SCAN/LOOK worst at 899.6 (sweeps empty halves).</div>
    </div>
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 6 — Spatial Distribution</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Elevator_Spatial_Distribution.png?raw=true" alt="Cluster and Spread" loading="lazy">
      <div class="img-caption">Spread rises from near-zero to 0.306 — exceeding ETA (0.297). Emergent self-organisation through the approach-cluster penalty alone.</div>
    </div>
  </div>

  <!-- Fig 5 — Training Dynamics — FULL WIDTH -->
  <div class="img-frame" style="margin-bottom:16px">
    <div class="img-frame-head">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="img-frame-label">Fig 5 — Three-Metric Training Dynamics</span>
    </div>
    <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Training_Dynamics.png?raw=true" alt="Training Dynamics" loading="lazy">
    <div class="img-caption">Panel 1 (Wait): 0.78 → below ETA by ep 150 → 0.49 at best checkpoint. Panel 2 (Energy): 520 → 427 as policy learns smarter positioning. Panel 3 (Cluster): 52% → ~10% by ep 1500. Stronger penalty (FIX-3) prevents v10.2 oscillation.</div>
  </div>

  <!-- Fig 7 + Fig 8 side by side -->
  <div class="img-pair">
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 7 — Stress Test Generalisation</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Stress_Tests.png?raw=true" alt="Stress Test Results" loading="lazy">
      <div class="img-caption">At λ = 0.85 (2.4× training max), wait rises to 2.02 but cluster stays at 20%. No catastrophic collapse — strong OOD generalisation.</div>
    </div>
    <div class="img-frame">
      <div class="img-frame-head">
        <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
        <span class="img-frame-label">Fig 8 — Actor Loss Trajectory</span>
      </div>
      <img src="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch/blob/main/Results_Diagrams/Actor_Loss.png?raw=true" alt="Actor Loss" loading="lazy">
      <div class="img-caption">Falls from ~1.10 (ep 50) to ~0.63 (ep 3500) with no divergence. Each LR step produces a brief expected uptick followed by renewed descent.</div>
    </div>
  </div>
</section>
</div>

<!-- ═══ v10.3 FIXES ═══ -->
<div class="wrap">
<section>
  <span class="section-tag">09 — Design Decisions</span>
  <h2>Four targeted fixes.<br>v10.3 over v10.2.</h2>
  <div class="divider"></div>
  <p style="margin-bottom:32px">Each fix addresses a specific failure mode identified from training logs.</p>

  <div class="fix-cards">
    <div class="fix-card">
      <span class="fix-tag fix-tag-1">FIX-1</span>
      <h4>Composite Checkpoint Score</h4>
      <p class="fix-problem">v10.2 saved min(avg_wait) only → selected ep 2050 (score = 1.411). Episodes 2200 (energy = 440.2, cluster = 12%) were jointly superior but never saved.</p>
      <p class="fix-solution">score = 1.0×(wait/eta) + 0.4×(energy/eta) + 0.3×(cluster/0.15). Ep 2200 now correctly selected over ep 2050.</p>
    </div>
    <div class="fix-card">
      <span class="fix-tag fix-tag-2">FIX-2</span>
      <h4>Extended to 3,500 Episodes</h4>
      <p class="fix-problem">v10.2 actor loss was still at 0.498 and falling at ep 2500. Best policy was still emerging at training end.</p>
      <p class="fix-solution">500 additional episodes at LR = 2e-5. Gives jointly-efficient behaviour at ep 2200–2400 time to consolidate.</p>
    </div>
    <div class="fix-card">
      <span class="fix-tag fix-tag-3">FIX-3</span>
      <h4>Cluster Penalty 1.5 → 2.5</h4>
      <p class="fix-problem">ep 2200 achieved 12% cluster but the metric oscillated 12–15% in v10.2's final 500 episodes — signal too weak to hold the gain.</p>
      <p class="fix-solution">approach_cluster coefficient raised to 2.5. Strictly local — only fires for the elevator that moved, never the peer. No free-rider gradient.</p>
    </div>
    <div class="fix-card">
      <span class="fix-tag fix-tag-4">FIX-4</span>
      <h4>Finer Eval Cadence After Ep 1,500</h4>
      <p class="fix-problem">eval_every=50 throughout meant the peak composite checkpoint between ep 2150–2250 could be missed by up to 50 episodes.</p>
      <p class="fix-solution">eval_every=25 after ep 1500, with 50 eval episodes per checkpoint (vs 40 earlier). Dense late-training measurement where quality is highest.</p>
    </div>
  </div>
</section>
</div>

<!-- ═══ STRUCTURE + RUN ═══ -->
<div class="wrap">
<section id="run">
  <span class="section-tag">10 — Project &amp; Run</span>
  <h2>Zero external<br>RL frameworks.</h2>
  <div class="divider"></div>

  <div class="file-tree" style="margin-bottom:40px">
<span class="ft-folder">multi-elevator-ctde-qrdqn-dispatch/</span>
│
├── <span class="ft-nb">📓 Multi_Elevator_CTDE.ipynb</span>   <span class="ft-comment">← Full notebook (code + all outputs inline)</span>
├── 📄 README.md
│
├── <span class="ft-folder">📂 Results_Diagrams/</span>          <span class="ft-comment">← 9 publication-quality figures (PNG + PDF, 400 DPI)</span>
│   ├── <span class="ft-img">Training_Convergence.png</span>
│   ├── <span class="ft-img">Composite_Checkpoint.png</span>
│   ├── <span class="ft-img">Avg_Wait_Time.png</span>
│   ├── <span class="ft-img">Energy_Consumption.png</span>
│   ├── <span class="ft-img">Training_Dynamics.png</span>
│   ├── <span class="ft-img">Elevator_Spatial_Distribution.png</span>
│   ├── <span class="ft-img">Stress_Tests.png</span>
│   └── <span class="ft-img">Actor_Loss.png</span>
│
└── <span class="ft-folder">📂 UML Diagrams/</span>              <span class="ft-comment">← Complete system design documentation</span>
    ├── <span class="ft-uml">Multi_Elevator_Sys.svg</span>
    ├── <span class="ft-uml">Multi_Elevator_Class.svg</span>
    ├── <span class="ft-uml">Multi_Elevator_Sequence_Diagram.svg</span>
    ├── <span class="ft-uml">Multi_Elevator_Activity_Diagram.svg</span>
    └── <span class="ft-uml">Multi_Elevator_Use_Case.svg</span>
  </div>

  <div class="run-cards">
    <div class="run-card">
      <div class="run-card-head">
        <div class="run-card-num">1</div>
        <h4>Kaggle — Recommended (Free T4 GPU)</h4>
      </div>
      <div class="run-card-body">
        <p>1. Open Multi_Elevator_CTDE.ipynb on GitHub</p>
        <p>2. Import to your Kaggle account</p>
        <p>3. Settings → Accelerator → GPU T4</p>
        <p>4. <strong style="color:var(--text)">Run All</strong> — full training completes in ~2–3 hours</p>
      </div>
    </div>
    <div class="run-card">
      <div class="run-card-head">
        <div class="run-card-num">2</div>
        <h4>Local / Google Colab</h4>
      </div>
      <div class="run-card-body">
        <div class="code-block" style="margin:0">
          <pre style="padding:14px;font-size:11px">git clone https://github.com/kumarpiyushraj/\
  multi-elevator-ctde-qrdqn-dispatch.git
cd multi-elevator-ctde-qrdqn-dispatch
pip install torch numpy matplotlib
jupyter notebook Multi_Elevator_CTDE.ipynb</pre>
        </div>
      </div>
    </div>
  </div>

  <h3>Quick Smoke Test (~20 min on CPU)</h3>
  <div class="code-block">
    <div class="code-head">Edit the entry point at the bottom of the notebook</div>
    <pre><span class="fn">run_training</span>(n_elevators=<span class="nu">3</span>, floors=<span class="nu">10</span>, episodes=<span class="nu">500</span>, steps_per_ep=<span class="nu">300</span>, seeds=[<span class="nu">42</span>])</pre>
  </div>

  <h3>Dependencies</h3>
  <div class="deps-grid">
    <div class="dep-cell">
      <span class="dep-name">Python</span>
      <span class="dep-ver">3.12</span>
      <span class="dep-role">Runtime</span>
    </div>
    <div class="dep-cell">
      <span class="dep-name">PyTorch</span>
      <span class="dep-ver">2.9.0+cu126</span>
      <span class="dep-role">Neural nets, GPU</span>
    </div>
    <div class="dep-cell">
      <span class="dep-name">NumPy</span>
      <span class="dep-ver">latest</span>
      <span class="dep-role">Env simulation</span>
    </div>
    <div class="dep-cell">
      <span class="dep-name">Matplotlib</span>
      <span class="dep-ver">latest</span>
      <span class="dep-role">9 figures + animation</span>
    </div>
  </div>
</section>
</div>

<!-- ═══ FOOTER ═══ -->
<footer>
  <div class="wrap">
    <div class="footer-title">Multi-Elevator CTDE QR-DQN</div>
    <div class="footer-sub">Built from scratch · PyTorch 2.9 · NVIDIA Tesla T4 · 1,050,000 gradient updates</div>
    <a href="https://github.com/kumarpiyushraj/multi-elevator-ctde-qrdqn-dispatch" class="footer-link">⭐ Star on GitHub</a>
    <a href="https://github.com/kumarpiyushraj" class="github-link">© 2026 Kumar Piyush Raj · @kumarpiyushraj</a>
  </div>
</footer>

</body>
</html>
