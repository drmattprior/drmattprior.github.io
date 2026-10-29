---
layout: post
title: 'Pregnancy Calculator'
description: 'Calculate your estimated due date and how many weeks pregnant you are, from your last period or embryo transfer date.'
date: 2020-01-01
image: '/images/pregnancy-calculator-featured-image.jpg'
tags: [Calculators]
tags_color: '#58C1E9'
featured: false
last_modified_at: 2026-11-05
---

<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    background-color: #f8fafc;
    display: flex;
    justify-content: center;
    padding: 24px 16px;
  }
  .container { width: 100%; }
  h1 { font-size: 24px; font-weight: 700; margin-bottom: 20px; color: #111827; }
  .card {
    background: #fff;
    border-radius: 16px;
    padding: 18px;
    margin-bottom: 14px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.06);
  }
  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
  }
  .card-header span { font-weight: 600; color: #374151; }
  .toggle-wrap {
    display: flex;
    background-color: #f1f5f9;
    border-radius: 10px;
    padding: 3px;
    margin-bottom: 14px;
  }
  .toggle-btn {
    flex: 1;
    padding: 10px 0;
    font-size: 13px;
    font-weight: 600;
    border-radius: 8px;
    border: none;
    cursor: pointer;
    background: transparent;
    color: #6b7280;
  }
  .toggle-btn.active { background-color: #111827; color: #fff; }
  input[type="date"] {
    width: 100%;
    padding: 12px 14px;
    font-size: 17px;
    border-radius: 10px;
    border: 1px solid #d1d5db;
    outline: none;
    background: #fff;
    color: #111827;
  }
  .hint { font-size: 12px; color: #9ca3af; margin-top: 8px; line-height: 1.5; }
  .calc-btn {
    width: 100%;
    padding: 15px 0;
    font-size: 16px;
    font-weight: 700;
    color: #fff;
    background-color: #111827;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    margin-bottom: 14px;
  }
  .result-card { text-align: center; }
  .result-label {
    font-size: 13px; color: #9ca3af; font-weight: 600;
    letter-spacing: 0.03em; text-transform: uppercase;
  }
  .result-value { font-size: 40px; font-weight: 800; color: #111827; margin: 6px 0 4px; }
  .result-sub { font-size: 14px; color: #6b7280; margin-bottom: 4px; }
  .divider { margin: 16px 0; border-top: 1px solid #f1f5f9; }
  .edd-value { font-size: 26px; font-weight: 800; color: #7c3aed; margin: 6px 0; }
  .placeholder { color: #9ca3af; font-size: 15px; }
  .footnote { font-size: 12px; color: #9ca3af; text-align: center; margin-top: 14px; line-height: 1.5; }
</style>

<div class="container">
  <div class="card">
    <div class="card-header">
      <span>Calculate from</span>
    </div>
    <div class="toggle-wrap">
      <button class="toggle-btn active" id="modeLMP" onclick="setMode('lmp')">LMP</button>
      <button class="toggle-btn" id="modeIVF" onclick="setMode('ivf')">Fertilisation (IVF)</button>
    </div>

    <div id="lmpInputWrap">
      <input type="date" id="lmpInput">
      <div class="hint">First day of your last menstrual period.</div>
    </div>

    <div id="ivfInputWrap" class="hidden" style="display:none;">
      <input type="date" id="ivfInput">
      <div class="hint">Day of fertilisation / egg collection. Gestational age is dated from an equivalent LMP 14 days earlier, as standard convention.</div>
    </div>
  </div>

  <button class="calc-btn" onclick="calculate()">Calculate</button>

  <div class="card result-card">
    <div id="resultArea">
      <div class="placeholder">Enter a date, then tap Calculate</div>
    </div>
  </div>

  <div class="footnote">Estimates only, based on a standard 280-day (40-week) gestation. Your clinician's dating scan may adjust this.</div>
</div>

<script>
  let mode = 'lmp';

  function setMode(m) {
    mode = m;
    document.getElementById('modeLMP').classList.toggle('active', m === 'lmp');
    document.getElementById('modeIVF').classList.toggle('active', m === 'ivf');
    document.getElementById('lmpInputWrap').style.display = m === 'lmp' ? 'block' : 'none';
    document.getElementById('ivfInputWrap').style.display = m === 'ivf' ? 'block' : 'none';
  }

  function calculate() {
    const resultArea = document.getElementById('resultArea');
    let inputVal;

    if (mode === 'lmp') {
      inputVal = document.getElementById('lmpInput').value;
    } else {
      inputVal = document.getElementById('ivfInput').value;
    }

    if (!inputVal) {
      resultArea.innerHTML = '<div class="placeholder">Enter a date, then tap Calculate</div>';
      return;
    }

    // Parse as local date (avoid timezone shift from ISO parsing)
    const [y, m, d] = inputVal.split('-').map(Number);
    let startDate = new Date(y, m - 1, d);

    if (mode === 'ivf') {
      // Equivalent LMP is 14 days before fertilisation
      startDate.setDate(startDate.getDate() - 14);
    }

    const today = new Date();
    today.setHours(0, 0, 0, 0);
    startDate.setHours(0, 0, 0, 0);

    const diffMs = today - startDate;
    const diffDays = Math.round(diffMs / (1000 * 60 * 60 * 24));

    const eddDate = new Date(startDate);
    eddDate.setDate(eddDate.getDate() + 280);

    const eddFormatted = eddDate.toLocaleDateString('en-GB', {
      day: 'numeric', month: 'long', year: 'numeric'
    });

    if (diffDays < 0) {
      resultArea.innerHTML = `
        <div class="result-label">Gestation today</div>
        <div class="result-value">Not started</div>
        <div class="result-sub">Selected date is in the future</div>
        <div class="divider"></div>
        <div class="result-label">Estimated due date</div>
        <div class="edd-value">${eddFormatted}</div>
      `;
      return;
    }

    const weeks = Math.floor(diffDays / 7);
    const days = diffDays % 7;

    resultArea.innerHTML = `
      <div class="result-label">Gestation today</div>
      <div class="result-value">${weeks}<span style="font-size:22px;">w</span> ${days}<span style="font-size:22px;">d</span></div>
      <div class="divider"></div>
      <div class="result-label">Estimated due date</div>
      <div class="edd-value">${eddFormatted}</div>
    `;
  }
</script>
