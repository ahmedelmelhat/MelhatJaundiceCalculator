# Melhat Neonatal Jaundice Calculator
Melhat Neonatal Jaundice Calculator A web-based calculator for determining phototherapy and exchange transfusion thresholds for neonatal jaundice based on NICE guideline CG98 




 <!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Melhat Neonatal Jaundice Calculator</title>
  <style>
    /* --- Reset & base --- */
    * { box-sizing: border-box; }
    html,body { height:100%; margin:0; font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial; background: linear-gradient(180deg,#0f172a 0%, #071226 100%); color:#e6eef8; }
    .container { max-width:980px; margin:32px auto; padding:28px; background: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01)); border-radius:14px; box-shadow: 0 10px 30px rgba(2,6,23,0.7); border: 1px solid rgba(255,255,255,0.03); }
    header { display:flex; gap:16px; align-items:center; margin-bottom:18px; }
    .brand {
      width:76px; height:76px; border-radius:14px; display:flex; align-items:center; justify-content:center;
      background: linear-gradient(135deg,#06b6d4,#7c3aed); color:white; font-weight:600; font-size:14px; box-shadow: 0 6px 18px rgba(124,58,237,0.18);
    }
    h1 { margin:0; font-size:20px; letter-spacing:0.2px; }
    p.lead { margin:0; opacity:0.85; font-size:13px; color:#cfe7ff; }
    .grid { display:grid; grid-template-columns: 1fr 360px; gap:20px; margin-top:20px; }
    @media (max-width:920px){ .grid{ grid-template-columns: 1fr; } .right-col{ order:-1; } }
    /* form */
    .card { background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); padding:18px; border-radius:12px; border: 1px solid rgba(255,255,255,0.02); box-shadow: 0 6px 20px rgba(2,6,23,0.45); }
    label { display:block; font-size:13px; color:#d7ecff; margin-bottom:8px; font-weight:600; }
    .row { display:flex; gap:12px; margin-bottom:12px; }
    .col { flex:1; }
    input[type="text"], input[type="number"], input[type="datetime-local"], select {
      width:100%; padding:10px 12px; background:rgba(255,255,255,0.02); border-radius:8px; border: 1px solid rgba(255,255,255,0.04); color:#e9f6ff; font-size:14px;
    }
    input[type="checkbox"] { transform:scale(1.1); margin-right:8px; }
    .small { font-size:12px; color:#9fc7ff; margin-top:6px; }
    .btn {
      display:inline-block; background: linear-gradient(90deg,#06b6d4,#7c3aed); color:white; padding:10px 16px; border-radius:10px; border:none; cursor:pointer; font-weight:700;
      box-shadow: 0 8px 20px rgba(7,12,26,0.55);
    }
    .btn.secondary { background: transparent; border:1px solid rgba(255,255,255,0.06); color:#cfe7ff; box-shadow:none; }
    /* results */
    .result { margin-top:14px; padding:14px; border-radius:10px; background: linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.00)); border:1px solid rgba(255,255,255,0.02); }
    .badge { display:inline-block; padding:6px 10px; border-radius:999px; font-weight:700; font-size:13px; }
    .badge.ok { background: rgba(34,197,94,0.12); color:#a7f3d0; border:1px solid rgba(34,197,94,0.18); }
    .badge.warn { background: rgba(250,204,21,0.09); color:#ffefb0; border:1px solid rgba(250,204,21,0.12); }
    .badge.danger { background: rgba(244,63,94,0.09); color:#ffd1da; border:1px solid rgba(244,63,94,0.12); }
    .thresholds { display:flex; gap:10px; margin-top:10px; }
    .th { flex:1; padding:10px; border-radius:8px; background: rgba(255,255,255,0.01); border:1px dashed rgba(255,255,255,0.02); text-align:center; }
    .th h3{ margin:0; font-size:16px; color:#e6f8ff; }
    .th p { margin:6px 0 0 0; font-size:13px; color:#bfe6ff; }
    footer { margin-top:18px; font-size:12px; color:#9fbfe0; opacity:0.9; display:flex; justify-content:space-between; align-items:center; }
    .disclaimer { font-size:12px; color:#9fbfe0; opacity:0.85; margin-top:12px; }
    /* helper */
    .muted { color:#93b7d8; font-size:13px; }
    .note { font-size:12px; color:#bfe6ff; opacity:0.9; margin-top:8px; }
    .small-muted { font-size:12px; color:#93b7d8; }
    /* Graph styles */
    .chart-container { 
      position: relative; 
      height: 300px; 
      margin-top: 16px;
    }
  </style>
  <!-- Include Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
<div class="container" role="main" aria-labelledby="appTitle">
  <header>
    <div class="brand" aria-hidden="true">MNJC</div>
    <div>
      <h1 id="appTitle">Melhat Neonatal Jaundice Calculator</h1>
      <p class="lead">Quickly check phototherapy / exchange thresholds using NICE guideline CG98 thresholds.</p>
    </div>
  </header>
  <div class="grid">
    <!-- left: inputs -->
    <div>
      <div class="card">
        <label>Patient info (optional)</label>
        <div class="row">
          <input id="babyName" type="text" placeholder="Baby name (optional)" />
          <input id="hospitalNo" type="text" placeholder="Hospital no." />
        </div>
        <hr style="opacity:0.06;margin:12px 0;">
        <label>Age (provide either DOB/time OR manual hours)</label>
        <div class="row">
          <div class="col">
            <label class="small-muted">Date & time of birth</label>
            <input id="dob" type="datetime-local" />
            <div class="small">If provided, age in hours will be calculated automatically.</div>
          </div>
          <div class="col">
            <label class="small-muted">Age (hours) — manual</label>
            <input id="ageHours" type="number" min="0" step="0.1" placeholder="e.g. 36" />
          </div>
        </div>
        <label style="margin-top:8px;">Gestational age (weeks)</label>
        <div class="row" style="align-items:center">
          <input id="gestWeeks" type="number" min="22" max="44" step="0.1" placeholder="e.g. 38" />
          <select id="riskFactors" style="width:200px">
            <option value="none">No major risk factors</option>
            <option value="isoimmune">Isoimmune hemolysis</option>
            <option value="g6pd">G6PD deficiency</option>
            <option value="sepsis">Suspected sepsis</option>
            <option value="other">Other risk factors</option>
          </select>
        </div>
        <label style="margin-top:12px;">Serum bilirubin (mg/dL)</label>
        <div class="row">
          <input id="bilirubin" type="number" min="0" step="0.1" placeholder="e.g. 12.5" />
          <button class="btn" id="calcBtn">Calculate</button>
        </div>
        <div class="small-muted note">Entered bilirubin (mg/dL) is converted to µmol/L (mg/dL × 17.1) for calculations and displayed in both units. Thresholds are in µmol/L per NICE guideline CG98.</div>
      </div>
      <div class="card" style="margin-top:16px;">
        <label>Options</label>
        <div style="display:flex;gap:10px;align-items:center;margin-top:8px;">
          <label style="display:flex;align-items:center"><input id="showInterpolation" type="checkbox" checked /> Show interpolation details</label>
          <button id="resetBtn" class="btn secondary" style="margin-left:auto">Reset</button>
        </div>
        <div class="disclaimer">
          <strong>Clinical note:</strong> This tool is informational only, based on NICE guideline CG98. Use clinical judgement and local guidelines. Always verify before changing management.
        </div>
      </div>
    </div>
    <!-- right: results -->
    <div class="right-col">
      <div class="card">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <div>
            <div class="muted">Result</div>
            <h2 id="resultTitle" style="margin:8px 0 0 0;font-size:18px;color:#eaf8ff">No data yet</h2>
          </div>
          <div style="text-align:right">
            <div class="muted">Age (hours)</div>
            <div id="showHours" style="font-weight:700;font-size:18px;margin-top:6px">—</div>
          </div>
        </div>
        <div class="result" id="resultCard" style="margin-top:14px; display:none;">
          <div style="display:flex;gap:12px;align-items:center;justify-content:space-between;">
            <div>
              <div class="small-muted">Bilirubin</div>
              <div style="font-size:22px;font-weight:800" id="showBili">— µmol/L</div>
              <div class="small" id="showGest">— wk</div>
            </div>
            <div style="min-width:160px;">
              <div class="small-muted">Recommendation</div>
              <div id="recBadge" style="margin-top:8px"></div>
            </div>
          </div>
          <div class="thresholds" style="margin-top:14px">
            <div class="th">
              <h3>Phototherapy threshold</h3>
              <p id="ptThresh">— µmol/L</p>
            </div>
            <div class="th">
              <h3>Exchange transfusion</h3>
              <p id="exThresh">— µmol/L</p>
            </div>
          </div>
          <div id="interp" class="small-muted" style="margin-top:12px; display:none"></div>
        </div>
        <div id="emptyHint" style="margin-top:12px; color:#9fbfe0;">
          Enter age and bilirubin and click <em>Calculate</em> to view thresholds & recommendation.
        </div>
      </div>
      
      <!-- Graph Section -->
      <div class="card" id="graphCard" style="margin-top:14px; display:none;">
        <h3 style="margin:0 0 6px 0">Bilirubin Thresholds Graph</h3>
        <div class="chart-container">
          <canvas id="bilirubinChart"></canvas>
        </div>
      </div>
      
      <div class="card" style="margin-top:14px;">
        <h3 style="margin:0 0 6px 0">About</h3>
        <div class="small-muted">This calculator uses the threshold lookup table from NICE guideline CG98 ("Data sheet"). Age-based thresholds are interpolated between table rows for smooth results.</div>
        <div style="margin-top:12px">
          <strong>How it works</strong>
          <ul style="color:#bfe6ff;margin:6px 0 0 18px; padding-left:14px">
            <li>Find the threshold phototherapy/exchange values for the baby's age (hours).</li>
            <li>Compare the total serum bilirubin level to thresholds (in µmol/L).</li>
            <li>If bilirubin ≥ exchange threshold → recommend exchange transfusion. Else if ≥ phototherapy threshold → phototherapy recommended.</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <footer>
    <div>Built from NICE guideline CG98 thresholds</div>
    <div style="opacity:0.85">Melhat Neonatal Jaundice Calculator • v1</div>
  </footer>
</div>
<script>
/*
  Threshold tables extracted from NICE guideline CG98 for different gestational ages.
  Each table: [hours, phototherapy_threshold_µmol/L, exchange_threshold_µmol/L]
  Values are in µmol/L as provided in the NICE guideline CG98.
*/
// Unit conversion: mg/dL to µmol/L for bilirubin calculations
const MGDL_TO_UMOLL = 17.1;
const toUmolL = (mgdl) => mgdl * MGDL_TO_UMOLL;
const toMgDl = (umoll) => umoll / MGDL_TO_UMOLL;

// Threshold tables for different gestational ages
const thresholdTables = {
  23: [
    [0, 40, 80], [6, 48, 92], [12, 55, 105], [18, 62, 118], [24, 70, 130], [30, 78, 142], [36, 85, 155], [42, 92, 168], [48, 100, 180], [54, 108, 192], [60, 115, 205], [66, 122, 218], [72, 130, 230], [78, 130, 230], [84, 130, 230], [90, 130, 230], [96, 130, 230], [102, 130, 230], [108, 130, 230], [114, 130, 230], [120, 130, 230], [126, 130, 230], [132, 130, 230], [138, 130, 230], [144, 130, 230], [150, 130, 230], [156, 130, 230], [162, 130, 230], [168, 130, 230], [174, 130, 230], [180, 130, 230], [186, 130, 230], [192, 130, 230], [198, 130, 230], [204, 130, 230], [210, 130, 230], [216, 130, 230], [222, 130, 230], [228, 130, 230], [234, 130, 230], [240, 130, 230], [246, 130, 230], [252, 130, 230], [258, 130, 230], [264, 130, 230], [270, 130, 230], [276, 130, 230], [282, 130, 230], [288, 130, 230], [294, 130, 230], [300, 130, 230], [306, 130, 230], [312, 130, 230], [318, 130, 230], [324, 130, 230], [330, 130, 230], [336, 130, 230]
  ],
  24: [
    [0, 40, 80], [6, 48, 93], [12, 57, 107], [18, 65, 120], [24, 73, 133], [30, 82, 147], [36, 90, 160], [42, 98, 173], [48, 107, 187], [54, 115, 200], [60, 123, 213], [66, 132, 227], [72, 140, 240], [78, 140, 240], [84, 140, 240], [90, 140, 240], [96, 140, 240], [102, 140, 240], [108, 140, 240], [114, 140, 240], [120, 140, 240], [126, 140, 240], [132, 140, 240], [138, 140, 240], [144, 140, 240], [150, 140, 240], [156, 140, 240], [162, 140, 240], [168, 140, 240], [174, 140, 240], [180, 140, 240], [186, 140, 240], [192, 140, 240], [198, 140, 240], [204, 140, 240], [210, 140, 240], [216, 140, 240], [222, 140, 240], [228, 140, 240], [234, 140, 240], [240, 140, 240], [246, 140, 240], [252, 140, 240], [258, 140, 240], [264, 140, 240], [270, 140, 240], [276, 140, 240], [282, 140, 240], [288, 140, 240], [294, 140, 240], [300, 140, 240], [306, 140, 240], [312, 140, 240], [318, 140, 240], [324, 140, 240], [330, 140, 240], [336, 140, 240]
  ],
  25: [
    [0, 40, 80], [6, 49, 94], [12, 58, 108], [18, 68, 122], [24, 77, 137], [30, 86, 151], [36, 95, 165], [42, 104, 179], [48, 113, 193], [54, 122, 208], [60, 132, 222], [66, 141, 236], [72, 150, 250], [78, 150, 250], [84, 150, 250], [90, 150, 250], [96, 150, 250], [102, 150, 250], [108, 150, 250], [114, 150, 250], [120, 150, 250], [126, 150, 250], [132, 150, 250], [138, 150, 250], [144, 150, 250], [150, 150, 250], [156, 150, 250], [162, 150, 250], [168, 150, 250], [174, 150, 250], [180, 150, 250], [186, 150, 250], [192, 150, 250], [198, 150, 250], [204, 150, 250], [210, 150, 250], [216, 150, 250], [222, 150, 250], [228, 150, 250], [234, 150, 250], [240, 150, 250], [246, 150, 250], [252, 150, 250], [258, 150, 250], [264, 150, 250], [270, 150, 250], [276, 150, 250], [282, 150, 250], [288, 150, 250], [294, 150, 250], [300, 150, 250], [306, 150, 250], [312, 150, 250], [318, 150, 250], [324, 150, 250], [330, 150, 250], [336, 150, 250]
  ],
  26: [
    [0, 40, 80], [6, 50, 95], [12, 60, 110], [18, 70, 125], [24, 80, 140], [30, 90, 155], [36, 100, 170], [42, 110, 185], [48, 120, 200], [54, 130, 215], [60, 140, 230], [66, 150, 245], [72, 160, 260], [78, 160, 260], [84, 160, 260], [90, 160, 260], [96, 160, 260], [102, 160, 260], [108, 160, 260], [114, 160, 260], [120, 160, 260], [126, 160, 260], [132, 160, 260], [138, 160, 260], [144, 160, 260], [150, 160, 260], [156, 160, 260], [162, 160, 260], [168, 160, 260], [174, 160, 260], [180, 160, 260], [186, 160, 260], [192, 160, 260], [198, 160, 260], [204, 160, 260], [210, 160, 260], [216, 160, 260], [222, 160, 260], [228, 160, 260], [234, 160, 260], [240, 160, 260], [246, 160, 260], [252, 160, 260], [258, 160, 260], [264, 160, 260], [270, 160, 260], [276, 160, 260], [282, 160, 260], [288, 160, 260], [294, 160, 260], [300, 160, 260], [306, 160, 260], [312, 160, 260], [318, 160, 260], [324, 160, 260], [330, 160, 260], [336, 160, 260]
  ],
  27: [
    [0, 40, 80], [6, 51, 96], [12, 62, 112], [18, 72, 128], [24, 83, 143], [30, 94, 159], [36, 105, 175], [42, 116, 191], [48, 127, 207], [54, 138, 222], [60, 148, 238], [66, 159, 254], [72, 170, 270], [78, 170, 270], [84, 170, 270], [90, 170, 270], [96, 170, 270], [102, 170, 270], [108, 170, 270], [114, 170, 270], [120, 170, 270], [126, 170, 270], [132, 170, 270], [138, 170, 270], [144, 170, 270], [150, 170, 270], [156, 170, 270], [162, 170, 270], [168, 170, 270], [174, 170, 270], [180, 170, 270], [186, 170, 270], [192, 170, 270], [198, 170, 270], [204, 170, 270], [210, 170, 270], [216, 170, 270], [222, 170, 270], [228, 170, 270], [234, 170, 270], [240, 170, 270], [246, 170, 270], [252, 170, 270], [258, 170, 270], [264, 170, 270], [270, 170, 270], [276, 170, 270], [282, 170, 270], [288, 170, 270], [294, 170, 270], [300, 170, 270], [306, 170, 270], [312, 170, 270], [318, 170, 270], [324, 170, 270], [330, 170, 270], [336, 170, 270]
  ],
  28: [
    [0, 40, 80], [6, 52, 97], [12, 63, 113], [18, 75, 130], [24, 87, 147], [30, 98, 163], [36, 110, 180], [42, 122, 197], [48, 133, 213], [54, 145, 230], [60, 157, 247], [66, 168, 263], [72, 180, 280], [78, 180, 280], [84, 180, 280], [90, 180, 280], [96, 180, 280], [102, 180, 280], [108, 180, 280], [114, 180, 280], [120, 180, 280], [126, 180, 280], [132, 180, 280], [138, 180, 280], [144, 180, 280], [150, 180, 280], [156, 180, 280], [162, 180, 280], [168, 180, 280], [174, 180, 280], [180, 180, 280], [186, 180, 280], [192, 180, 280], [198, 180, 280], [204, 180, 280], [210, 180, 280], [216, 180, 280], [222, 180, 280], [228, 180, 280], [234, 180, 280], [240, 180, 280], [246, 180, 280], [252, 180, 280], [258, 180, 280], [264, 180, 280], [270, 180, 280], [276, 180, 280], [282, 180, 280], [288, 180, 280], [294, 180, 280], [300, 180, 280], [306, 180, 280], [312, 180, 280], [318, 180, 280], [324, 180, 280], [330, 180, 280], [336, 180, 280]
  ],
  29: [
    [0, 40, 80], [6, 52, 98], [12, 65, 115], [18, 78, 132], [24, 90, 150], [30, 102, 168], [36, 115, 185], [42, 128, 202], [48, 140, 220], [54, 152, 238], [60, 165, 255], [66, 178, 272], [72, 190, 290], [78, 190, 290], [84, 190, 290], [90, 190, 290], [96, 190, 290], [102, 190, 290], [108, 190, 290], [114, 190, 290], [120, 190, 290], [126, 190, 290], [132, 190, 290], [138, 190, 290], [144, 190, 290], [150, 190, 290], [156, 190, 290], [162, 190, 290], [168, 190, 290], [174, 190, 290], [180, 190, 290], [186, 190, 290], [192, 190, 290], [198, 190, 290], [204, 190, 290], [210, 190, 290], [216, 190, 290], [222, 190, 290], [228, 190, 290], [234, 190, 290], [240, 190, 290], [246, 190, 290], [252, 190, 290], [258, 190, 290], [264, 190, 290], [270, 190, 290], [276, 190, 290], [282, 190, 290], [288, 190, 290], [294, 190, 290], [300, 190, 290], [306, 190, 290], [312, 190, 290], [318, 190, 290], [324, 190, 290], [330, 190, 290], [336, 190, 290]
  ],
  30: [
    [0, 40, 80], [6, 53, 98], [12, 67, 117], [18, 80, 135], [24, 93, 153], [30, 107, 172], [36, 120, 190], [42, 133, 208], [48, 147, 227], [54, 160, 245], [60, 173, 263], [66, 187, 282], [72, 200, 300], [78, 200, 300], [84, 200, 300], [90, 200, 300], [96, 200, 300], [102, 200, 300], [108, 200, 300], [114, 200, 300], [120, 200, 300], [126, 200, 300], [132, 200, 300], [138, 200, 300], [144, 200, 300], [150, 200, 300], [156, 200, 300], [162, 200, 300], [168, 200, 300], [174, 200, 300], [180, 200, 300], [186, 200, 300], [192, 200, 300], [198, 200, 300], [204, 200, 300], [210, 200, 300], [216, 200, 300], [222, 200, 300], [228, 200, 300], [234, 200, 300], [240, 200, 300], [246, 200, 300], [252, 200, 300], [258, 200, 300], [264, 200, 300], [270, 200, 300], [276, 200, 300], [282, 200, 300], [288, 200, 300], [294, 200, 300], [300, 200, 300], [306, 200, 300], [312, 200, 300], [318, 200, 300], [324, 200, 300], [330, 200, 300], [336, 200, 300]
  ],
  31: [
    [0, 40, 80], [6, 54, 99], [12, 68, 118], [18, 82, 138], [24, 97, 157], [30, 111, 176], [36, 125, 195], [42, 139, 214], [48, 153, 233], [54, 168, 252], [60, 182, 272], [66, 196, 291], [72, 210, 310], [78, 210, 310], [84, 210, 310], [90, 210, 310], [96, 210, 310], [102, 210, 310], [108, 210, 310], [114, 210, 310], [120, 210, 310], [126, 210, 310], [132, 210, 310], [138, 210, 310], [144, 210, 310], [150, 210, 310], [156, 210, 310], [162, 210, 310], [168, 210, 310], [174, 210, 310], [180, 210, 310], [186, 210, 310], [192, 210, 310], [198, 210, 310], [204, 210, 310], [210, 210, 310], [216, 210, 310], [222, 210, 310], [228, 210, 310], [234, 210, 310], [240, 210, 310], [246, 210, 310], [252, 210, 310], [258, 210, 310], [264, 210, 310], [270, 210, 310], [276, 210, 310], [282, 210, 310], [288, 210, 310], [294, 210, 310], [300, 210, 310], [306, 210, 310], [312, 210, 310], [318, 210, 310], [324, 210, 310], [330, 210, 310], [336, 210, 310]
  ],
  32: [
    [0, 40, 80], [6, 55, 100], [12, 70, 120], [18, 85, 140], [24, 100, 160], [30, 115, 180], [36, 130, 200], [42, 145, 220], [48, 160, 240], [54, 175, 260], [60, 190, 280], [66, 205, 300], [72, 220, 320], [78, 220, 320], [84, 220, 320], [90, 220, 320], [96, 220, 320], [102, 220, 320], [108, 220, 320], [114, 220, 320], [120, 220, 320], [126, 220, 320], [132, 220, 320], [138, 220, 320], [144, 220, 320], [150, 220, 320], [156, 220, 320], [162, 220, 320], [168, 220, 320], [174, 220, 320], [180, 220, 320], [186, 220, 320], [192, 220, 320], [198, 220, 320], [204, 220, 320], [210, 220, 320], [216, 220, 320], [222, 220, 320], [228, 220, 320], [234, 220, 320], [240, 220, 320], [246, 220, 320], [252, 220, 320], [258, 220, 320], [264, 220, 320], [270, 220, 320], [276, 220, 320], [282, 220, 320], [288, 220, 320], [294, 220, 320], [300, 220, 320], [306, 220, 320], [312, 220, 320], [318, 220, 320], [324, 220, 320], [330, 220, 320], [336, 220, 320]
  ],
  33: [
    [0, 40, 80], [6, 56, 101], [12, 72, 122], [18, 88, 142], [24, 103, 163], [30, 119, 184], [36, 135, 205], [42, 151, 226], [48, 167, 247], [54, 182, 268], [60, 198, 288], [66, 214, 309], [72, 230, 330], [78, 230, 330], [84, 230, 330], [90, 230, 330], [96, 230, 330], [102, 230, 330], [108, 230, 330], [114, 230, 330], [120, 230, 330], [126, 230, 330], [132, 230, 330], [138, 230, 330], [144, 230, 330], [150, 230, 330], [156, 230, 330], [162, 230, 330], [168, 230, 330], [174, 230, 330], [180, 230, 330], [186, 230, 330], [192, 230, 330], [198, 230, 330], [204, 230, 330], [210, 230, 330], [216, 230, 330], [222, 230, 330], [228, 230, 330], [234, 230, 330], [240, 230, 330], [246, 230, 330], [252, 230, 330], [258, 230, 330], [264, 230, 330], [270, 230, 330], [276, 230, 330], [282, 230, 330], [288, 230, 330], [294, 230, 330], [300, 230, 330], [306, 230, 330], [312, 230, 330], [318, 230, 330], [324, 230, 330], [330, 230, 330], [336, 230, 330]
  ],
  34: [
    [0, 40, 80], [6, 57, 102], [12, 73, 123], [18, 90, 145], [24, 107, 167], [30, 123, 188], [36, 140, 210], [42, 157, 232], [48, 173, 253], [54, 190, 275], [60, 207, 297], [66, 223, 318], [72, 240, 340], [78, 240, 340], [84, 240, 340], [90, 240, 340], [96, 240, 340], [102, 240, 340], [108, 240, 340], [114, 240, 340], [120, 240, 340], [126, 240, 340], [132, 240, 340], [138, 240, 340], [144, 240, 340], [150, 240, 340], [156, 240, 340], [162, 240, 340], [168, 240, 340], [174, 240, 340], [180, 240, 340], [186, 240, 340], [192, 240, 340], [198, 240, 340], [204, 240, 340], [210, 240, 340], [216, 240, 340], [222, 240, 340], [228, 240, 340], [234, 240, 340], [240, 240, 340], [246, 240, 340], [252, 240, 340], [258, 240, 340], [264, 240, 340], [270, 240, 340], [276, 240, 340], [282, 240, 340], [288, 240, 340], [294, 240, 340], [300, 240, 340], [306, 240, 340], [312, 240, 340], [318, 240, 340], [324, 240, 340], [330, 240, 340], [336, 240, 340]
  ],
  35: [
    [0, 40, 80], [6, 58, 102], [12, 75, 125], [18, 92, 148], [24, 110, 170], [30, 128, 192], [36, 145, 215], [42, 162, 238], [48, 180, 260], [54, 198, 282], [60, 215, 305], [66, 232, 328], [72, 250, 350], [78, 250, 350], [84, 250, 350], [90, 250, 350], [96, 250, 350], [102, 250, 350], [108, 250, 350], [114, 250, 350], [120, 250, 350], [126, 250, 350], [132, 250, 350], [138, 250, 350], [144, 250, 350], [150, 250, 350], [156, 250, 350], [162, 250, 350], [168, 250, 350], [174, 250, 350], [180, 250, 350], [186, 250, 350], [192, 250, 350], [198, 250, 350], [204, 250, 350], [210, 250, 350], [216, 250, 350], [222, 250, 350], [228, 250, 350], [234, 250, 350], [240, 250, 350], [246, 250, 350], [252, 250, 350], [258, 250, 350], [264, 250, 350], [270, 250, 350], [276, 250, 350], [282, 250, 350], [288, 250, 350], [294, 250, 350], [300, 250, 350], [306, 250, 350], [312, 250, 350], [318, 250, 350], [324, 250, 350], [330, 250, 350], [336, 250, 350]
  ],
  36: [
    [0, 40, 80], [6, 58, 103], [12, 77, 127], [18, 95, 150], [24, 113, 173], [30, 132, 197], [36, 150, 220], [42, 168, 243], [48, 187, 267], [54, 205, 290], [60, 223, 313], [66, 242, 337], [72, 260, 360], [78, 260, 360], [84, 260, 360], [90, 260, 360], [96, 260, 360], [102, 260, 360], [108, 260, 360], [114, 260, 360], [120, 260, 360], [126, 260, 360], [132, 260, 360], [138, 260, 360], [144, 260, 360], [150, 260, 360], [156, 260, 360], [162, 260, 360], [168, 260, 360], [174, 260, 360], [180, 260, 360], [186, 260, 360], [192, 260, 360], [198, 260, 360], [204, 260, 360], [210, 260, 360], [216, 260, 360], [222, 260, 360], [228, 260, 360], [234, 260, 360], [240, 260, 360], [246, 260, 360], [252, 260, 360], [258, 260, 360], [264, 260, 360], [270, 260, 360], [276, 260, 360], [282, 260, 360], [288, 260, 360], [294, 260, 360], [300, 260, 360], [306, 260, 360], [312, 260, 360], [318, 260, 360], [324, 260, 360], [330, 260, 360], [336, 260, 360]
  ],
  37: [
    [0, 40, 80], [6, 59, 104], [12, 78, 128], [18, 98, 152], [24, 117, 177], [30, 136, 201], [36, 155, 225], [42, 174, 249], [48, 193, 273], [54, 212, 298], [60, 232, 322], [66, 251, 346], [72, 270, 370], [78, 270, 370], [84, 270, 370], [90, 270, 370], [96, 270, 370], [102, 270, 370], [108, 270, 370], [114, 270, 370], [120, 270, 370], [126, 270, 370], [132, 270, 370], [138, 270, 370], [144, 270, 370], [150, 270, 370], [156, 270, 370], [162, 270, 370], [168, 270, 370], [174, 270, 370], [180, 270, 370], [186, 270, 370], [192, 270, 370], [198, 270, 370], [204, 270, 370], [210, 270, 370], [216, 270, 370], [222, 270, 370], [228, 270, 370], [234, 270, 370], [240, 270, 370], [246, 270, 370], [252, 270, 370], [258, 270, 370], [264, 270, 370], [270, 270, 370], [276, 270, 370], [282, 270, 370], [288, 270, 370], [294, 270, 370], [300, 270, 370], [306, 270, 370], [312, 270, 370], [318, 270, 370], [324, 270, 370], [330, 270, 370], [336, 270, 370]
  ],
  38: [
    [0, 100, 100], [6, 125, 150], [12, 150, 200], [18, 175, 250], [24, 200, 300], [30, 212, 350], [36, 225, 400], [42, 237, 450], [48, 250, 450], [54, 262, 450], [60, 275, 450], [66, 287, 450], [72, 300, 450], [78, 312, 450], [84, 325, 450], [90, 337, 450], [96, 350, 450], [102, 350, 450], [108, 350, 450], [114, 350, 450], [120, 350, 450], [126, 350, 450], [132, 350, 450], [138, 350, 450], [144, 350, 450], [150, 350, 450], [156, 350, 450], [162, 350, 450], [168, 350, 450], [174, 350, 450], [180, 350, 450], [186, 350, 450], [192, 350, 450], [198, 350, 450], [204, 350, 450], [210, 350, 450], [216, 350, 450], [222, 350, 450], [228, 350, 450], [234, 350, 450], [240, 350, 450], [246, 350, 450], [252, 350, 450], [258, 350, 450], [264, 350, 450], [270, 350, 450], [276, 350, 450], [282, 350, 450], [288, 350, 450], [294, 350, 450], [300, 350, 450], [306, 350, 450], [312, 350, 450], [318, 350, 450], [324, 350, 450], [330, 350, 450], [336, 350, 450]
  ]
};

function findThresholdsByHours(hours, gestWeeks) {
  // Determine which table to use based on gestational age
  let tableKey;
  if (gestWeeks < 23) {
    tableKey = 23; // Use 23 weeks as minimum
  } else if (gestWeeks >= 38) {
    tableKey = 38; // Use 38 weeks for 38+ weeks
  } else {
    // Round down to nearest integer week
    tableKey = Math.floor(gestWeeks);
  }
  
  const table = thresholdTables[tableKey].slice().sort((a,b)=>a[0]-b[0]);
  
  if (hours <= table[0][0]) return {pt: table[0][1], ex: table[0][2], interp: `used row at ${table[0][0]} h for ${tableKey} weeks GA`};
  if (hours >= table[table.length-1][0]) return {pt: table[table.length-1][1], ex: table[table.length-1][2], interp: `used last row at ${table[table.length-1][0]} h for ${tableKey} weeks GA`};
  
  let lo=null, hi=null;
  for (let i=0;i<table.length-1;i++){
    if (hours === table[i][0]) {
      return {pt: table[i][1], ex: table[i][2], interp: `exact match at ${table[i][0]} h for ${tableKey} weeks GA`};
    }
    if (hours > table[i][0] && hours < table[i+1][0]) { lo = table[i]; hi = table[i+1]; break; }
  }
  
  if (!lo || !hi) return {pt: table[0][1], ex: table[0][2], interp: 'fallback'};
  
  const ratio = (hours - lo[0]) / (hi[0] - lo[0]);
  const pt = lo[1] + ratio * (hi[1] - lo[1]);
  const ex = lo[2] + ratio * (hi[2] - lo[2]);
  const interp = `interpolated between ${lo[0]}h (${lo[1].toFixed(1)}/${lo[2].toFixed(1)} µmol/L) and ${hi[0]}h (${hi[1].toFixed(1)}/${hi[2].toFixed(1)} µmol/L) for ${tableKey} weeks GA → ratio ${ratio.toFixed(2)}`;
  
  return {pt: +pt.toFixed(1), ex: +ex.toFixed(1), interp};
}
// Chart variables
let bilirubinChart = null;
function resetUI(){
  document.getElementById('dob').value = '';
  document.getElementById('ageHours').value = '';
  document.getElementById('gestWeeks').value = '';
  document.getElementById('bilirubin').value = '';
  document.getElementById('babyName').value = '';
  document.getElementById('hospitalNo').value = '';
  document.getElementById('resultCard').style.display='none';
  document.getElementById('emptyHint').style.display='block';
  document.getElementById('resultTitle').textContent = 'No data yet';
  document.getElementById('showHours').textContent = '—';
  document.getElementById('showBili').textContent = '— µmol/L';
  document.getElementById('showGest').textContent = '— wk';
  document.getElementById('interp').style.display='none';
  document.getElementById('graphCard').style.display='none';
  
  // Destroy chart if exists
  if (bilirubinChart) {
    bilirubinChart.destroy();
    bilirubinChart = null;
  }
}
document.getElementById('resetBtn').addEventListener('click', (e)=>{
  resetUI();
});
document.getElementById('calcBtn').addEventListener('click', (e)=>{
  e.preventDefault();
  const dobVal = document.getElementById('dob').value;
  const manualHoursVal = document.getElementById('ageHours').value;
  let hours = null;
  if (dobVal) {
    const dob = new Date(dobVal);
    const now = new Date();
    const ms = now - dob;
    hours = ms / (1000*60*60);
    if (isNaN(hours) || hours < 0) {
      alert('Invalid date/time of birth. Check format/timezone.');
      return;
    }
  }
  if (manualHoursVal !== '') {
    const parsed = parseFloat(manualHoursVal);
    if (!isNaN(parsed)) hours = parsed;
  }
  if (hours === null) { alert('Please provide either Date/time of birth or manual age (hours).'); return; }
  const gest = parseFloat(document.getElementById('gestWeeks').value) || null;
  if (gest === null) { alert('Please enter gestational age in weeks.'); return; }
  const biliVal = document.getElementById('bilirubin').value;
  if (biliVal === '' || isNaN(parseFloat(biliVal))) { alert('Please enter a bilirubin value (mg/dL).'); return; }
  const biliMgDl = parseFloat(biliVal);
  const biliUmolL = toUmolL(biliMgDl); // Convert for calculations and display
  // Get thresholds (in µmol/L)
  const thresh = findThresholdsByHours(hours, gest);
  // Show values - entered bilirubin displayed in both units
  document.getElementById('resultCard').style.display='block';
  document.getElementById('emptyHint').style.display='none';
  document.getElementById('showHours').textContent = `${hours.toFixed(1)} h`;
  document.getElementById('showBili').innerHTML = `${biliUmolL.toFixed(1)} µmol/L <span class="small-muted">(${biliMgDl.toFixed(2)} mg/dL)</span>`;
  document.getElementById('showGest').textContent = `${gest.toFixed(1)} wk`;
  document.getElementById('ptThresh').textContent = `${thresh.pt.toFixed(1)} µmol/L`;
  document.getElementById('exThresh').textContent = `${thresh.ex.toFixed(1)} µmol/L`;
  document.getElementById('interp').textContent = thresh.interp;
  const showInterp = document.getElementById('showInterpolation').checked;
  document.getElementById('interp').style.display = showInterp ? 'block' : 'none';
  // Recommendation logic - compare in µmol/L
  let recText = 'No treatment indicated based on thresholds';
  let badgeClass = 'ok';
  if (biliUmolL >= thresh.ex) {
    recText = 'Exchange transfusion recommended';
    badgeClass = 'danger';
  } else if (biliUmolL >= thresh.pt) {
    recText = 'Phototherapy recommended';
    badgeClass = 'warn';
  } else {
    recText = 'No phototherapy/exchange indicated';
    badgeClass = 'ok';
  }
  const rf = document.getElementById('riskFactors').value;
  if (rf !== 'none') {
    recText += ' — consider earlier intervention due to risk factor: ' + rf;
  }
  const recBadge = document.getElementById('recBadge');
  recBadge.innerHTML = '';
  const span = document.createElement('span');
  span.className = 'badge ' + (badgeClass === 'ok' ? 'ok' : (badgeClass === 'warn' ? 'warn' : 'danger'));
  span.textContent = recText;
  recBadge.appendChild(span);
  document.getElementById('resultTitle').textContent = recText.split(' —')[0];
  
  // Show graph card
  document.getElementById('graphCard').style.display = 'block';
  
  // Prepare data for the chart
  const ptData = [];
  const exData = [];
  
  // Determine which table to use based on gestational age
  let tableKey;
  if (gest < 23) {
    tableKey = 23; // Use 23 weeks as minimum
  } else if (gest >= 38) {
    tableKey = 38; // Use 38 weeks for 38+ weeks
  } else {
    // Round down to nearest integer week
    tableKey = Math.floor(gest);
  }
  
  // Generate data points for the chart
  for (let i = 0; i <= 336; i += 6) { // Every 6 hours to reduce points
    const thresholds = findThresholdsByHours(i, gest);
    ptData.push({x: i, y: thresholds.pt});
    exData.push({x: i, y: thresholds.ex});
  }
  
  // Get the current bilirubin point
  const currentPoint = {
    x: hours,
    y: biliUmolL
  };
  
  // Destroy previous chart if exists
  if (bilirubinChart) {
    bilirubinChart.destroy();
  }
  
  // Create the chart
  const ctx = document.getElementById('bilirubinChart').getContext('2d');
  bilirubinChart = new Chart(ctx, {
    type: 'line',
    data: {
      datasets: [
        {
          label: 'Phototherapy Threshold',
          data: ptData,
          borderColor: 'rgba(250, 204, 21, 1)',
          backgroundColor: 'rgba(250, 204, 21, 0.1)',
          borderWidth: 2,
          fill: false,
          tension: 0.4,
          pointRadius: 0,
          pointHoverRadius: 4
        },
        {
          label: 'Exchange Transfusion Threshold',
          data: exData,
          borderColor: 'rgba(244, 63, 94, 1)',
          backgroundColor: 'rgba(244, 63, 94, 0.1)',
          borderWidth: 2,
          fill: false,
          tension: 0.4,
          pointRadius: 0,
          pointHoverRadius: 4
        },
        {
          label: 'Current Bilirubin',
          data: [currentPoint],
          backgroundColor: 'rgba(34, 197, 94, 1)',
          borderColor: 'rgba(34, 197, 94, 1)',
          borderWidth: 2,
          pointRadius: 4,  // Reduced from 8 to 4 (half the size)
          pointHoverRadius: 5,  // Reduced from 10 to 5
          showLine: false
        }
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        x: {
          type: 'linear',
          title: {
            display: true,
            text: 'Age (hours)',
            color: '#e6eef8',
            font: {
              size: 14
            }
          },
          ticks: {
            color: '#93b7d8',
            maxRotation: 0,
            autoSkip: true,
            maxTicksLimit: 10
          },
          grid: {
            color: 'rgba(255, 255, 255, 0.05)'
          },
          min: 0,
          max: 336
        },
        y: {
          title: {
            display: true,
            text: 'Bilirubin (µmol/L)',
            color: '#e6eef8',
            font: {
              size: 14
            }
          },
          ticks: {
            color: '#93b7d8'
          },
          grid: {
            color: 'rgba(255, 255, 255, 0.05)'
          },
          min: 0,
          max: 500
        }
      },
      plugins: {
        legend: {
          labels: {
            color: '#e6eef8',
            font: {
              size: 12
            }
          }
        },
        tooltip: {
          backgroundColor: 'rgba(15, 23, 42, 0.9)',
          titleColor: '#e6eef8',
          bodyColor: '#cfe7ff',
          borderColor: 'rgba(255, 255, 255, 0.1)',
          borderWidth: 1,
          padding: 10,
          displayColors: true,
          callbacks: {
            label: function(context) {
              let label = context.dataset.label || '';
              if (label) {
                label += ': ';
              }
              if (context.parsed.y !== null) {
                label += context.parsed.y.toFixed(1) + ' µmol/L';
              }
              if (context.parsed.x !== null) {
                label += ' at ' + context.parsed.x + 'h';
              }
              return label;
            }
          }
        }
      }
    }
  });
});
resetUI();
</script>
</body>
</html>
