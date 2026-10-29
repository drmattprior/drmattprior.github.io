---
layout: post
title: 'BMI Calculator'
description: 'Calculate your body mass index (BMI) in metric or imperial units, using height in metres or feet and inches, and weight in kg or stone and pounds.'
date: 2020-01-01
image: '/images/bmi-calculator-featured-image.jpg'
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
  .container { width: 100%;}
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
    width: 140px;
  }
  .toggle-btn {
    flex: 1;
    padding: 8px 0;
    font-size: 14px;
    font-weight: 600;
    border-radius: 8px;
    border: none;
    cursor: pointer;
    background: transparent;
    color: #6b7280;
  }
  .toggle-btn.active { background-color: #111827; color: #fff; }
  input[type="number"] {
    width: 100%;
    padding: 12px 14px;
    font-size: 17px;
    border-radius: 10px;
    border: 1px solid #d1d5db;
    outline: none;
    background: #fff;
  }
  .row { display: flex; gap: 10px; }
  .row > div { flex: 1; }
  .unit-label { font-size: 12px; color: #9ca3af; margin-top: 4px; text-align: center; }
  .hidden { display: none; }
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
  .result-value { font-size: 48px; font-weight: 800; color: #111827; margin: 6px 0; }
  .result-badge {
    display: inline-block;
    padding: 6px 16px;
    border-radius: 999px;
    font-weight: 700;
    font-size: 14px;
  }
  .placeholder { color: #9ca3af; font-size: 15px; }
  .targets { margin-top: 16px; padding-top: 14px; border-top: 1px solid #f1f5f9; }
  .target-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 6px 0;
    font-size: 14px;
  }
  .target-label { color: #6b7280; }
  .target-value { color: #111827; font-weight: 600; }
  .footnote { font-size: 12px; color: #9ca3af; text-align: center; margin-top: 14px; line-height: 1.5; }
</style>


<div class="container">
  <!-- Height -->
  <div class="card">
    <div class="card-header">
      <span>Height</span>
      <div class="toggle-wrap">
        <button class="toggle-btn active" id="hUnitM" onclick="setHeightUnit('m')">m</button>
        <button class="toggle-btn" id="hUnitFt" onclick="setHeightUnit('ft')">ft/in</button>
      </div>
    </div>
    <div id="heightM">
      <input type="number" inputmode="decimal" id="heightMInput" value="1.75" placeholder="1.75">
    </div>
    <div id="heightFt" class="row hidden">
      <div>
        <input type="number" inputmode="numeric" id="heightFtInput" value="5" placeholder="5">
        <div class="unit-label">feet</div>
      </div>
      <div>
        <input type="number" inputmode="numeric" id="heightInInput" value="9" placeholder="9">
        <div class="unit-label">inches</div>
      </div>
    </div>
  </div>

  <!-- Weight -->
  <div class="card">
    <div class="card-header">
      <span>Weight</span>
      <div class="toggle-wrap">
        <button class="toggle-btn active" id="wUnitKg" onclick="setWeightUnit('kg')">kg</button>
        <button class="toggle-btn" id="wUnitSt" onclick="setWeightUnit('stone')">st/lb</button>
      </div>
    </div>
    <div id="weightKg">
      <input type="number" inputmode="decimal" id="weightKgInput" value="70" placeholder="70">
    </div>
    <div id="weightSt" class="row hidden">
      <div>
        <input type="number" inputmode="numeric" id="weightStInput" value="11" placeholder="11">
        <div class="unit-label">stone</div>
      </div>
      <div>
        <input type="number" inputmode="numeric" id="weightLbInput" value="0" placeholder="0">
        <div class="unit-label">pounds</div>
      </div>
    </div>
  </div>

  <button class="calc-btn" onclick="calculate()">Calculate BMI</button>

  <div class="card result-card">
    <div id="resultArea">
      <div class="placeholder">Enter your height and weight, then tap Calculate</div>
    </div>
  </div>

  <div class="footnote">BMI is a general screening measure and doesn't account for muscle mass, frame size, or other factors.</div>
</div>

<script>
  let heightUnit = 'm';
  let weightUnit = 'kg';

  function setHeightUnit(unit) {
    heightUnit = unit;
    document.getElementById('hUnitM').classList.toggle('active', unit === 'm');
    document.getElementById('hUnitFt').classList.toggle('active', unit === 'ft');
    document.getElementById('heightM').classList.toggle('hidden', unit !== 'm');
    document.getElementById('heightFt').classList.toggle('hidden', unit !== 'ft');
  }

  function setWeightUnit(unit) {
    weightUnit = unit;
    document.getElementById('wUnitKg').classList.toggle('active', unit === 'kg');
    document.getElementById('wUnitSt').classList.toggle('active', unit === 'stone');
    document.getElementById('weightKg').classList.toggle('hidden', unit !== 'kg');
    document.getElementById('weightSt').classList.toggle('hidden', unit !== 'stone');
  }

  function calculate() {
    let heightInMeters;
    if (heightUnit === 'm') {
      const v = parseFloat(document.getElementById('heightMInput').value);
      heightInMeters = isNaN(v) ? 0 : v;
    } else {
      const ft = parseFloat(document.getElementById('heightFtInput').value) || 0;
      const inch = parseFloat(document.getElementById('heightInInput').value) || 0;
      heightInMeters = (ft * 12 + inch) * 0.0254;
    }

    let weightInKg;
    if (weightUnit === 'kg') {
      const v = parseFloat(document.getElementById('weightKgInput').value);
      weightInKg = isNaN(v) ? 0 : v;
    } else {
      const st = parseFloat(document.getElementById('weightStInput').value) || 0;
      const lb = parseFloat(document.getElementById('weightLbInput').value) || 0;
      weightInKg = (st * 14 + lb) * 0.453592;
    }

    const resultArea = document.getElementById('resultArea');

    if (heightInMeters <= 0 || weightInKg <= 0) {
      resultArea.innerHTML = '<div class="placeholder">Enter your height and weight, then tap Calculate</div>';
      return;
    }

    const bmi = weightInKg / (heightInMeters * heightInMeters);
    let label, color;
    if (bmi < 18.5) { label = 'Underweight'; color = '#3b82f6'; }
    else if (bmi < 25) { label = 'Healthy weight'; color = '#16a34a'; }
    else if (bmi < 30) { label = 'Overweight'; color = '#f59e0b'; }
    else { label = 'Obese'; color = '#dc2626'; }

    const weight18 = 18 * heightInMeters * heightInMeters;
    const weight20 = 20 * heightInMeters * heightInMeters;
    const weight25 = 25 * heightInMeters * heightInMeters;
    const weight30 = 30 * heightInMeters * heightInMeters;
    const weight35 = 35 * heightInMeters * heightInMeters;

    resultArea.innerHTML = `
      <div class="result-label">Your BMI</div>
      <div class="result-value">${bmi.toFixed(1)}</div>
      <div class="result-badge" style="background-color:${color}20;color:${color}">${label}</div>
      <div class="targets">
        <div class="target-row">
          <span class="target-label">Weight at BMI 18</span>
          <span class="target-value">${formatWeight(weight18)}</span>
        </div>
        <div class="target-row">
          <span class="target-label">Weight at BMI 20</span>
          <span class="target-value">${formatWeight(weight20)}</span>
        </div>
        <div class="target-row">
          <span class="target-label">Weight at BMI 25</span>
          <span class="target-value">${formatWeight(weight25)}</span>
        </div>
        <div class="target-row">
          <span class="target-label">Weight at BMI 30</span>
          <span class="target-value">${formatWeight(weight30)}</span>
        </div>
        <div class="target-row">
          <span class="target-label">Weight at BMI 35</span>
          <span class="target-value">${formatWeight(weight35)}</span>
        </div>
      </div>
    `;
  }

  function formatWeight(kg) {
    const totalLb = kg / 0.453592;
    const stone = Math.floor(totalLb / 14);
    const lb = Math.round(totalLb % 14);
    return `${kg.toFixed(1)} kg (${stone}st ${lb}lb)`;
  }
</script>
