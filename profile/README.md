<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Brooked — Two-Way SQL Sync for Google Sheets</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --green: #10a37f;
    --green-dim: rgba(16,163,127,0.12);
    --green-mid: rgba(16,163,127,0.3);
    --bg: #0d0d0d;
    --bg-2: #141414;
    --bg-3: #1a1a1a;
    --bg-card: #161616;
    --border: rgba(255,255,255,0.08);
    --border-hover: rgba(255,255,255,0.15);
    --text: #f0f0ee;
    --text-muted: #888884;
    --text-dim: #555550;
    --mono: 'DM Mono', monospace;
    --sans: 'DM Sans', sans-serif;
  }

  html { scroll-behavior: smooth; }

  body {
    font-family: var(--sans);
    background: var(--bg);
    color: var(--text);
    line-height: 1.7;
    font-size: 15px;
    -webkit-font-smoothing: antialiased;
  }

  a { color: var(--green); text-decoration: none; }
  a:hover { text-decoration: underline; }

  /* ── Layout ── */
  .container {
    max-width: 780px;
    margin: 0 auto;
    padding: 0 2rem;
  }

  /* ── Header ── */
  header {
    border-bottom: 1px solid var(--border);
    padding: 1.2rem 0;
    position: sticky;
    top: 0;
    background: rgba(13,13,13,0.92);
    backdrop-filter: blur(12px);
    z-index: 100;
  }

  .header-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    max-width: 780px;
    margin: 0 auto;
    padding: 0 2rem;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 10px;
    font-weight: 500;
    font-size: 15px;
    color: var(--text);
  }

  .logo-mark {
    width: 28px;
    height: 28px;
    background: var(--green);
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 13px;
    font-weight: 500;
    color: #fff;
    font-family: var(--mono);
  }

  .header-links {
    display: flex;
    align-items: center;
    gap: 1.5rem;
  }

  .header-links a {
    color: var(--text-muted);
    font-size: 14px;
  }

  .header-links a:hover { color: var(--text); text-decoration: none; }

  .btn-primary {
    background: var(--green);
    color: #fff !important;
    padding: 6px 14px;
    border-radius: 6px;
    font-size: 13px;
    font-weight: 500;
    white-space: nowrap;
  }

  .btn-primary:hover { opacity: 0.9; text-decoration: none !important; }

  /* ── Hero ── */
  .hero {
    padding: 5rem 0 4rem;
    text-align: center;
    position: relative;
  }

  .hero::before {
    content: '';
    position: absolute;
    top: 0; left: 50%; transform: translateX(-50%);
    width: 600px; height: 300px;
    background: radial-gradient(ellipse at center top, rgba(16,163,127,0.1) 0%, transparent 70%);
    pointer-events: none;
  }

  .hero-badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: var(--green-dim);
    border: 1px solid var(--green-mid);
    color: var(--green);
    font-size: 12px;
    font-family: var(--mono);
    padding: 4px 12px;
    border-radius: 20px;
    margin-bottom: 1.5rem;
    letter-spacing: 0.02em;
  }

  .hero h1 {
    font-size: clamp(2rem, 5vw, 3rem);
    font-weight: 300;
    line-height: 1.2;
    letter-spacing: -0.03em;
    margin-bottom: 1rem;
    color: var(--text);
  }

  .hero h1 em {
    font-style: normal;
    color: var(--green);
  }

  .hero-sub {
    font-size: 16px;
    color: var(--text-muted);
    max-width: 520px;
    margin: 0 auto 2rem;
    font-weight: 300;
  }

  .hero-cta {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
    margin-bottom: 3rem;
  }

  .btn-outline {
    border: 1px solid var(--border-hover);
    color: var(--text-muted);
    padding: 8px 18px;
    border-radius: 6px;
    font-size: 14px;
    font-weight: 400;
    transition: border-color 0.15s, color 0.15s;
  }

  .btn-outline:hover { border-color: var(--text-muted); color: var(--text); text-decoration: none; }

  .btn-large {
    background: var(--green);
    color: #fff !important;
    padding: 10px 22px;
    border-radius: 6px;
    font-size: 14px;
    font-weight: 500;
    transition: opacity 0.15s;
  }

  .btn-large:hover { opacity: 0.88; text-decoration: none !important; }

  .integrations-strip {
    font-size: 12px;
    color: var(--text-dim);
    font-family: var(--mono);
    letter-spacing: 0.03em;
    margin-bottom: 0.5rem;
  }

  .integrations-pills {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 6px;
    max-width: 580px;
    margin: 0 auto;
  }

  .pill {
    background: var(--bg-3);
    border: 1px solid var(--border);
    color: var(--text-muted);
    font-size: 11px;
    font-family: var(--mono);
    padding: 3px 10px;
    border-radius: 4px;
  }

  /* ── Rating bar ── */
  .rating-bar {
    background: var(--bg-2);
    border-top: 1px solid var(--border);
    border-bottom: 1px solid var(--border);
    padding: 1rem 0;
    margin: 0 0 3rem;
  }

  .rating-inner {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 2rem;
    flex-wrap: wrap;
    max-width: 780px;
    margin: 0 auto;
    padding: 0 2rem;
    font-size: 13px;
  }

  .rating-item {
    display: flex;
    align-items: center;
    gap: 6px;
    color: var(--text-muted);
  }

  .stars { color: #f5c542; letter-spacing: 1px; }

  .sep {
    width: 1px;
    height: 18px;
    background: var(--border);
  }

  /* ── Section ── */
  section { padding: 3rem 0; }

  .section-label {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--green);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 0.75rem;
  }

  h2 {
    font-size: 1.6rem;
    font-weight: 400;
    letter-spacing: -0.02em;
    margin-bottom: 0.5rem;
  }

  .section-desc {
    color: var(--text-muted);
    max-width: 520px;
    margin-bottom: 2rem;
    font-weight: 300;
  }

  hr.divider {
    border: none;
    border-top: 1px solid var(--border);
    margin: 2rem 0;
  }

  /* ── Feature grid ── */
  .feature-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 1px;
    border: 1px solid var(--border);
    border-radius: 10px;
    overflow: hidden;
    background: var(--border);
    margin-bottom: 2rem;
  }

  .feature-card {
    background: var(--bg-card);
    padding: 1.5rem;
    transition: background 0.2s;
  }

  .feature-card:hover { background: var(--bg-3); }

  .feature-icon {
    width: 32px;
    height: 32px;
    background: var(--green-dim);
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 0.75rem;
  }

  .feature-icon svg { stroke: var(--green); width: 16px; height: 16px; }

  .feature-card h3 {
    font-size: 14px;
    font-weight: 500;
    margin-bottom: 0.35rem;
    color: var(--text);
  }

  .feature-card p {
    font-size: 13px;
    color: var(--text-muted);
    line-height: 1.6;
    font-weight: 300;
  }

  /* ── Integration table ── */
  .integration-section {
    margin-bottom: 1.5rem;
  }

  .integration-section h3 {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-bottom: 0.75rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid var(--border);
  }

  .integration-list {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }

  .integration-tag {
    background: var(--bg-2);
    border: 1px solid var(--border);
    border-radius: 5px;
    padding: 5px 12px;
    font-size: 13px;
    color: var(--text-muted);
    transition: border-color 0.15s, color 0.15s;
    cursor: default;
  }

  .integration-tag:hover {
    border-color: var(--green-mid);
    color: var(--text);
  }

  /* ── Testimonial ── */
  .testimonial-block {
    background: var(--bg-2);
    border: 1px solid var(--border);
    border-left: 3px solid var(--green);
    border-radius: 8px;
    padding: 1.5rem 1.75rem;
    margin: 2rem 0;
  }

  .testimonial-block blockquote {
    font-size: 15px;
    font-weight: 300;
    color: var(--text);
    font-style: italic;
    margin-bottom: 0.75rem;
    line-height: 1.7;
  }

  .testimonial-meta {
    font-size: 12px;
    color: var(--text-dim);
    font-family: var(--mono);
  }

  /* ── Reviews ── */
  .review-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 12px;
    margin: 1.5rem 0;
  }

  .review-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 1.25rem;
  }

  .review-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 0.75rem;
  }

  .reviewer {
    font-size: 13px;
    font-weight: 500;
    color: var(--text);
  }

  .review-date {
    font-size: 11px;
    color: var(--text-dim);
    font-family: var(--mono);
  }

  .review-card p {
    font-size: 13px;
    color: var(--text-muted);
    font-weight: 300;
    line-height: 1.6;
    font-style: italic;
  }

  .review-title {
    font-size: 12px;
    font-weight: 500;
    color: var(--text);
    margin-bottom: 0.4rem;
  }

  /* ── Steps ── */
  .steps {
    display: flex;
    flex-direction: column;
    gap: 0;
    position: relative;
  }

  .step {
    display: flex;
    gap: 1.5rem;
    padding: 1.5rem 0;
    position: relative;
  }

  .step:not(:last-child)::after {
    content: '';
    position: absolute;
    left: 19px;
    top: 3.5rem;
    bottom: -0.5rem;
    width: 1px;
    background: var(--border);
  }

  .step-num {
    width: 38px;
    height: 38px;
    min-width: 38px;
    border: 1px solid var(--border);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 13px;
    font-family: var(--mono);
    color: var(--text-dim);
    background: var(--bg-card);
  }

  .step-content h3 {
    font-size: 15px;
    font-weight: 500;
    margin-bottom: 0.3rem;
    color: var(--text);
  }

  .step-content p {
    font-size: 13px;
    color: var(--text-muted);
    font-weight: 300;
  }

  /* ── Who uses ── */
  .persona-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 12px;
    margin: 1.5rem 0;
  }

  .persona-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 1.25rem;
  }

  .persona-title {
    font-size: 13px;
    font-weight: 500;
    color: var(--text);
    margin-bottom: 0.4rem;
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .persona-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--green);
  }

  .persona-card p {
    font-size: 12px;
    color: var(--text-muted);
    font-weight: 300;
    line-height: 1.6;
  }

  /* ── CTA ── */
  .cta-block {
    background: var(--bg-2);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 2.5rem;
    text-align: center;
    margin: 3rem 0;
    position: relative;
    overflow: hidden;
  }

  .cta-block::before {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse at center bottom, rgba(16,163,127,0.07) 0%, transparent 60%);
    pointer-events: none;
  }

  .cta-block h2 {
    font-size: 1.4rem;
    font-weight: 400;
    letter-spacing: -0.02em;
    margin-bottom: 0.5rem;
  }

  .cta-block p {
    color: var(--text-muted);
    font-size: 14px;
    margin-bottom: 1.5rem;
    font-weight: 300;
  }

  /* ── Footer ── */
  footer {
    border-top: 1px solid var(--border);
    padding: 2rem 0;
    margin-top: 2rem;
  }

  .footer-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 1rem;
  }

  .footer-links {
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
  }

  .footer-links a {
    font-size: 13px;
    color: var(--text-dim);
  }

  .footer-links a:hover { color: var(--text-muted); text-decoration: none; }

  .footer-copy {
    font-size: 12px;
    color: var(--text-dim);
    font-family: var(--mono);
  }

  /* ── Util ── */
  .mono { font-family: var(--mono); }
  .green { color: var(--green); }

  @media (max-width: 600px) {
    .header-links { gap: 0.75rem; }
    .hero { padding: 3rem 0 2.5rem; }
    .hero h1 { font-size: 1.75rem; }
    .sep { display: none; }
    .rating-inner { gap: 1rem; font-size: 12px; }
  }
</style>
</head>
<body>

<header>
  <div class="header-inner">
    <a href="https://brooked.io" class="logo" style="text-decoration:none">
      <div class="logo-mark">B</div>
      Brooked
    </a>
    <nav class="header-links">
      <a href="#features">Features</a>
      <a href="#integrations">Integrations</a>
      <a href="https://brooked.io/pricing">Pricing</a>
      <a href="https://app.brooked.io" class="btn-primary">Open app →</a>
    </nav>
  </div>
</header>

<!-- ── Hero ── -->
<section class="hero">
  <div class="container">
    <div class="hero-badge">
      <span>●</span> Google Workspace Verified · 5.0 stars · 71 installs
    </div>
    <h1>Two-way SQL sync between<br><em>Google Sheets</em> and your stack</h1>
    <p class="hero-sub">Finance teams and analysts waste hours exporting data manually. Brooked replaces brittle pipelines with live, bidirectional sync — pull data in, push edits back.</p>
    <div class="hero-cta">
      <a href="https://workspace.google.com/marketplace/app/brooked_ai_snowflake_quickbooks_hubspot/887798732409" class="btn-large">Install free — 100 imports/month</a>
      <a href="https://app.brooked.io" class="btn-outline">Open app</a>
    </div>
    <p class="integrations-strip">20+ enterprise sources</p>
    <div class="integrations-pills">
      <span class="pill">Snowflake</span>
      <span class="pill">QuickBooks</span>
      <span class="pill">NetSuite</span>
      <span class="pill">Salesforce</span>
      <span class="pill">HubSpot</span>
      <span class="pill">BigQuery</span>
      <span class="pill">PostgreSQL</span>
      <span class="pill">Supabase</span>
      <span class="pill">Stripe</span>
      <span class="pill">Procore</span>
      <span class="pill">Intacct</span>
      <span class="pill">Databricks</span>
      <span class="pill">Redshift</span>
      <span class="pill">+more</span>
    </div>
  </div>
</section>

<!-- ── Rating bar ── -->
<div class="rating-bar">
  <div class="rating-inner">
    <span class="rating-item"><span class="stars">★★★★★</span> 5.0 on Marketplace</span>
    <div class="sep"></div>
    <span class="rating-item">71 finance &amp; ops teams</span>
    <div class="sep"></div>
    <span class="rating-item">No credit card required</span>
    <div class="sep"></div>
    <span class="rating-item mono green">Free tier · 100 imports/month</span>
  </div>
</div>

<!-- ── Features ── -->
<div class="container">
  <section id="features">
    <div class="section-label">Platform</div>
    <h2>Everything your data workflow needs</h2>
    <p class="section-desc">One add-on that replaces manual exports, fragile scripts, and separate BI dashboards.</p>

    <div class="feature-grid">
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M7 16V4m0 0L3 8m4-4 4 4M17 8v12m0 0 4-4m-4 4-4-4"/></svg>
        </div>
        <h3>Two-way sync</h3>
        <p>Pull live data from any source or push Sheets edits back upstream. No manual exports. Keep Sheets as your single source of truth.</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"/><path d="M12 2v2m0 16v2M4.93 4.93l1.41 1.41m11.32 11.32 1.41 1.41M2 12h2m16 0h2M4.93 19.07l1.41-1.41M17.66 6.34l1.41-1.41"/></svg>
        </div>
        <h3>AI Agent</h3>
        <p>Query your data in plain English, run Python analysis inline, and build automations without writing code. Claude, GPT, and Gemini supported.</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>
        </div>
        <h3>Auto-refresh</h3>
        <p>Schedule syncs every 15 minutes, hourly, or daily. Your sheet is always current — no one needs to remember to refresh.</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"/><path d="M13.73 21a2 2 0 0 1-3.46 0"/></svg>
        </div>
        <h3>Alerts</h3>
        <p>Set thresholds on any metric. Get Slack and email alerts the moment anomalies are detected — no dashboard to babysit.</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 11 12 14 22 4"/><path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"/></svg>
        </div>
        <h3>Data validation</h3>
        <p>Automatic schema checks and drift detection keep reports clean and audit-ready. Every write is logged with a full diff.</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
        </div>
        <h3>Python sandbox</h3>
        <p>Run pandas, numpy, and custom analysis directly inside your AI prompts. Results write back to the cell — no Colab needed.</p>
      </div>
    </div>
  </section>

  <hr class="divider">

  <!-- ── Integrations ── -->
  <section id="integrations">
    <div class="section-label">Integrations</div>
    <h2>Connected to your entire stack</h2>
    <p class="section-desc">Every integration ships with two-way sync, AI queries, and scheduled refresh out of the box.</p>

    <div class="integration-section">
      <h3>Data warehouses</h3>
      <div class="integration-list">
        <span class="integration-tag">Snowflake</span>
        <span class="integration-tag">BigQuery</span>
        <span class="integration-tag">PostgreSQL</span>
        <span class="integration-tag">Supabase</span>
        <span class="integration-tag">Amazon Redshift</span>
        <span class="integration-tag">Databricks</span>
        <span class="integration-tag">MySQL</span>
        <span class="integration-tag">SQL Server</span>
      </div>
    </div>

    <div class="integration-section">
      <h3>Finance &amp; ERP</h3>
      <div class="integration-list">
        <span class="integration-tag">QuickBooks</span>
        <span class="integration-tag">NetSuite</span>
        <span class="integration-tag">Sage Intacct</span>
        <span class="integration-tag">Stripe</span>
        <span class="integration-tag">Procore</span>
      </div>
    </div>

    <div class="integration-section">
      <h3>CRM &amp; Analytics</h3>
      <div class="integration-list">
        <span class="integration-tag">Salesforce</span>
        <span class="integration-tag">HubSpot</span>
        <span class="integration-tag">Google Analytics</span>
        <span class="integration-tag">Looker</span>
      </div>
    </div>

    <p style="font-size:13px;color:var(--text-dim);margin-top:1rem;">
      <a href="https://brooked.io/integrations" style="color:var(--green)">Browse all integrations →</a> &nbsp;·&nbsp; 13 live connectors and growing
    </p>
  </section>

  <hr class="divider">

  <!-- ── Setup steps ── -->
  <section id="get-started">
    <div class="section-label">Setup</div>
    <h2>Live in minutes, not days</h2>
    <p class="section-desc">One install, one OAuth, and you're querying live data. No middleware, no IT ticket, no separate dashboard.</p>

    <div class="steps">
      <div class="step">
        <div class="step-num">01</div>
        <div class="step-content">
          <h3>Install from the Marketplace</h3>
          <p>Add Brooked to Google Sheets from the Workspace Marketplace in one click. No Chrome extension, no IT ticket. Free tier included, no credit card required.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">02</div>
        <div class="step-content">
          <h3>Connect your sources</h3>
          <p>Authenticate via OAuth, API key, or key-pair auth depending on the source. Read-only by default — admins control write permissions per connector.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">03</div>
        <div class="step-content">
          <h3>Sync and query</h3>
          <p>Pull rows on a schedule or ask the AI agent in plain English. Brooked writes the SQL, runs Python, and writes results back to the cell automatically.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">04</div>
        <div class="step-content">
          <h3>Set alerts and share</h3>
          <p>Configure threshold alerts for any metric. Share live sheets with stakeholders — they always see current data without touching the pipeline.</p>
        </div>
      </div>
    </div>
  </section>

  <hr class="divider">

  <!-- ── Who uses ── -->
  <section id="who">
    <div class="section-label">Customers</div>
    <h2>Built for the teams keeping your business running</h2>

    <div class="persona-grid">
      <div class="persona-card">
        <div class="persona-title"><span class="persona-dot"></span>Finance teams</div>
        <p>Ditch the weekly CSV export. Pull live P&amp;L, AR, and cash flow from QuickBooks or NetSuite directly into your reporting sheet.</p>
      </div>
      <div class="persona-card">
        <div class="persona-title"><span class="persona-dot"></span>Revenue analysts</div>
        <p>Live CRM data in Sheets without bugging an engineer. Query HubSpot and Salesforce pipeline in plain English, push updates back.</p>
      </div>
      <div class="persona-card">
        <div class="persona-title"><span class="persona-dot"></span>Data engineers</div>
        <p>Give stakeholders self-serve access to Snowflake or Databricks without building custom pipelines or granting direct warehouse access.</p>
      </div>
      <div class="persona-card">
        <div class="persona-title"><span class="persona-dot"></span>Operations teams</div>
        <p>Sync Procore project data, automate alerts for budget overruns, and keep the whole org aligned on one live spreadsheet.</p>
      </div>
    </div>
  </section>

  <hr class="divider">

  <!-- ── Testimonials ── -->
  <section id="reviews">
    <div class="section-label">Reviews</div>
    <h2>What teams are saying</h2>

    <div class="review-grid">
      <div class="review-card">
        <div class="review-header">
          <span class="reviewer">Nathan Yapi</span>
          <span class="review-date">27 Apr 2026</span>
        </div>
        <div class="stars" style="font-size:12px;margin-bottom:0.5rem;">★★★★★</div>
        <p class="review-title">No more weekly CSV dumps</p>
        <p>"I was tired of bugging our data engineers every week for a CSV dump from Snowflake. Now the team just hits refresh and has the latest warehouse data instantly. If you're tired of the manual export-import dance, this is a no-brainer."</p>
      </div>
      <div class="review-card">
        <div class="review-header">
          <span class="reviewer">Derek Nguyen</span>
          <span class="review-date">1 May 2026</span>
        </div>
        <div class="stars" style="font-size:12px;margin-bottom:0.5rem;">★★★★★</div>
        <p class="review-title">Two-way sync, not just one-way</p>
        <p>"What really stands out is the ability to push updates back to the source, not just pull data like most tools. The AI chat is surprisingly useful — I can ask questions about my data without writing queries."</p>
      </div>
    </div>

    <div class="testimonial-block">
      <blockquote>"Using Brooked has been like unlocking a secret data superpower — the perfect fusion of simplicity and versatility."</blockquote>
      <div class="testimonial-meta">— Google Workspace Marketplace review</div>
    </div>

    <p style="font-size:13px;color:var(--text-dim);margin-top:0.75rem;">
      <a href="https://workspace.google.com/marketplace/app/brooked_ai_snowflake_quickbooks_hubspot/887798732409" style="color:var(--green)">Read all reviews on the Marketplace →</a>
    </p>
  </section>

  <hr class="divider">

  <!-- ── Security ── -->
  <section id="security">
    <div class="section-label">Security</div>
    <h2>Enterprise-grade from day one</h2>
    <p class="section-desc">Your credentials never leave your control. Brooked is Google Workspace Verified and built with security-first defaults.</p>

    <div class="feature-grid" style="grid-template-columns:repeat(auto-fit,minmax(180px,1fr))">
      <div class="feature-card">
        <h3 style="font-family:var(--mono);font-size:12px;color:var(--text-dim);margin-bottom:0.25rem">Auth</h3>
        <p>OAuth, API key, and key-pair auth. Read-only by default.</p>
      </div>
      <div class="feature-card">
        <h3 style="font-family:var(--mono);font-size:12px;color:var(--text-dim);margin-bottom:0.25rem">Encryption</h3>
        <p>Encrypted at rest and in transit. No data stored on Brooked servers.</p>
      </div>
      <div class="feature-card">
        <h3 style="font-family:var(--mono);font-size:12px;color:var(--text-dim);margin-bottom:0.25rem">Audit log</h3>
        <p>Every write logged with a full diff. Defend any report in front of leadership.</p>
      </div>
      <div class="feature-card">
        <h3 style="font-family:var(--mono);font-size:12px;color:var(--text-dim);margin-bottom:0.25rem">Uptime</h3>
        <p>99.9% uptime SLA for Enterprise plans.</p>
      </div>
    </div>
  </section>

  <!-- ── CTA ── -->
  <div class="cta-block">
    <div class="section-label" style="margin-bottom:0.75rem">Get started</div>
    <h2>Your live data, inside Google Sheets</h2>
    <p>Free tier includes 100 imports per month. No credit card required.</p>
    <div style="display:flex;justify-content:center;gap:12px;flex-wrap:wrap">
      <a href="https://workspace.google.com/marketplace/app/brooked_ai_snowflake_quickbooks_hubspot/887798732409" class="btn-large">Install free on Workspace Marketplace →</a>
      <a href="https://brooked.io/pricing" class="btn-outline">View pricing</a>
    </div>
  </div>
</div>

<!-- ── Footer ── -->
<footer>
  <div class="container">
    <div class="footer-inner">
      <div class="footer-links">
        <a href="https://brooked.io">brooked.io</a>
        <a href="https://brooked.io/guides">Guides</a>
        <a href="https://brooked.io/pricing">Pricing</a>
        <a href="https://brooked.io/integrations">Integrations</a>
        <a href="https://brooked.io/news">News</a>
        <a href="https://brooked.io/support">Support</a>
        <a href="https://twitter.com/brookedapp">@brookedapp</a>
      </div>
      <span class="footer-copy">© 2026 Brooked</span>
    </div>
  </div>
</footer>

</body>
</html>
