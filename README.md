<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI OS Pro — 智能任务编排系统</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:wght@300;400;500&family=Noto+Sans+SC:wght@300;400;500;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #08080a;
    --bg-surface: #111114;
    --bg-elevated: #1a1a1f;
    --accent: #e8a82c;
    --accent-glow: rgba(232, 168, 44, 0.15);
    --accent-dim: #a07420;
    --text-primary: #eae6df;
    --text-secondary: #8a857e;
    --text-muted: #504d48;
    --border: #22222a;
    --node-gen: #e8a82c;
    --node-process: #4a9eff;
    --node-validate: #34d399;
    --node-output: #f472b6;
    --font-display: 'Syne', 'Noto Sans SC', sans-serif;
    --font-mono: 'DM Mono', monospace;
    --font-body: 'Noto Sans SC', sans-serif;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html {
    scroll-behavior: smooth;
    scrollbar-width: thin;
    scrollbar-color: var(--accent-dim) var(--bg);
  }

  body {
    background: var(--bg);
    color: var(--text-primary);
    font-family: var(--font-body);
    line-height: 1.7;
    overflow-x: hidden;
  }

  /* ── Grain Overlay ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='1'/%3E%3C/svg%3E");
    opacity: 0.03;
    pointer-events: none;
    z-index: 9999;
  }

  /* ── Navigation ── */
  nav {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1000;
    padding: 20px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: rgba(8, 8, 10, 0.85);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
  }

  .nav-logo {
    font-family: var(--font-display);
    font-weight: 800;
    font-size: 20px;
    letter-spacing: -0.02em;
    color: var(--text-primary);
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .nav-logo .logo-icon {
    width: 28px;
    height: 28px;
    background: var(--accent);
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
    color: var(--bg);
    font-weight: 800;
  }

  .nav-links {
    display: flex;
    gap: 32px;
    list-style: none;
  }

  .nav-links a {
    color: var(--text-secondary);
    text-decoration: none;
    font-size: 13px;
    font-family: var(--font-mono);
    letter-spacing: 0.04em;
    transition: color 0.2s;
  }

  .nav-links a:hover { color: var(--accent); }

  .nav-cta {
    padding: 8px 20px;
    background: var(--accent);
    color: var(--bg);
    border: none;
    border-radius: 6px;
    font-family: var(--font-mono);
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.06em;
    cursor: pointer;
    transition: all 0.2s;
  }

  .nav-cta:hover {
    background: #f0b83c;
    box-shadow: 0 0 24px var(--accent-glow);
  }

  /* ── Hero ── */
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 120px 48px 80px;
    position: relative;
    overflow: hidden;
  }

  .hero-bg-grid {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(232, 168, 44, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(232, 168, 44, 0.03) 1px, transparent 1px);
    background-size: 60px 60px;
    mask-image: radial-gradient(ellipse 70% 60% at 50% 50%, black 20%, transparent 80%);
    -webkit-mask-image: radial-gradient(ellipse 70% 60% at 50% 50%, black 20%, transparent 80%);
  }

  .hero-glow {
    position: absolute;
    width: 600px;
    height: 600px;
    border-radius: 50%;
    filter: blur(120px);
    opacity: 0.12;
    pointer-events: none;
  }

  .hero-glow-1 {
    top: -100px;
    left: 10%;
    background: var(--accent);
  }

  .hero-glow-2 {
    bottom: -200px;
    right: 5%;
    background: #4a9eff;
  }

  .hero-badge {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 6px 16px;
    background: var(--bg-elevated);
    border: 1px solid var(--border);
    border-radius: 100px;
    font-family: var(--font-mono);
    font-size: 12px;
    color: var(--text-secondary);
    margin-bottom: 32px;
    animation: fadeDown 0.6s ease forwards;
    opacity: 0;
  }

  .hero-badge .dot {
    width: 6px;
    height: 6px;
    background: var(--node-validate);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }

  .hero h1 {
    font-family: var(--font-display);
    font-size: clamp(42px, 6vw, 80px);
    font-weight: 800;
    line-height: 1.05;
    letter-spacing: -0.03em;
    margin-bottom: 24px;
    animation: fadeUp 0.8s ease 0.1s forwards;
    opacity: 0;
  }

  .hero h1 .accent { color: var(--accent); }

  .hero-sub {
    font-size: 18px;
    color: var(--text-secondary);
    max-width: 600px;
    line-height: 1.8;
    margin-bottom: 48px;
    animation: fadeUp 0.8s ease 0.2s forwards;
    opacity: 0;
  }

  .hero-actions {
    display: flex;
    gap: 16px;
    animation: fadeUp 0.8s ease 0.3s forwards;
    opacity: 0;
  }

  .btn-primary {
    padding: 14px 32px;
    background: var(--accent);
    color: var(--bg);
    border: none;
    border-radius: 8px;
    font-family: var(--font-display);
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
    transition: all 0.25s;
    letter-spacing: 0.02em;
  }

  .btn-primary:hover {
    background: #f0b83c;
    transform: translateY(-2px);
    box-shadow: 0 8px 32px rgba(232, 168, 44, 0.25);
  }

  .btn-secondary {
    padding: 14px 32px;
    background: transparent;
    color: var(--text-primary);
    border: 1px solid var(--border);
    border-radius: 8px;
    font-family: var(--font-display);
    font-size: 15px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.25s;
  }

  .btn-secondary:hover {
    border-color: var(--text-muted);
    background: var(--bg-elevated);
  }

  /* ── Workflow Visual ── */
  .workflow-visual {
    margin-top: 80px;
    width: 100%;
    max-width: 900px;
    animation: fadeUp 1s ease 0.5s forwards;
    opacity: 0;
    position: relative;
  }

  .workflow-canvas {
    width: 100%;
    height: 260px;
    position: relative;
  }

  .wf-node {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    z-index: 2;
  }

  .wf-node-circle {
    width: 56px;
    height: 56px;
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22px;
    border: 2px solid;
    transition: transform 0.3s, box-shadow 0.3s;
  }

  .wf-node:hover .wf-node-circle {
    transform: scale(1.1);
  }

  .wf-node-label {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-secondary);
    letter-spacing: 0.04em;
    white-space: nowrap;
  }

  .wf-connector {
    position: absolute;
    z-index: 1;
  }

  .wf-connector svg {
    overflow: visible;
  }

  .connector-path {
    stroke-dasharray: 6 4;
    animation: dashFlow 1.5s linear infinite;
  }

  .data-particle {
    animation: particleMove 3s linear infinite;
  }

  /* ── Section Common ── */
  section {
    padding: 120px 48px;
    position: relative;
  }

  .section-label {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--accent);
    letter-spacing: 0.15em;
    text-transform: uppercase;
    margin-bottom: 16px;
  }

  .section-title {
    font-family: var(--font-display);
    font-size: clamp(32px, 4vw, 52px);
    font-weight: 800;
    letter-spacing: -0.03em;
    line-height: 1.15;
    margin-bottom: 20px;
  }

  .section-desc {
    font-size: 16px;
    color: var(--text-secondary);
    max-width: 560px;
    line-height: 1.8;
  }

  /* ── Paradigm Shift ── */
  .paradigm {
    max-width: 1200px;
    margin: 0 auto;
  }

  .paradigm-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 24px;
    margin-top: 64px;
  }

  .paradigm-card {
    padding: 40px;
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.3s, transform 0.3s;
  }

  .paradigm-card:hover {
    border-color: var(--text-muted);
    transform: translateY(-4px);
  }

  .paradigm-card.old::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 3px;
    background: linear-gradient(90deg, #555, #333);
  }

  .paradigm-card.new::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 3px;
    background: linear-gradient(90deg, var(--accent), #f0c040);
  }

  .paradigm-card h3 {
    font-family: var(--font-display);
    font-size: 20px;
    font-weight: 700;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .paradigm-card p {
    color: var(--text-secondary);
    font-size: 14px;
    line-height: 1.8;
    margin-bottom: 24px;
  }

  .paradigm-card .tag {
    display: inline-block;
    padding: 4px 12px;
    border-radius: 100px;
    font-family: var(--font-mono);
    font-size: 11px;
    margin-right: 8px;
    margin-bottom: 8px;
  }

  .paradigm-card.old .tag {
    background: rgba(255,255,255,0.05);
    color: var(--text-muted);
    border: 1px solid rgba(255,255,255,0.06);
  }

  .paradigm-card.new .tag {
    background: var(--accent-glow);
    color: var(--accent);
    border: 1px solid rgba(232, 168, 44, 0.15);
  }

  /* ── How It Works ── */
  .how-it-works {
    max-width: 1200px;
    margin: 0 auto;
  }

  .steps {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 24px;
    margin-top: 64px;
  }

  .step-card {
    padding: 40px 32px;
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    position: relative;
    opacity: 0;
    transform: translateY(30px);
    transition: border-color 0.3s, transform 0.3s;
  }

  .step-card.visible {
    animation: fadeUp 0.6s ease forwards;
  }

  .step-card:hover {
    border-color: var(--text-muted);
    transform: translateY(-4px) !important;
  }

  .step-num {
    font-family: var(--font-display);
    font-size: 64px;
    font-weight: 800;
    color: var(--bg-elevated);
    line-height: 1;
    margin-bottom: 20px;
    -webkit-text-stroke: 1px var(--border);
  }

  .step-card h3 {
    font-family: var(--font-display);
    font-size: 20px;
    font-weight: 700;
    margin-bottom: 12px;
  }

  .step-card p {
    color: var(--text-secondary);
    font-size: 14px;
    line-height: 1.8;
  }

  .step-icon {
    width: 44px;
    height: 44px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    margin-bottom: 20px;
  }

  /* ── Live Demo ── */
  .demo-section {
    max-width: 1200px;
    margin: 0 auto;
  }

  .demo-container {
    margin-top: 64px;
    display: grid;
    grid-template-columns: 280px 1fr;
    gap: 24px;
    min-height: 520px;
  }

  .demo-sidebar {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .demo-sidebar h4 {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-muted);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 8px;
  }

  .workflow-item {
    padding: 12px 16px;
    background: var(--bg-elevated);
    border: 1px solid var(--border);
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.2s;
  }

  .workflow-item:hover, .workflow-item.active {
    border-color: var(--accent-dim);
    background: rgba(232, 168, 44, 0.05);
  }

  .workflow-item.active {
    border-color: var(--accent);
  }

  .workflow-item .wf-name {
    font-size: 13px;
    font-weight: 500;
    margin-bottom: 4px;
  }

  .workflow-item .wf-meta {
    font-family: var(--font-mono);
    font-size: 10px;
    color: var(--text-muted);
  }

  .demo-main {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }

  .demo-toolbar {
    padding: 16px 24px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .demo-toolbar-title {
    font-family: var(--font-mono);
    font-size: 13px;
    color: var(--text-secondary);
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .demo-toolbar-title .status-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--node-validate);
    animation: pulse 2s infinite;
  }

  .demo-run-btn {
    padding: 8px 20px;
    background: var(--accent);
    color: var(--bg);
    border: none;
    border-radius: 6px;
    font-family: var(--font-mono);
    font-size: 11px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s;
    letter-spacing: 0.04em;
  }

  .demo-run-btn:hover {
    background: #f0b83c;
    box-shadow: 0 0 20px var(--accent-glow);
  }

  .demo-workspace {
    flex: 1;
    padding: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
  }

  .demo-flow {
    display: flex;
    align-items: center;
    gap: 0;
    position: relative;
  }

  .demo-node {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    padding: 20px 24px;
    background: var(--bg-elevated);
    border: 1px solid var(--border);
    border-radius: 14px;
    min-width: 120px;
    transition: all 0.3s;
    cursor: default;
    opacity: 0;
    transform: scale(0.9);
  }

  .demo-node.active {
    opacity: 1;
    transform: scale(1);
  }

  .demo-node.running {
    border-color: var(--accent);
    box-shadow: 0 0 20px var(--accent-glow);
  }

  .demo-node.done {
    border-color: var(--node-validate);
    box-shadow: 0 0 16px rgba(52, 211, 153, 0.1);
  }

  .demo-node-icon {
    width: 40px;
    height: 40px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
  }

  .demo-node-name {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-secondary);
    text-align: center;
  }

  .demo-node-status {
    font-family: var(--font-mono);
    font-size: 10px;
    padding: 2px 8px;
    border-radius: 4px;
    background: var(--bg);
  }

  .demo-node-status.waiting { color: var(--text-muted); }
  .demo-node-status.running { color: var(--accent); background: var(--accent-glow); }
  .demo-node-status.done { color: var(--node-validate); background: rgba(52,211,153,0.1); }

  .demo-arrow {
    width: 48px;
    height: 2px;
    position: relative;
    opacity: 0;
    transition: opacity 0.3s;
  }

  .demo-arrow.active { opacity: 1; }

  .demo-arrow-line {
    width: 100%;
    height: 2px;
    background: var(--border);
    position: absolute;
    top: 50%;
  }

  .demo-arrow-fill {
    height: 2px;
    background: var(--accent);
    position: absolute;
    top: 50%;
    width: 0;
    transition: width 0.5s ease;
  }

  .demo-arrow.active .demo-arrow-fill { width: 100%; }

  .demo-arrow-head {
    position: absolute;
    right: -4px;
    top: 50%;
    transform: translateY(-50%);
    width: 0;
    height: 0;
    border-left: 6px solid var(--accent);
    border-top: 4px solid transparent;
    border-bottom: 4px solid transparent;
    opacity: 0;
    transition: opacity 0.3s 0.4s;
  }

  .demo-arrow.active .demo-arrow-head { opacity: 1; }

  .demo-log {
    padding: 16px 24px;
    border-top: 1px solid var(--border);
    background: rgba(0,0,0,0.3);
    max-height: 100px;
    overflow-y: auto;
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-muted);
    line-height: 2;
  }

  .demo-log .log-line {
    opacity: 0;
    animation: fadeIn 0.3s ease forwards;
  }

  .demo-log .log-time { color: var(--text-muted); }
  .demo-log .log-action { color: var(--accent); }
  .demo-log .log-success { color: var(--node-validate); }

  /* ── Features ── */
  .features {
    max-width: 1200px;
    margin: 0 auto;
  }

  .features-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
    margin-top: 64px;
  }

  .feature-card {
    padding: 36px;
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    transition: all 0.3s;
    opacity: 0;
    transform: translateY(20px);
  }

  .feature-card.visible {
    animation: fadeUp 0.5s ease forwards;
  }

  .feature-card:hover {
    border-color: var(--text-muted);
    transform: translateY(-4px) !important;
  }

  .feature-icon {
    width: 48px;
    height: 48px;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22px;
    margin-bottom: 20px;
  }

  .feature-card h3 {
    font-family: var(--font-display);
    font-size: 17px;
    font-weight: 700;
    margin-bottom: 10px;
  }

  .feature-card p {
    color: var(--text-secondary);
    font-size: 13px;
    line-height: 1.8;
  }

  /* ── Stats ── */
  .stats {
    max-width: 1200px;
    margin: 0 auto;
    padding: 100px 48px;
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 24px;
    margin-top: 48px;
  }

  .stat-card {
    padding: 36px;
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    text-align: center;
  }

  .stat-num {
    font-family: var(--font-display);
    font-size: 48px;
    font-weight: 800;
    color: var(--accent);
    line-height: 1;
    margin-bottom: 8px;
  }

  .stat-label {
    font-family: var(--font-mono);
    font-size: 12px;
    color: var(--text-muted);
    letter-spacing: 0.06em;
  }

  /* ── CTA ── */
  .cta-section {
    text-align: center;
    padding: 120px 48px;
    position: relative;
  }

  .cta-section::before {
    content: '';
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 600px;
    height: 600px;
    background: var(--accent);
    border-radius: 50%;
    filter: blur(200px);
    opacity: 0.06;
    pointer-events: none;
  }

  .cta-section h2 {
    font-family: var(--font-display);
    font-size: clamp(36px, 4.5vw, 56px);
    font-weight: 800;
    letter-spacing: -0.03em;
    margin-bottom: 20px;
  }

  .cta-section p {
    color: var(--text-secondary);
    font-size: 16px;
    max-width: 480px;
    margin: 0 auto 40px;
    line-height: 1.8;
  }

  /* ── Footer ── */
  footer {
    padding: 40px 48px;
    border-top: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-muted);
  }

  footer a {
    color: var(--text-secondary);
    text-decoration: none;
    transition: color 0.2s;
  }

  footer a:hover { color: var(--accent); }

  .footer-links { display: flex; gap: 24px; }

  /* ── Animations ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(24px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes fadeDown {
    from { opacity: 0; transform: translateY(-12px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  @keyframes dashFlow {
    from { stroke-dashoffset: 0; }
    to { stroke-dashoffset: -20; }
  }

  @keyframes particleMove {
    0% { offset-distance: 0%; opacity: 0; }
    10% { opacity: 1; }
    90% { opacity: 1; }
    100% { offset-distance: 100%; opacity: 0; }
  }

  @keyframes nodeFloat {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-6px); }
  }

  @keyframes scanLine {
    0% { left: -100%; }
    100% { left: 200%; }
  }

  /* ── Responsive ── */
  @media (max-width: 900px) {
    nav { padding: 16px 24px; }
    .nav-links { display: none; }
    section { padding: 80px 24px; }
    .paradigm-grid { grid-template-columns: 1fr; }
    .steps { grid-template-columns: 1fr; }
    .features-grid { grid-template-columns: 1fr; }
    .stats-grid { grid-template-columns: repeat(2, 1fr); }
    .demo-container { grid-template-columns: 1fr; }
    .demo-sidebar { display: none; }
    .demo-flow { flex-wrap: wrap; justify-content: center; gap: 8px; }
    .demo-arrow { display: none; }
    .hero { padding: 100px 24px 60px; }
    footer { flex-direction: column; gap: 16px; text-align: center; }
  }
</style>
</head>
<body>

<!-- Navigation -->
<nav>
  <div class="nav-logo">
    <div class="logo-icon">A</div>
    AI OS Pro
  </div>
  <ul class="nav-links">
    <li><a href="#paradigm">核心理念</a></li>
    <li><a href="#how">工作原理</a></li>
    <li><a href="#demo">在线演示</a></li>
    <li><a href="#features">产品能力</a></li>
  </ul>
  <button class="nav-cta">开始使用</button>
</nav>

<!-- Hero -->
<section class="hero">
  <div class="hero-bg-grid"></div>
  <div class="hero-glow hero-glow-1"></div>
  <div class="hero-glow hero-glow-2"></div>

  <div class="hero-badge">
    <span class="dot"></span>
    v2.0 — 工作流引擎全面升级
  </div>

  <h1>
    从<span class="accent">工具调用</span><br>
    到<span class="accent">系统运行</span>
  </h1>

  <p class="hero-sub">
    AI 工作流操作系统将分散的 AI 能力整合为可执行的自动化流程。<br>
    定义一次，反复运行，构建属于你的 AI 能力体系。
  </p>

  <div class="hero-actions">
    <button class="btn-primary" onclick="document.getElementById('demo').scrollIntoView({behavior:'smooth'})">体验演示</button>
    <button class="btn-secondary" onclick="document.getElementById('how').scrollIntoView({behavior:'smooth'})">了解原理</button>
  </div>

  <!-- Workflow Visual -->
  <div class="workflow-visual">
    <div class="workflow-canvas">
      <svg width="100%" height="260" viewBox="0 0 900 260" fill="none" xmlns="http://www.w3.org/2000/svg">
        <!-- Connection lines -->
        <path d="M130 130 C200 130, 220 80, 290 80" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>
        <path d="M130 130 C200 130, 220 180, 290 180" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>
        <path d="M346 80 C400 80, 420 130, 470 130" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>
        <path d="M346 180 C400 180, 420 130, 470 130" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>
        <path d="M526 130 C580 130, 600 130, 650 130" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>
        <path d="M706 130 C750 130, 770 130, 800 130" stroke="#2a2a30" stroke-width="2" stroke-dasharray="6 4" class="connector-path"/>

        <!-- Animated particles -->
        <circle r="3" fill="#e8a82c" opacity="0.8">
          <animateMotion dur="3s" repeatCount="indefinite" path="M130 130 C200 130, 220 80, 290 80"/>
        </circle>
        <circle r="3" fill="#4a9eff" opacity="0.8">
          <animateMotion dur="3s" repeatCount="indefinite" begin="0.8s" path="M346 80 C400 80, 420 130, 470 130"/>
        </circle>
        <circle r="3" fill="#34d399" opacity="0.8">
          <animateMotion dur="2.5s" repeatCount="indefinite" begin="1.6s" path="M526 130 C580 130, 600 130, 650 130"/>
        </circle>
        <circle r="3" fill="#f472b6" opacity="0.8">
          <animateMotion dur="2s" repeatCount="indefinite" begin="2.4s" path="M706 130 C750 130, 770 130, 800 130"/>
        </circle>
      </svg>

      <!-- Nodes -->
      <div class="wf-node" style="left: 74px; top: 102px; animation: nodeFloat 3s ease infinite;">
        <div class="wf-node-circle" style="background: rgba(232,168,44,0.1); border-color: var(--node-gen);">📥</div>
        <span class="wf-node-label">输入</span>
      </div>
      <div class="wf-node" style="left: 262px; top: 52px; animation: nodeFloat 3s ease 0.3s infinite;">
        <div class="wf-node-circle" style="background: rgba(74,158,255,0.1); border-color: var(--node-process);">⚙️</div>
        <span class="wf-node-label">生成</span>
      </div>
      <div class="wf-node" style="left: 262px; top: 152px; animation: nodeFloat 3s ease 0.6s infinite;">
        <div class="wf-node-circle" style="background: rgba(74,158,255,0.1); border-color: var(--node-process);">🔄</div>
        <span class="wf-node-label">改写</span>
      </div>
      <div class="wf-node" style="left: 442px; top: 102px; animation: nodeFloat 3s ease 0.9s infinite;">
        <div class="wf-node-circle" style="background: rgba(52,211,153,0.1); border-color: var(--node-validate);">🔍</div>
        <span class="wf-node-label">分析</span>
      </div>
      <div class="wf-node" style="left: 622px; top: 102px; animation: nodeFloat 3s ease 1.2s infinite;">
        <div class="wf-node-circle" style="background: rgba(244,114,182,0.1); border-color: var(--node-output);">✅</div>
        <span class="wf-node-label">验证</span>
      </div>
      <div class="wf-node" style="left: 772px; top: 102px; animation: nodeFloat 3s ease 1.5s infinite;">
        <div class="wf-node-circle" style="background: rgba(232,168,44,0.1); border-color: var(--node-gen);">📤</div>
        <span class="wf-node-label">输出</span>
      </div>
    </div>
  </div>
</section>

<!-- Paradigm Shift -->
<section id="paradigm" class="paradigm">
  <div class="section-label">核心理念</div>
  <div class="section-title">从"对话式 AI"到"流程化 AI"</div>
  <div class="section-desc">传统 AI 工具只能做单轮问答，AI OS Pro 让 AI 成为可以持续工作的自动化执行系统。</div>

  <div class="paradigm-grid">
    <div class="paradigm-card old">
      <h3>🕘 传统 AI 工具</h3>
      <p>每次使用都需要手动输入 Prompt，获取结果后手动复制到下一步。无法串联多步操作，重复劳动多，效率受限于人工操作速度。</p>
      <span class="tag">单轮对话</span>
      <span class="tag">手动传递</span>
      <span class="tag">无状态</span>
      <span class="tag">不可复用</span>
    </div>
    <div class="paradigm-card new">
      <h3>⚡ AI OS Pro</h3>
      <p>将复杂任务定义为结构化工作流，数据在 AI 节点之间自动流转。一次定义，持续运行，支持条件分支、并行执行和错误重试。</p>
      <span class="tag">多步编排</span>
      <span class="tag">自动流转</span>
      <span class="tag">有状态</span>
      <span class="tag">可复用模板</span>
    </div>
  </div>
</section>

<!-- How It Works -->
<section id="how" class="how-it-works">
  <div class="section-label">工作原理</div>
  <div class="section-title">三步构建自动化 AI 流程</div>
  <div class="section-desc">无需编写代码，通过可视化编排将 AI 能力组合为端到端的自动化工作流。</div>

  <div class="steps">
    <div class="step-card" data-animate>
      <div class="step-num">01</div>
      <div class="step-icon" style="background: rgba(232,168,44,0.1);">🧩</div>
      <h3>定义节点</h3>
      <p>从节点库中选择功能模块——文本生成、数据分析、格式转换、API 调用等，每个节点承担一个原子化的 AI 操作。</p>
    </div>
    <div class="step-card" data-animate>
      <div class="step-num">02</div>
      <div class="step-icon" style="background: rgba(74,158,255,0.1);">🔗</div>
      <h3>编排流程</h3>
      <p>将节点连接为有向工作流，定义数据传递规则和执行条件。支持串行、并行、条件分支和循环等多种编排模式。</p>
    </div>
    <div class="step-card" data-animate>
      <div class="step-num">03</div>
      <div class="step-icon" style="background: rgba(52,211,153,0.1);">🚀</div>
      <h3>一键运行</h3>
      <p>输入初始数据，系统自动按流程依次执行各节点。前一步的输出自动传递给下一步，直至生成最终结果。</p>
    </div>
  </div>
</section>

<!-- Live Demo -->
<section id="demo" class="demo-section">
  <div class="section-label">在线演示</div>
  <div class="section-title">实时体验工作流引擎</div>
  <div class="section-desc">选择一个工作流模板，点击运行，观察数据如何在节点之间自动流转。</div>

  <div class="demo-container">
    <div class="demo-sidebar">
      <h4>工作流模板</h4>
      <div class="workflow-item active" onclick="selectWorkflow(this, 0)">
        <div class="wf-name">📝 内容生成流水线</div>
        <div class="wf-meta">4 节点 · 约 12s</div>
      </div>
      <div class="workflow-item" onclick="selectWorkflow(this, 1)">
        <div class="wf-name">🔍 代码审查 Agent</div>
        <div class="wf-meta">5 节点 · 约 18s</div>
      </div>
      <div class="workflow-item" onclick="selectWorkflow(this, 2)">
        <div class="wf-name">📊 数据分析报告</div>
        <div class="wf-meta">3 节点 · 约 8s</div>
      </div>
      <div class="workflow-item" onclick="selectWorkflow(this, 3)">
        <div class="wf-name">🌐 多语言翻译</div>
        <div class="wf-meta">4 节点 · 约 10s</div>
      </div>
    </div>

    <div class="demo-main">
      <div class="demo-toolbar">
        <div class="demo-toolbar-title">
          <span class="status-dot"></span>
          <span id="demo-title">内容生成流水线</span>
        </div>
        <button class="demo-run-btn" id="runBtn" onclick="runDemo()">▶ 运行</button>
      </div>

      <div class="demo-workspace">
        <div class="demo-flow" id="demoFlow">
          <!-- Nodes injected by JS -->
        </div>
      </div>

      <div class="demo-log" id="demoLog">
        <div class="log-line"><span class="log-time">[就绪]</span> 选择工作流模板并点击运行</div>
      </div>
    </div>
  </div>
</section>

<!-- Features -->
<section id="features" class="features">
  <div class="section-label">产品能力</div>
  <div class="section-title">为复杂任务而生的引擎</div>
  <div class="section-desc">AI OS Pro 不只是 Prompt 模板，而是一套完整的任务执行基础设施。</div>

  <div class="features-grid">
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(232,168,44,0.1);">🔀</div>
      <h3>条件分支与路由</h3>
      <p>根据中间结果动态决定下一步执行路径，支持 IF/ELSE 逻辑和加权随机路由。</p>
    </div>
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(74,158,255,0.1);">⚡</div>
      <h3>并行执行</h3>
      <p>无依赖关系的节点自动并行运行，充分利用 Token 配额，大幅缩短端到端执行时间。</p>
    </div>
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(52,211,153,0.1);">🛡️</div>
      <h3>断点重试与回退</h3>
      <p>节点执行失败时自动重试，支持配置最大重试次数和回退策略，确保流程鲁棒性。</p>
    </div>
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(244,114,182,0.1);">📦</div>
      <h3>模板市场</h3>
      <p>从社区共享的工作流模板中一键导入，快速复用成熟方案，也支持发布自己的流程。</p>
    </div>
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(168,85,247,0.1);">📊</div>
      <h3>执行监控面板</h3>
      <p>实时查看每个节点的执行状态、Token 消耗和耗时，支持历史回溯和性能分析。</p>
    </div>
    <div class="feature-card" data-animate>
      <div class="feature-icon" style="background: rgba(251,191,36,0.1);">🔌</div>
      <h3>外部 API 接入</h3>
      <p>通过 HTTP 节点接入任意外部服务——数据库、搜索引擎、第三方 SaaS，扩展无限可能。</p>
    </div>
  </div>
</section>

<!-- Stats -->
<div class="stats">
  <div class="section-label">运行数据</div>
  <div class="section-title">被验证的生产力</div>
  <div class="stats-grid">
    <div class="stat-card">
      <div class="stat-num" data-count="80">0%</div>
      <div class="stat-label">效率提升</div>
    </div>
    <div class="stat-card">
      <div class="stat-num" data-count="500">0万</div>
      <div class="stat-label">日均 Token 消耗</div>
    </div>
    <div class="stat-card">
      <div class="stat-num" data-count="20">0人</div>
      <div class="stat-label">团队落地规模</div>
    </div>
    <div class="stat-card">
      <div class="stat-num" data-count="90">0%</div>
      <div class="stat-label">规范覆盖率</div>
    </div>
  </div>
</div>

<!-- CTA -->
<section class="cta-section">
  <h2>让 AI 不再是<span style="color:var(--accent);">单点工具</span></h2>
  <p>构建属于你的自动化 AI 能力体系，从今天开始。</p>
  <button class="btn-primary" style="padding: 16px 40px; font-size: 16px;">免费开始使用</button>
</section>

<!-- Footer -->
<footer>
  <div>&copy; 2026 AI OS Pro. All rights reserved.</div>
  <div class="footer-links">
    <a href="#">文档</a>
    <a href="#">GitHub</a>
    <a href="#">社区</a>
    <a href="#">隐私政策</a>
  </div>
</footer>

<script>
// ── Workflow Definitions ──
const workflows = [
  {
    name: '内容生成流水线',
    nodes: [
      { icon: '📥', label: '接收主题', color: '#e8a82c', status: '接收用户输入的文章主题和要求' },
      { icon: '⚙️', label: '大纲生成', color: '#4a9eff', status: '调用 LLM 生成结构化文章大纲' },
      { icon: '✏️', label: '内容撰写', color: '#4a9eff', status: '根据大纲逐章节生成正文内容' },
      { icon: '🔍', label: '质量审核', color: '#34d399', status: '检查逻辑一致性、事实准确性和语言质量' },
      { icon: '📤', label: '输出发布', color: '#f472b6', status: '格式化输出并生成发布就绪的文档' },
    ]
  },
  {
    name: '代码审查 Agent',
    nodes: [
      { icon: '📥', label: '拉取代码', color: '#e8a82c', status: '从 Git 仓库获取最新提交的代码变更' },
      { icon: '🔎', label: '静态分析', color: '#4a9eff', status: '运行 AST 分析，识别代码异味和技术债' },
      { icon: '🧠', label: '语义审查', color: '#4a9eff', status: 'LLM 深度理解代码逻辑，发现潜在 Bug' },
      { icon: '📋', label: '生成报告', color: '#34d399', status: '汇总问题清单并生成修复建议 PR' },
      { icon: '✅', label: '测试验证', color: '#f472b6', status: '自动运行单元测试，验证修复正确性' },
    ]
  },
  {
    name: '数据分析报告',
    nodes: [
      { icon: '📥', label: '数据采集', color: '#e8a82c', status: '从多个数据源拉取原始数据集' },
      { icon: '⚙️', label: '清洗处理', color: '#4a9eff', status: '去重、补缺、格式标准化处理' },
      { icon: '📊', label: '洞察生成', color: '#34d399', status: 'LLM 分析数据趋势，生成可视化报告' },
    ]
  },
  {
    name: '多语言翻译',
    nodes: [
      { icon: '📥', label: '接收原文', color: '#e8a82c', status: '接收待翻译的源语言文本' },
      { icon: '🌐', label: '初翻生成', color: '#4a9eff', status: 'LLM 生成多语言初版翻译' },
      { icon: '🔄', label: '语义校准', color: '#4a9eff', status: '回译对比，修正语义偏差和文化适配' },
      { icon: '📤', label: '输出交付', color: '#f472b6', status: '生成最终多语言版本并格式化输出' },
    ]
  }
];

let currentWorkflow = 0;
let isRunning = false;

function selectWorkflow(el, index) {
  if (isRunning) return;
  document.querySelectorAll('.workflow-item').forEach(i => i.classList.remove('active'));
  el.classList.add('active');
  currentWorkflow = index;
  document.getElementById('demo-title').textContent = workflows[index].name;
  renderNodes(index);
  clearLog();
  addLog('就绪', '已加载工作流模板: ' + workflows[index].name);
}

function renderNodes(wfIndex) {
  const flow = document.getElementById('demoFlow');
  const wf = workflows[wfIndex];
  flow.innerHTML = '';

  wf.nodes.forEach((node, i) => {
    if (i > 0) {
      const arrow = document.createElement('div');
      arrow.className = 'demo-arrow';
      arrow.id = 'arrow-' + i;
      arrow.innerHTML = `
        <div class="demo-arrow-line"></div>
        <div class="demo-arrow-fill"></div>
        <div class="demo-arrow-head"></div>
      `;
      flow.appendChild(arrow);
    }

    const el = document.createElement('div');
    el.className = 'demo-node active';
    el.id = 'dnode-' + i;
    el.innerHTML = `
      <div class="demo-node-icon" style="background: ${node.color}15; border: 1px solid ${node.color}30; border-radius: 10px;">${node.icon}</div>
      <div class="demo-node-name">${node.label}</div>
      <div class="demo-node-status waiting" id="nstatus-${i}">等待中</div>
    `;
    flow.appendChild(el);
  });
}

function addLog(type, msg) {
  const log = document.getElementById('demoLog');
  const now = new Date();
  const time = now.toTimeString().slice(0, 8);
  const cls = type === '完成' ? 'log-success' : type === '执行' ? 'log-action' : '';
  const line = document.createElement('div');
  line.className = 'log-line';
  line.innerHTML = `<span class="log-time">[${time}]</span> <span class="${cls}">${msg}</span>`;
  log.appendChild(line);
  log.scrollTop = log.scrollHeight;
}

function clearLog() {
  document.getElementById('demoLog').innerHTML = '';
}

async function runDemo() {
  if (isRunning) return;
  isRunning = true;
  const btn = document.getElementById('runBtn');
  btn.textContent = '⏳ 运行中...';
  btn.style.opacity = '0.6';

  renderNodes(currentWorkflow);
  clearLog();

  const wf = workflows[currentWorkflow];
  addLog('执行', `开始执行工作流: ${wf.name}`);

  for (let i = 0; i < wf.nodes.length; i++) {
    const node = wf.nodes[i];

    // Activate arrow
    if (i > 0) {
      const arrow = document.getElementById('arrow-' + i);
      if (arrow) arrow.classList.add('active');
    }

    // Set running
    const nodeEl = document.getElementById('dnode-' + i);
    const statusEl = document.getElementById('nstatus-' + i);
    nodeEl.classList.add('running');
    statusEl.className = 'demo-node-status running';
    statusEl.textContent = '执行中...';

    addLog('执行', `[${node.label}] ${node.status}`);

    await sleep(1200 + Math.random() * 800);

    // Set done
    nodeEl.classList.remove('running');
    nodeEl.classList.add('done');
    statusEl.className = 'demo-node-status done';
    statusEl.textContent = '✓ 完成';

    addLog('完成', `[${node.label}] 执行完成`);
  }

  addLog('完成', `✅ 工作流执行完毕 — ${wf.nodes.length} 个节点全部成功`);

  btn.textContent = '▶ 运行';
  btn.style.opacity = '1';
  isRunning = false;
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// ── Scroll Animations ──
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry, index) => {
    if (entry.isIntersecting) {
      const delay = entry.target.dataset.delay || 0;
      setTimeout(() => {
        entry.target.classList.add('visible');
      }, delay);
      observer.unobserve(entry.target);
    }
  });
}, { threshold: 0.15 });

document.querySelectorAll('[data-animate]').forEach((el, i) => {
  el.dataset.delay = i * 100;
  observer.observe(el);
});

// ── Stats Counter ──
const statObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const nums = entry.target.querySelectorAll('[data-count]');
      nums.forEach(num => {
        const target = parseInt(num.dataset.count);
        const suffix = num.textContent.replace(/[0-9]/g, '');
        let current = 0;
        const step = target / 40;
        const interval = setInterval(() => {
          current += step;
          if (current >= target) {
            current = target;
            clearInterval(interval);
          }
          num.textContent = Math.floor(current) + suffix;
        }, 30);
      });
      statObserver.unobserve(entry.target);
    }
  });
}, { threshold: 0.3 });

document.querySelectorAll('.stats-grid').forEach(el => statObserver.observe(el));

// ── Init ──
renderNodes(0);
addLog('就绪', '已加载工作流模板: 内容生成流水线');
</script>
</body>
</html>
