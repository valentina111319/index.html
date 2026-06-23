<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Comparador de Precios — Neumáticos</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700;800&family=Barlow:wght@400;500;600&display=swap" rel="stylesheet" />
  <style>
    :root {
      --brand: #E63B2E;
      --brand-dark: #B52D22;
      --surface: #1A1A1A;
      --surface-2: #242424;
      --surface-3: #2E2E2E;
      --border: #3A3A3A;
      --text: #F0EDE8;
      --muted: #8A8580;
      --green: #2ECC71;
      --amber: #F39C12;
    }
    * { box-sizing: border-box; }
    body {
      background: var(--surface);
      color: var(--text);
      font-family: 'Barlow', sans-serif;
      min-height: 100vh;
    }
    h1, h2, h3, .condensed { font-family: 'Barlow Condensed', sans-serif; }

    .btn-primary {
      background: var(--brand);
      color: white;
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 700;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      border: none;
      cursor: pointer;
      transition: background 0.15s, transform 0.1s;
    }
    .btn-primary:hover { background: var(--brand-dark); }
    .btn-primary:active { transform: scale(0.98); }
    .btn-primary:disabled { background: #555; cursor: not-allowed; opacity: 0.6; }

    .card {
      background: var(--surface-2);
      border: 1px solid var(--border);
      border-radius: 8px;
    }
    .card-header {
      background: var(--surface-3);
      border-bottom: 1px solid var(--border);
      border-radius: 8px 8px 0 0;
      padding: 12px 16px;
    }
    input[type="text"], input[type="url"] {
      background: var(--surface-3);
      border: 1px solid var(--border);
      border-radius: 6px;
      color: var(--text);
      padding: 10px 14px;
      font-family: 'Barlow', sans-serif;
      font-size: 15px;
      width: 100%;
      transition: border-color 0.15s;
      outline: none;
    }
    input[type="text"]:focus, input[type="url"]:focus {
      border-color: var(--brand);
      box-shadow: 0 0 0 2px rgba(230,59,46,0.15);
    }
    input::placeholder { color: var(--muted); }

    .tag {
      display: inline-block;
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 600;
      font-size: 11px;
      letter-spacing: 0.1em;
      text-transform: uppercase;
      padding: 2px 8px;
      border-radius: 4px;
    }
    .tag-red { background: rgba(230,59,46,0.15); color: #F07060; border: 1px solid rgba(230,59,46,0.3); }
    .tag-green { background: rgba(46,204,113,0.15); color: #5DD990; border: 1px solid rgba(46,204,113,0.3); }
    .tag-amber { background: rgba(243,156,18,0.15); color: #F5B93A; border: 1px solid rgba(243,156,18,0.3); }

    .price-badge {
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 800;
      font-size: 28px;
      letter-spacing: -0.02em;
    }
    .price-badge.best { color: var(--green); }
    .price-badge.mid  { color: var(--amber); }
    .price-badge.high { color: #F07060; }

    .progress-bar {
      height: 6px;
      background: var(--border);
      border-radius: 3px;
      overflow: hidden;
      margin-top: 8px;
    }
    .progress-fill {
      height: 100%;
      border-radius: 3px;
      transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .spinner {
      width: 18px; height: 18px;
      border: 2px solid rgba(255,255,255,0.2);
      border-top-color: white;
      border-radius: 50%;
      animation: spin 0.7s linear infinite;
      display: inline-block;
      vertical-align: middle;
      margin-right: 8px;
    }
    @keyframes spin { to { transform: rotate(360deg); } }

    .fade-in { animation: fadeIn 0.4s ease; }
    @keyframes fadeIn { from { opacity:0; transform: translateY(8px); } to { opacity:1; transform: translateY(0); } }

    .site-row {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px 0;
      border-bottom: 1px solid var(--border);
    }
    .site-row:last-child { border-bottom: none; }
    .site-num {
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 800;
      font-size: 22px;
      color: var(--brand);
      width: 28px;
      flex-shrink: 0;
    }

    .result-card {
      border: 1px solid var(--border);
      border-radius: 8px;
      overflow: hidden;
      transition: border-color 0.2s;
    }
    .result-card.best-price { border-color: var(--green); }
    .result-card.high-price { border-color: #F07060; }

    .winner-banner {
      background: rgba(46,204,113,0.12);
      border-bottom: 1px solid rgba(46,204,113,0.3);
      padding: 6px 16px;
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 700;
      font-size: 12px;
      letter-spacing: 0.1em;
      text-transform: uppercase;
      color: #5DD990;
    }

    .diff-badge {
      font-size: 13px;
      font-weight: 600;
      padding: 3px 8px;
      border-radius: 4px;
    }
    .diff-pos { background: rgba(46,204,113,0.12); color: #5DD990; }
    .diff-neg { background: rgba(240,112,96,0.12); color: #F07060; }

    @media (max-width: 640px) {
      .price-badge { font-size: 22px; }
    }

    .howto-step {
      display: flex;
      gap: 14px;
      align-items: flex-start;
      padding: 14px 0;
      border-bottom: 1px solid var(--border);
    }
    .howto-step:last-child { border-bottom: none; }
    .step-num {
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 800;
      font-size: 32px;
      line-height: 1;
      color: var(--brand);
      width: 36px;
      flex-shrink: 0;
    }

    .accordion-header {
      cursor: pointer;
      user-select: none;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 14px 16px;
      background: var(--surface-3);
      border-radius: 6px;
      border: 1px solid var(--border);
      font-family: 'Barlow Condensed', sans-serif;
      font-weight: 700;
      font-size: 15px;
      letter-spacing: 0.04em;
      text-transform: uppercase;
    }
    .accordion-header:hover { border-color: var(--brand); }

    .error-box {
      background: rgba(230,59,46,0.1);
      border: 1px solid rgba(230,59,46,0.35);
      border-radius: 6px;
      padding: 12px 16px;
      color: #F07060;
      font-size: 14px;
    }
  </style>
</head>
<body>

<!-- ═══════════════════════════ HEADER ═══════════════════════════ -->
<header style="background:#111; border-bottom:2px solid var(--brand);">
  <div class="max-w-5xl mx-auto px-4 py-5 flex items-center justify-between flex-wrap gap-3">
    <div class="flex items-center gap-3">
      <img src="data:image/png;base64,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" alt="Gomería Central" style="height:44px; width:44px; object-fit:contain; border-radius:6px;" />
      <div>
        <h1 class="condensed text-white" style="font-size:22px; font-weight:800; line-height:1; letter-spacing:0.02em;">ANÁLISIS DE COMPETENCIA</h1>
        <p style="font-size:12px; color:var(--muted); letter-spacing:0.08em;">COMPARADOR DE PRECIOS · GOMERÍA CENTRAL</p>
      </div>
    </div>
    <span class="tag tag-red">Beta</span>
  </div>
</header>

<!-- ═══════════════════════════ INSTRUCCIONES ═══════════════════════════ -->
<div class="max-w-5xl mx-auto px-4 pt-5 pb-2" id="instrucciones-section">
  <div class="accordion-header" id="toggle-instrucciones" onclick="toggleInstrucciones()">
    <span>📋 Instrucciones de uso</span>
    <span id="arrow-icon" style="transition:transform 0.2s; font-size:18px;">▼</span>
  </div>
  <div id="instrucciones-body" class="card mt-1" style="display:none;">
    <div class="p-4">
      <p style="font-size:14px; color:var(--muted); margin-bottom:14px;">
        Esta herramienta usa inteligencia artificial para buscar y comparar precios de neumáticos en sitios web de Argentina.
      </p>
      <div class="howto-step">
        <div class="step-num">1</div>
        <div>
          <p style="font-weight:600; font-size:15px; margin-bottom:4px;">Ingresá tu clave API de Anthropic</p>
          <p style="font-size:14px; color:var(--muted);">En el campo "API Key", pegá tu clave que empieza con <code style="background:var(--surface-3); padding:1px 5px; border-radius:3px;">sk-ant-...</code>. 
          <br>Si no tenés una: entrá a <a href="https://console.anthropic.com" target="_blank" style="color:var(--brand); text-decoration:underline;">console.anthropic.com</a> → creá una cuenta → API Keys → Create Key. 
          <br><strong>Tu clave no se guarda ni se envía a ningún otro servidor.</strong></p>
        </div>
      </div>
      <div class="howto-step">
        <div class="step-num">2</div>
        <div>
          <p style="font-weight:600; font-size:15px; margin-bottom:4px;">Escribí el producto</p>
          <p style="font-size:14px; color:var(--muted);">En "Producto a buscar" escribí el neumático que querés comparar. Ejemplo:<br><code style="background:var(--surface-3); padding:1px 5px; border-radius:3px;">Neumático 185/60 R15 APTANY RP203 84H</code></p>
        </div>
      </div>
      <div class="howto-step">
        <div class="step-num">3</div>
        <div>
          <p style="font-weight:600; font-size:15px; margin-bottom:4px;">Ingresá hasta 5 sitios web</p>
          <p style="font-size:14px; color:var(--muted);">Pegá las URLs de los sitios de la competencia. Podés poner la URL de la página de inicio o de búsqueda del producto. Ejemplo:<br>
          <code style="background:var(--surface-3); padding:1px 5px; border-radius:3px;">https://neumaticosmartin.com.ar</code><br>
          <code style="background:var(--surface-3); padding:1px 5px; border-radius:3px;">https://gomeriacentral.com</code></p>
        </div>
      </div>
      <div class="howto-step">
        <div class="step-num">4</div>
        <div>
          <p style="font-weight:600; font-size:15px; margin-bottom:4px;">Hacé clic en "Comparar precios"</p>
          <p style="font-size:14px; color:var(--muted);">La IA buscará el producto en cada sitio en tiempo real y te devolverá un cuadro comparativo con los precios, el más barato destacado y el porcentaje de diferencia entre los sitios.</p>
        </div>
      </div>
      <div style="background:rgba(243,156,18,0.1); border:1px solid rgba(243,156,18,0.3); border-radius:6px; padding:10px 14px; margin-top:8px;">
        <p style="font-size:13px; color:#F5B93A;"><strong>⚠ Importante:</strong> La herramienta usa Claude (IA de Anthropic) para analizar los sitios. Los precios pueden no estar actualizados al minuto — siempre confirmá en el sitio del vendedor antes de comprar. El uso de la API tiene un costo bajo por consulta (aprox. $0.01 USD por búsqueda).</p>
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════ FORMULARIO ═══════════════════════════ -->
<main class="max-w-5xl mx-auto px-4 py-4">

  <!-- API Key -->
  <div class="card mb-4">
    <div class="card-header flex items-center gap-2">
      <svg width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="#E63B2E" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M15 7a2 2 0 012 2m4 0a6 6 0 01-7.743 5.743L11 17H9v2H7v2H4a1 1 0 01-1-1v-2.586a1 1 0 01.293-.707l5.964-5.964A6 6 0 1121 9z"/></svg>
      <span class="condensed font-bold uppercase tracking-wider text-sm">Clave API de Anthropic</span>
    </div>
    <div class="p-4">
      <div class="flex gap-2">
        <input type="password" id="apiKey" placeholder="sk-ant-api03-..." autocomplete="off" style="flex:1;" />
        <button onclick="toggleApiKeyVisibility()" style="background:var(--surface-3); border:1px solid var(--border); border-radius:6px; padding:0 12px; color:var(--muted); cursor:pointer; font-size:18px; flex-shrink:0;" title="Mostrar/ocultar clave">👁</button>
      </div>
      <p style="font-size:12px; color:var(--muted); margin-top:6px;">La clave solo se usa en tu navegador. No se almacena ni se envía a terceros.</p>
    </div>
  </div>

  <div class="grid gap-4 md:grid-cols-2">
    <!-- Medida -->
    <div class="card">
      <div class="card-header flex items-center gap-2">
        <svg width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="#E63B2E" stroke-width="2.5"><circle cx="11" cy="11" r="8"/><path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-4.35-4.35"/></svg>
        <span class="condensed font-bold uppercase tracking-wider text-sm">Medida del neumático</span>
      </div>
      <div class="p-4">
        <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap;margin-bottom:10px;">
          <div style="flex:1;min-width:60px;">
            <label style="font-size:10px;color:var(--muted);letter-spacing:0.06em;text-transform:uppercase;display:block;margin-bottom:4px;">Ancho</label>
            <input type="text" id="med-ancho" placeholder="185" maxlength="3" style="text-align:center;font-size:18px;font-weight:700;" oninput="actualizarMedida()" />
          </div>
          <span style="font-size:28px;color:var(--muted);font-weight:300;padding-top:18px;">/</span>
          <div style="flex:1;min-width:60px;">
            <label style="font-size:10px;color:var(--muted);letter-spacing:0.06em;text-transform:uppercase;display:block;margin-bottom:4px;">Perfil</label>
            <input type="text" id="med-perfil" placeholder="60" maxlength="2" style="text-align:center;font-size:18px;font-weight:700;" oninput="actualizarMedida()" />
          </div>
          <span style="font-size:22px;color:var(--muted);font-weight:300;padding-top:18px;">R</span>
          <div style="flex:1;min-width:60px;">
            <label style="font-size:10px;color:var(--muted);letter-spacing:0.06em;text-transform:uppercase;display:block;margin-bottom:4px;">Rodado</label>
            <input type="text" id="med-rodado" placeholder="15" maxlength="2" style="text-align:center;font-size:18px;font-weight:700;" oninput="actualizarMedida()" />
          </div>
          <div style="flex:2;min-width:120px;">
            <label style="font-size:10px;color:var(--muted);letter-spacing:0.06em;text-transform:uppercase;display:block;margin-bottom:4px;">Medida completa</label>
            <input type="text" id="producto" placeholder="185/60 R15" readonly style="background:var(--surface);font-size:16px;font-weight:700;color:var(--brand);cursor:default;" />
          </div>
        </div>
        <div class="flex flex-wrap gap-2 mt-2">
          <span style="font-size:12px; color:var(--muted);">Medidas frecuentes:</span>
          <button onclick="setMedida('185','60','15')" style="background:var(--surface-3); border:1px solid var(--border); border-radius:4px; padding:2px 8px; font-size:12px; color:var(--muted); cursor:pointer;">185/60 R15</button>
          <button onclick="setMedida('205','55','16')" style="background:var(--surface-3); border:1px solid var(--border); border-radius:4px; padding:2px 8px; font-size:12px; color:var(--muted); cursor:pointer;">205/55 R16</button>
          <button onclick="setMedida('225','45','17')" style="background:var(--surface-3); border:1px solid var(--border); border-radius:4px; padding:2px 8px; font-size:12px; color:var(--muted); cursor:pointer;">225/45 R17</button>
          <button onclick="setMedida('175','65','14')" style="background:var(--surface-3); border:1px solid var(--border); border-radius:4px; padding:2px 8px; font-size:12px; color:var(--muted); cursor:pointer;">175/65 R14</button>
          <button onclick="setMedida('195','65','15')" style="background:var(--surface-3); border:1px solid var(--border); border-radius:4px; padding:2px 8px; font-size:12px; color:var(--muted); cursor:pointer;">195/65 R15</button>
        </div>
      </div>
    </div>

    <!-- Sitios -->
    <div class="card">
      <div class="card-header flex items-center gap-2">
        <svg width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="#E63B2E" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 01-9 9m9-9a9 9 0 00-9-9m9 9H3m9 9a9 9 0 01-9-9m9 9c1.657 0 3-4.03 3-9s-1.343-9-3-9m0 18c-1.657 0-3-4.03-3-9s1.343-9 3-9"/></svg>
        <span class="condensed font-bold uppercase tracking-wider text-sm">Sitios a comparar (hasta 5)</span>
      </div>
      <div class="p-4">
        <div id="sites-container">
          <div class="site-row">
            <span class="site-num">1</span>
            <input type="url" class="site-url" placeholder="https://sitio1.com.ar" />
          </div>
          <div class="site-row">
            <span class="site-num">2</span>
            <input type="url" class="site-url" placeholder="https://sitio2.com.ar (opcional)" />
          </div>
          <div class="site-row">
            <span class="site-num">3</span>
            <input type="url" class="site-url" placeholder="https://sitio3.com.ar (opcional)" />
          </div>
          <div class="site-row">
            <span class="site-num">4</span>
            <input type="url" class="site-url" placeholder="https://sitio4.com.ar (opcional)" />
          </div>
          <div class="site-row">
            <span class="site-num">5</span>
            <input type="url" class="site-url" placeholder="https://sitio5.com.ar (opcional)" />
          </div>
        </div>
        <p style="font-size:12px; color:var(--muted); margin-top:8px;">Podés poner la URL raíz del sitio o una búsqueda específica.</p>
      </div>
    </div>
  </div>

  <!-- Botón principal -->
  <div class="mt-5">
    <button id="btn-comparar" class="btn-primary w-full py-4 rounded-lg text-lg flex items-center justify-center gap-2" onclick="compararPrecios()">
      <svg width="20" height="20" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/></svg>
      Comparar precios
    </button>
  </div>

  <!-- Error global -->
  <div id="error-global" class="error-box mt-4" style="display:none;"></div>

  <!-- ═══ RESULTADOS ═══ -->
  <div id="resultados" class="mt-6" style="display:none;"></div>

  <!-- Botón PDF -->
  <div id="btn-pdf-wrap" class="mt-4" style="display:none;">
    <button onclick="exportarPDF()" style="background:#1A1A1A; border:1px solid var(--border); color:var(--muted); font-family:'Barlow Condensed',sans-serif; font-weight:700; letter-spacing:0.08em; text-transform:uppercase; cursor:pointer; border-radius:8px; padding:12px 24px; font-size:15px; display:flex; align-items:center; gap:8px; transition:border-color 0.15s;" onmouseover="this.style.borderColor='var(--brand)';this.style.color='var(--text)'" onmouseout="this.style.borderColor='var(--border)';this.style.color='var(--muted)'">
      <svg width="18" height="18" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/></svg>
      Descargar resultados en PDF
    </button>
  </div>

</main>

<!-- ═══════════════════════════ FOOTER ═══════════════════════════ -->
<footer class="mt-10 py-6 border-t" style="border-color:var(--border);">
  <div class="max-w-5xl mx-auto px-4 text-center" style="font-size:12px; color:var(--muted);">
    Comparador de Neumáticos · Desarrollado con Claude AI (Anthropic) · Solo para uso interno en Argentina
  </div>
</footer>

<script>
// ──────────────────────────────────────────────
// Utilidades
// ──────────────────────────────────────────────
function toggleInstrucciones() {
  const body  = document.getElementById('instrucciones-body');
  const arrow = document.getElementById('arrow-icon');
  const open  = body.style.display !== 'none';
  body.style.display  = open ? 'none' : 'block';
  arrow.style.transform = open ? '' : 'rotate(180deg)';
}

function toggleApiKeyVisibility() {
  const input = document.getElementById('apiKey');
  input.type  = input.type === 'password' ? 'text' : 'password';
}

// Actualiza el campo de medida completa al escribir en los 3 campos
function actualizarMedida() {
  const a = document.getElementById('med-ancho').value.trim();
  const p = document.getElementById('med-perfil').value.trim();
  const r = document.getElementById('med-rodado').value.trim();
  let medida = '';
  if (a) medida += a;
  if (a && p) medida += '/' + p;
  if (a && p && r) medida += ' R' + r;
  document.getElementById('producto').value = medida;
}

function setMedida(ancho, perfil, rodado) {
  document.getElementById('med-ancho').value  = ancho;
  document.getElementById('med-perfil').value = perfil;
  document.getElementById('med-rodado').value = rodado;
  actualizarMedida();
}

// Compatibilidad con código viejo
function setProducto(val) {
  const m = val.trim().match(/(\d{3})\s*\/\s*(\d{2})\s*R\s*(\d{2})/i);
  if (m) { setMedida(m[1], m[2], m[3]); } else { document.getElementById('producto').value = val; }
}

function formatPrice(num) {
  if (!num || isNaN(num)) return null;
  return new Intl.NumberFormat('es-AR', { style: 'currency', currency: 'ARS', maximumFractionDigits: 0 }).format(num);
}

function extractNumber(val) {
  if (val === null || val === undefined) return null;
  // Si ya es número, validarlo directamente
  if (typeof val === 'number') return (isFinite(val) && val > 100) ? val : null;
  const str = String(val).trim();
  // Quitar símbolo $ y espacios
  let clean = str.replace(/\$/g, '').replace(/\s/g, '');
  // Formato argentino: punto = miles, coma = decimal → "121.026,24" → 121026.24
  // Formato inglés:   coma = miles, punto = decimal → "121,026.24" → 121026.24
  // Detectar cuál es cuál según la posición del último separador
  const lastDot   = clean.lastIndexOf('.');
  const lastComma = clean.lastIndexOf(',');
  if (lastComma > lastDot) {
    // Coma es decimal (formato AR): quitar puntos de miles, reemplazar coma por punto
    clean = clean.replace(/\./g, '').replace(',', '.');
  } else if (lastDot > lastComma) {
    // Punto es decimal (formato EN): quitar comas de miles
    clean = clean.replace(/,/g, '');
  } else {
    // Sin separador decimal: quitar todo lo que no sea dígito
    clean = clean.replace(/[.,]/g, '');
  }
  const num = parseFloat(clean);
  // Sanity check: precios de neumáticos en AR están entre $10.000 y $2.000.000
  if (isNaN(num) || num < 10000 || num > 2000000) return null;
  return Math.round(num); // siempre entero
}

// ──────────────────────────────────────────────
// Llamada a Claude API
// ──────────────────────────────────────────────
// ──────────────────────────────────────────────
// Scraping directo del sitio web
// ──────────────────────────────────────────────
// ──────────────────────────────────────────────
// Scraping real del sitio via proxy CORS
// ──────────────────────────────────────────────
async function scrapeSite(medida, siteUrl) {
  const m = medida.trim().match(/(\d{3})\s*\/\s*(\d{2})\s*R\s*(\d{2})/i);
  if (!m) return null;
  const ancho = m[1], perfil = m[2], rodado = m[3];

  const base = (siteUrl.startsWith('http') ? siteUrl : 'https://'+siteUrl).replace(/\/$/, '');

  // Términos de búsqueda con variantes de formato
  const term1 = `${ancho}/${perfil}R${rodado}`;
  const term2 = `${ancho}/${perfil} R${rodado}`;

  // URLs de búsqueda comunes en e-commerce argentinos
  const searchURLs = [
    `${base}/?s=${encodeURIComponent(term1)}`,
    `${base}/?s=${encodeURIComponent(term2)}`,
    `${base}/search?q=${encodeURIComponent(term1)}`,
    `${base}/busqueda?q=${encodeURIComponent(term1)}`,
    `${base}/productos?search=${encodeURIComponent(term1)}`,
    `${base}/tienda?s=${encodeURIComponent(term1)}`,
  ];

  // Proxies vigentes (2026), cada uno con su forma de devolver el contenido
  const proxies = [
    { build: url => `https://api.codetabs.com/v1/proxy?quest=${encodeURIComponent(url)}`, type: 'text' },
    { build: url => `https://api.allorigins.win/raw?url=${encodeURIComponent(url)}`, type: 'text' },
    { build: url => `https://corsproxy.io/?url=${encodeURIComponent(url)}`, type: 'text' },
    { build: url => `https://cors.x2u.in/${url}`, type: 'text' },
  ];

  for (const searchUrl of searchURLs) {
    for (const proxy of proxies) {
      try {
        const proxyUrl = proxy.build(searchUrl);
        const resp = await fetch(proxyUrl, { signal: AbortSignal.timeout(8000) });
        if (!resp.ok) { console.warn(`Scrape: proxy status ${resp.status} →`, proxyUrl.slice(0,60)); continue; }
        const html = await resp.text();
        if (!html || html.length < 300) { console.warn('Scrape: HTML muy corto o vacío desde', proxyUrl.slice(0,60)); continue; }

        const result = extractPricesFromHTML(html, ancho, perfil, rodado, searchUrl);
        if (result.precioMinimo) {
          console.info(`✓ Scraping OK para ${siteUrl} via ${proxyUrl.slice(0,40)} — precio: ${result.precioMinimo}`);
          return result;
        }
      } catch(e) { console.warn('Scrape proxy error:', e.message, proxy.build(searchUrl).slice(0,60)); continue; }
    }
  }
  console.warn(`✗ Scraping sin éxito para ${siteUrl} — se usará Claude con web_search como fallback`);
  return null;
}

function extractPricesFromHTML(html, ancho, perfil, rodado, urlUsada) {
  // Limpiar HTML a texto plano
  const text = html
    .replace(/<script[\s\S]*?<\/script>/gi, ' ')
    .replace(/<style[\s\S]*?<\/style>/gi, ' ')
    .replace(/<!--[\s\S]*?-->/g, ' ')
    .replace(/<[^>]+>/g, ' ')
    .replace(/&nbsp;/gi, ' ').replace(/&amp;/gi, '&')
    .replace(/&lt;/gi, '<').replace(/&gt;/gi, '>')
    .replace(/&#\d+;/g, ' ')
    .replace(/\s+/g, ' ').trim();

  // Patrones de la medida (varias variantes)
  const medidaRe = new RegExp(
    `${ancho}\\s*[/\\-]\\s*${perfil}\\s*[Rr]\\s*${rodado}`, 'g'
  );

  // Regex de precios en formato argentino
  const precioRe = /\$\s*(\d{1,3}(?:[.,]\d{3})*(?:[.,]\d{1,2})?|\d{5,7})/g;

  // Palabras clave que indican SIN stock
  const sinStockRe = /(sin\s*stock|agotado|no\s*disponible|out\s*of\s*stock|sold\s*out|consultar\s*disponibilidad|próximamente|sin\s*existencia)/i;
  // Palabras clave que indican CON stock
  const conStockRe  = /(en\s*stock|disponible|agregar\s*al\s*carrito|añadir\s*al\s*carrito|add\s*to\s*cart|comprar\s*ahora|en\s*existencia)/i;

  const preciosConStock    = [];
  const preciosSinStock    = [];
  const preciosSinInfo     = [];
  const todosPreciosValidos = [];
  const stockSnippets       = [];

  // Buscar precios en un radio alrededor de cada mención de la medida
  let matchMedida;
  while ((matchMedida = medidaRe.exec(text)) !== null) {
    const start = Math.max(0, matchMedida.index - 150);
    const end   = Math.min(text.length, matchMedida.index + 400);
    const fragment = text.slice(start, end);

    const tieneSinStock = sinStockRe.test(fragment);
    const tieneConStock = conStockRe.test(fragment);

    if (tieneSinStock || tieneConStock) {
      stockSnippets.push(fragment.slice(0, 120).trim());
    }

    let pm;
    const reLocal = new RegExp(precioRe.source, 'g');
    while ((pm = reLocal.exec(fragment)) !== null) {
      const n = parsePrecioAR(pm[0]);
      if (!n) continue;
      if (tieneSinStock && !tieneConStock) {
        preciosSinStock.push(n);
      } else if (tieneConStock) {
        preciosConStock.push(n);
      } else {
        preciosSinInfo.push(n);
      }
    }
  }

  // También recolectar todos los precios de la página para contexto
  let pm2;
  precioRe.lastIndex = 0;
  while ((pm2 = precioRe.exec(text)) !== null) {
    const n = parsePrecioAR(pm2[0]);
    if (n) todosPreciosValidos.push(n);
  }

  // Prioridad: precios CON stock confirmado > precios sin info de stock > precios SIN stock (último recurso)
  let preciosCercanos, fuenteStock;
  if (preciosConStock.length > 0) {
    preciosCercanos = preciosConStock;
    fuenteStock = 'disponible';
  } else if (preciosSinInfo.length > 0) {
    preciosCercanos = preciosSinInfo;
    fuenteStock = 'desconocido';
  } else if (preciosSinStock.length > 0) {
    preciosCercanos = preciosSinStock;
    fuenteStock = 'no disponible';
  } else {
    preciosCercanos = [];
    fuenteStock = 'desconocido';
  }

  const lista = preciosCercanos.length > 0 ? preciosCercanos : todosPreciosValidos;
  const unicos = [...new Set(lista)].sort((a,b) => a-b);

  return {
    precioMinimo: unicos[0] || null,
    disponibilidadDetectada: fuenteStock,
    todosPreciosValidos: [...new Set(todosPreciosValidos)].sort((a,b)=>a-b).slice(0, 15),
    preciosCercanos: unicos,
    stockSnippets,
    urlUsada,
    fuente: 'scraping'
  };
}

function parsePrecioAR(str) {
  // Limpia $ y espacios
  let s = str.replace(/\$/g,'').replace(/\s/g,'').trim();
  // Formato AR: 68.500 o 68.500,00 → punto=miles, coma=decimal
  // Formato EN: 68,500 o 68,500.00 → coma=miles, punto=decimal
  const lastDot   = s.lastIndexOf('.');
  const lastComma = s.lastIndexOf(',');
  if (lastComma > lastDot) {
    // coma es decimal → AR format
    s = s.replace(/\./g,'').replace(',','.');
  } else if (lastDot > lastComma) {
    // punto es decimal → EN format
    s = s.replace(/,/g,'');
  } else {
    s = s.replace(/[.,]/g,'');
  }
  const n = Math.round(parseFloat(s));
  return (isFinite(n) && n >= 10000 && n <= 2000000) ? n : null;
}

// ──────────────────────────────────────────────
// Claude analiza el HTML scrapeado (SIN web_search)
// Más determinístico: siempre analiza el mismo texto
// ──────────────────────────────────────────────
async function fetchPriceFromClaude(apiKey, medida, siteUrl, scrapedData) {
  let hostname = siteUrl;
  try { hostname = new URL(siteUrl.startsWith('http') ? siteUrl : 'https://'+siteUrl).hostname.replace('www.',''); } catch {}

  const m = medida.trim().match(/(\d{3})\s*\/\s*(\d{2})\s*R\s*(\d{2})/i);

  // CASO 1: Tenemos datos de scraping → Claude interpreta, sin web_search
  if (scrapedData?.precioMinimo) {
    const preciosCercanos = scrapedData.preciosCercanos?.length > 0
      ? scrapedData.preciosCercanos.map(formatPrice).join(', ')
      : 'ninguno';
    const todosPreciosPag = scrapedData.todosPreciosValidos?.slice(0,10).map(formatPrice).join(', ') || 'ninguno';
    const stockInfo = scrapedData.stockSnippets?.length > 0
      ? scrapedData.stockSnippets.slice(0,5).join(' | ')
      : 'sin información de stock detectada';

    const prompt = `Analizá esta información de precios obtenida del sitio ${hostname} para neumáticos con medida ${medida}.

PRECIOS ENCONTRADOS CERCA DE LA MEDIDA EN LA PÁGINA: ${preciosCercanos}
TODOS LOS PRECIOS EN LA PÁGINA: ${todosPreciosPag}
FRAGMENTOS DE TEXTO SOBRE STOCK/DISPONIBILIDAD CERCA DE LA MEDIDA: ${stockInfo}
URL ANALIZADA: ${scrapedData.urlUsada}

Tu tarea:
- Determiná cuál es el precio MÁS BAJO real para la medida ${medida} EN STOCK en este sitio
- Si los fragmentos de stock mencionan "sin stock", "agotado", "no disponible" cerca de un precio, ESE precio no cuenta como disponible — preferí otro precio si lo hay
- Si TODOS los precios encontrados parecen sin stock, usá el más bajo igual pero marcá disponibilidad "no disponible" y confianza "media"
- Descartá precios que claramente no correspondan a neumáticos (servicios, envío, etc.)
- Precio válido: entre $30.000 y $500.000 para esta medida
- Nombre del sitio: deducilo del hostname "${hostname}"

Respondé SOLO con este JSON exacto, sin texto extra ni markdown:
{"sitio":"nombre comercial del sitio","url":"${siteUrl}","precio":NUMERO_ENTERO,"precio_formateado":"$XX.XXX","disponibilidad":"disponible|no disponible|consultar","observaciones":"breve descripción max 60 chars","confianza":"alta|media","status":"encontrado"}

Si ningún precio es válido para un neumático, devolvé: {"sitio":"${hostname}","url":"${siteUrl}","precio":null,"precio_formateado":null,"disponibilidad":"desconocido","observaciones":"Precios no corresponden a neumáticos","confianza":"baja","status":"no_encontrado"}`;

    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
        'anthropic-version': '2023-06-01',
        'anthropic-dangerous-direct-browser-access': 'true'
      },
      body: JSON.stringify({
        model: 'claude-haiku-4-5-20251001',
        max_tokens: 400,
        // SIN tools: respuesta determinística siempre
        messages: [{ role: 'user', content: prompt }]
      })
    });
    if (!response.ok) {
      const err = await response.json().catch(() => ({}));
      throw new Error(err.error?.message || `Error HTTP ${response.status}`);
    }
    const data = await response.json();
    const raw = (data.content||[]).filter(b=>b.type==='text').map(b=>b.text).join('').trim();
    for (const s of [raw, raw.replace(/```json|```/g,'').trim()]) {
      try { return JSON.parse(s); } catch {}
      const mm = s.match(/\{[\s\S]*\}/);
      if (mm) { try { return JSON.parse(mm[0]); } catch {} }
    }
    // Si no parsea, usar datos del scraping directamente
    return {
      sitio: hostname, url: siteUrl,
      precio: scrapedData.precioMinimo,
      precio_formateado: formatPrice(scrapedData.precioMinimo),
      disponibilidad: 'disponible', confianza: 'media',
      observaciones: 'Precio por scraping directo',
      status: 'encontrado'
    };
  }

  // CASO 2: Sin scraping → Claude busca con web_search (fallback)
  const variantes = m ? `${m[1]}/${m[2]} R${m[3]} | ${m[1]}/${m[2]}R${m[3]}` : medida;
  const prompt = `Sos un investigador de precios meticuloso. Tu tarea es encontrar el precio MÁS BAJO real y DISPONIBLE de neumáticos medida ${medida} en el sitio ${siteUrl}.

Variantes del formato: ${variantes}

PASOS OBLIGATORIOS:
1. Buscá: site:${hostname} neumatico ${medida}
2. Si no hay resultados: neumatico ${medida} ${hostname} precio Argentina
3. Revisá CADA resultado encontrado y anotá: precio + si está en stock/disponible
4. Si el sitio tiene varios modelos/marcas para esa medida, anotá el precio de TODOS
5. Elegí el precio MÁS BAJO ENTRE LOS QUE ESTÁN EN STOCK

REGLAS CRÍTICAS (muy importantes):
- IGNORÁ productos marcados como "sin stock", "agotado", "no disponible", "consultar disponibilidad" — esos NO cuentan, aunque tengan precio
- Si TODOS los productos para esa medida están sin stock, indicá "disponibilidad": "no disponible" y usá el precio más bajo de los que viste (con confianza "media")
- NO devuelvas el primer precio que veas — comparalos todos y quedate con el menor
- Precio en pesos argentinos (ARS), entre $10.000 y $2.000.000
- NO inventes ni estimes precios que no viste explícitamente
- Si no encontrás nada concreto para esa medida exacta: status "no_encontrado"

Respondé SOLO con JSON, sin markdown ni texto extra:
{"sitio":"nombre comercial","url":"${siteUrl}","precio":NUMERO_o_null,"precio_formateado":"$XX.XXX o null","disponibilidad":"disponible|consultar|no disponible|desconocido","observaciones":"qué modelo y stock, max 70 chars","confianza":"alta|media|baja","status":"encontrado|no_encontrado|error"}`;

  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': apiKey,
      'anthropic-version': '2023-06-01',
      'anthropic-dangerous-direct-browser-access': 'true'
    },
    body: JSON.stringify({
      model: 'claude-haiku-4-5-20251001',
      max_tokens: 600,
      tools: [{ type: 'web_search_20250305', name: 'web_search' }],
      messages: [{ role: 'user', content: prompt }]
    })
  });
  if (!response.ok) {
    const err = await response.json().catch(() => ({}));
    throw new Error(err.error?.message || `Error HTTP ${response.status}`);
  }
  const data = await response.json();
  const raw = (data.content||[]).filter(b=>b.type==='text').map(b=>b.text).join('').trim();
  for (const s of [raw, raw.replace(/```json|```/g,'').trim()]) {
    try { return JSON.parse(s); } catch {}
    const mm = s.match(/\{[\s\S]*\}/);
    if (mm) { try { return JSON.parse(mm[0]); } catch {} }
  }
  throw new Error('Respuesta no parseable');
}

// ──────────────────────────────────────────────
// Renderizar estado de carga por sitio
// ──────────────────────────────────────────────
function renderLoadingCards(sites, producto) {
  const container = document.getElementById('resultados');
  container.style.display = 'block';

  let html = `
    <div class="card mb-4">
      <div class="card-header">
        <p style="font-size:13px; color:var(--muted);">Analizando precios para</p>
        <h2 class="condensed" style="font-size:20px; font-weight:700; color:var(--text); margin-top:2px;">${escHtml(producto)}</h2>
      </div>
      <div class="p-4">
        <div id="loading-cards" class="grid gap-3" style="grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));">
  `;
  sites.forEach((url, i) => {
    html += `
      <div class="result-card" id="card-${i}" style="background:var(--surface-2);">
        <div class="p-4">
          <p style="font-size:12px; color:var(--muted); word-break:break-all; margin-bottom:10px;">${escHtml(url)}</p>
          <div class="flex items-center gap-2">
            <div class="spinner"></div>
            <span style="font-size:14px; color:var(--muted);">Buscando precio...</span>
          </div>
        </div>
      </div>`;
  });
  html += `</div></div></div>`;
  container.innerHTML = html;
}

// ──────────────────────────────────────────────
// Actualizar una card con resultado
// ──────────────────────────────────────────────
function updateCard(index, result, rank, minPrice) {
  const card = document.getElementById(`card-${index}`);
  if (!card) return;

  const found    = result.status === 'encontrado' && result.precio;
  const price    = found ? extractNumber(result.precio) : null;
  const isBest   = found && price === minPrice;
  const priceCls = !found ? '' : isBest ? 'best' : price <= minPrice * 1.05 ? 'mid' : 'high';
  const diffPct  = (found && minPrice && price !== minPrice)
    ? Math.round(((price - minPrice) / minPrice) * 100)
    : null;

  if (isBest) card.classList.add('best-price');
  else if (priceCls === 'high') card.classList.add('high-price');

  const confColor = { alta: '#5DD990', media: '#F5B93A', baja: '#F07060' };

  card.innerHTML = `
    ${isBest ? `<div class="winner-banner">★ Precio más bajo</div>` : ''}
    <div class="p-4">
      <p style="font-size:11px; color:var(--muted); word-break:break-all; margin-bottom:6px;">${escHtml(result.url || '')}</p>
      <p class="condensed" style="font-size:16px; font-weight:700; color:var(--text); margin-bottom:12px;">${escHtml(result.sitio || 'Sitio desconocido')}</p>
      ${found ? `
        <p class="price-badge ${priceCls}">${result.precio_formateado || formatPrice(price) || '—'}</p>
        ${diffPct !== null ? `
          <span class="diff-badge mt-2 inline-block ${diffPct > 0 ? 'diff-neg' : 'diff-pos'}">
            ${diffPct > 0 ? '+' : ''}${diffPct}% vs. más barato
          </span>
        ` : `<span class="tag tag-green mt-2" style="display:inline-block;">Precio más bajo</span>`}
        <div class="progress-bar">
          <div class="progress-fill" style="width:${Math.min(100, Math.round((minPrice / price) * 100))}%; background:${isBest ? 'var(--green)' : priceCls === 'high' ? '#F07060' : 'var(--amber)'}"></div>
        </div>
        <div class="flex justify-between mt-3" style="font-size:12px; color:var(--muted);">
          <span>Disp.: <strong style="color:var(--text);">${result.disponibilidad || '—'}</strong></span>
          <span>Confianza: <strong style="color:${confColor[result.confianza] || 'var(--muted)'};">${result.confianza || '—'}</strong></span>
        </div>
        ${result.observaciones ? `<p style="font-size:12px; color:var(--muted); margin-top:8px; font-style:italic;">${escHtml(result.observaciones)}</p>` : ''}
      ` : `
        <div class="flex items-center gap-2 mt-2">
          <span style="font-size:20px;">❌</span>
          <span style="font-size:14px; color:var(--muted);">${result.status === 'error' ? 'Error al consultar' : 'Precio no encontrado'}</span>
        </div>
        ${result.observaciones ? `<p style="font-size:12px; color:var(--muted); margin-top:6px; font-style:italic;">${escHtml(result.observaciones)}</p>` : ''}
      `}
    </div>`;

  card.classList.add('fade-in');
}

// ──────────────────────────────────────────────
// Tabla resumen
// ──────────────────────────────────────────────
function renderSummaryTable(results, producto) {
  // Solo incluir resultados con precio numérico válido
  const found = results.filter(r => {
    if (r.status !== 'encontrado') return false;
    const p = typeof r.precio === 'number' ? r.precio : extractNumber(r.precio);
    return p !== null && p > 0;
  });
  if (found.length < 2) return '';

  // Normalizar precios a número para todos los cálculos
  const withNum = found.map(r => ({
    ...r,
    _num: typeof r.precio === 'number' ? r.precio : extractNumber(r.precio)
  })).filter(r => r._num !== null);

  if (withNum.length < 2) return '';

  const prices  = withNum.map(r => r._num);
  const minP    = Math.min(...prices);
  const maxP    = Math.max(...prices);
  const avgP    = Math.round(prices.reduce((a,b)=>a+b,0) / prices.length);
  const savings = maxP - minP;

  let rows = '';
  const sorted = [...withNum].sort((a,b) => a._num - b._num);
  sorted.forEach((r, i) => {
    const p       = r._num;
    const isBest  = p === minP;
    const isWorst = p === maxP;
    rows += `
      <tr style="border-bottom:1px solid var(--border);">
        <td style="padding:10px 14px;">
          <span class="condensed font-bold" style="font-size:16px; color:${isBest ? 'var(--green)' : isWorst ? '#F07060' : 'var(--text)'};">#${i+1}</span>
        </td>
        <td style="padding:10px 14px;">
          <p style="font-weight:600; font-size:14px; margin:0;">${escHtml(r.sitio || '')}</p>
          <p style="font-size:11px; color:var(--muted); margin:0; word-break:break-all;">${escHtml(r.url || '')}</p>
        </td>
        <td style="padding:10px 14px; text-align:right;">
          <span class="condensed font-bold" style="font-size:18px; color:${isBest ? 'var(--green)' : isWorst ? '#F07060' : 'var(--amber)'};">
            ${formatPrice(p)}
          </span>
        </td>
        <td style="padding:10px 14px; text-align:center;">
          ${isBest
            ? `<span class="tag tag-green">Más barato</span>`
            : `<span class="diff-badge diff-neg">+${Math.round(((p - minP) / minP) * 100)}%</span>`
          }
        </td>
      </tr>`;
  });

  return `
    <div class="card mt-4 fade-in">
      <div class="card-header">
        <h3 class="condensed font-bold uppercase tracking-wider" style="font-size:16px;">Resumen comparativo</h3>
      </div>
      <div style="overflow-x:auto;">
        <table style="width:100%; border-collapse:collapse;">
          <thead>
            <tr style="border-bottom:2px solid var(--border);">
              <th style="padding:8px 14px; text-align:left; font-size:11px; color:var(--muted); font-weight:600; letter-spacing:0.08em; text-transform:uppercase; width:50px;">POS.</th>
              <th style="padding:8px 14px; text-align:left; font-size:11px; color:var(--muted); font-weight:600; letter-spacing:0.08em; text-transform:uppercase;">SITIO</th>
              <th style="padding:8px 14px; text-align:right; font-size:11px; color:var(--muted); font-weight:600; letter-spacing:0.08em; text-transform:uppercase;">PRECIO</th>
              <th style="padding:8px 14px; text-align:center; font-size:11px; color:var(--muted); font-weight:600; letter-spacing:0.08em; text-transform:uppercase;">VS. MÁS BARATO</th>
            </tr>
          </thead>
          <tbody>${rows}</tbody>
        </table>
      </div>
      <div class="p-4 grid gap-3" style="grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); border-top:1px solid var(--border);">
        <div style="background:var(--surface-3); border-radius:6px; padding:10px 14px;">
          <p style="font-size:11px; color:var(--muted); margin-bottom:4px; letter-spacing:0.06em; text-transform:uppercase;">Precio mínimo</p>
          <p class="condensed font-bold" style="font-size:20px; color:var(--green);">${formatPrice(minP)}</p>
        </div>
        <div style="background:var(--surface-3); border-radius:6px; padding:10px 14px;">
          <p style="font-size:11px; color:var(--muted); margin-bottom:4px; letter-spacing:0.06em; text-transform:uppercase;">Precio máximo</p>
          <p class="condensed font-bold" style="font-size:20px; color:#F07060;">${formatPrice(maxP)}</p>
        </div>
        <div style="background:var(--surface-3); border-radius:6px; padding:10px 14px;">
          <p style="font-size:11px; color:var(--muted); margin-bottom:4px; letter-spacing:0.06em; text-transform:uppercase;">Precio promedio</p>
          <p class="condensed font-bold" style="font-size:20px; color:var(--amber);">${formatPrice(avgP)}</p>
        </div>
        <div style="background:rgba(46,204,113,0.08); border:1px solid rgba(46,204,113,0.2); border-radius:6px; padding:10px 14px;">
          <p style="font-size:11px; color:var(--muted); margin-bottom:4px; letter-spacing:0.06em; text-transform:uppercase;">Ahorro potencial</p>
          <p class="condensed font-bold" style="font-size:20px; color:var(--green);">${formatPrice(savings)}</p>
        </div>
      </div>
    </div>`;
}

// ──────────────────────────────────────────────
// Función principal
// ──────────────────────────────────────────────
async function compararPrecios() {
  const apiKey   = document.getElementById('apiKey').value.trim();
  const producto = document.getElementById('producto').value.trim();
  const siteInputs = document.querySelectorAll('.site-url');
  const sites    = Array.from(siteInputs).map(i => i.value.trim()).filter(Boolean);

  const errDiv = document.getElementById('error-global');
  errDiv.style.display = 'none';

  if (!apiKey) {
    errDiv.textContent = '⚠ Ingresá tu clave API de Anthropic para continuar.';
    errDiv.style.display = 'block'; return;
  }
  if (!apiKey.startsWith('sk-ant-')) {
    errDiv.textContent = '⚠ La clave API no parece válida. Debe comenzar con "sk-ant-".';
    errDiv.style.display = 'block'; return;
  }
  if (!producto) {
    errDiv.textContent = '⚠ Ingresá la medida del neumático (ancho, perfil y rodado).';
    errDiv.style.display = 'block'; return;
  }
  if (!/\d{3}\/\d{2}\s*R\d{2}/i.test(producto)) {
    errDiv.textContent = '⚠ La medida no tiene el formato correcto. Debe ser algo como: 185/60 R15';
    errDiv.style.display = 'block'; return;
  }
  if (sites.length === 0) {
    errDiv.textContent = '⚠ Ingresá al menos una URL de sitio web.';
    errDiv.style.display = 'block'; return;
  }

  const btn = document.getElementById('btn-comparar');
  btn.disabled = true;
  btn.innerHTML = `<div class="spinner"></div> Analizando ${sites.length} sitio${sites.length > 1 ? 's' : ''}...`;

  // Mostrar skeletons de sitios
  renderLoadingCards(sites, producto);

  // Consultar sitios: scraping directo primero, Claude con esos datos como contexto
  const siteResults = await Promise.all(sites.map(async (url, i) => {
    try {
      // 1. Scraping directo del HTML del sitio
      const scraped = await scrapeSite(producto, url);
      // 2. Claude interpreta los precios encontrados (o busca si no hay scraping)
      const res = await fetchPriceFromClaude(apiKey, producto, url, scraped);
      // Fallback: si Claude no devolvió precio pero scraping sí encontró algo
      if ((!res.precio || res.status !== 'encontrado') && scraped?.precioMinimo) {
        let siteName = url;
        try { siteName = new URL(url.startsWith('http')?url:'https://'+url).hostname.replace('www.',''); } catch {}
        return {
          sitio: res.sitio || siteName, url,
          precio: scraped.precioMinimo,
          precio_formateado: formatPrice(scraped.precioMinimo),
          disponibilidad: 'disponible',
          observaciones: 'Precio por scraping directo',
          confianza: 'media', status: 'encontrado', _index: i
        };
      }
      return { ...res, url, _index: i };
    } catch (err) {
      let siteName = url;
      try { siteName = new URL(url.startsWith('http')?url:'https://'+url).hostname.replace('www.',''); } catch {}
      return { sitio: siteName, url, precio: null, status: 'error',
        observaciones: err.message?.substring(0,100) || 'Error', confianza: 'baja', _index: i };
    }
  }));

  // Calcular precio mínimo global — solo números válidos
  const sitePrices = siteResults
    .filter(r => r.status === 'encontrado' && r.precio)
    .map(r => typeof r.precio === 'number' ? r.precio : extractNumber(r.precio))
    .filter(Boolean);
  const minPrice = sitePrices.length > 0 ? Math.min(...sitePrices) : null;

  // Actualizar cards de sitios
  siteResults.forEach((r, i) => updateCard(i, r, i, minPrice));

  // Tabla resumen de sitios
  const summaryHtml = renderSummaryTable(siteResults, producto);
  if (summaryHtml) {
    document.getElementById('resultados').insertAdjacentHTML('beforeend', summaryHtml);
  }

  btn.disabled = false;
  btn.innerHTML = `
    <svg width="20" height="20" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
      <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
    </svg>
    Nueva comparación`;

  // Guardar resultados para exportación PDF
  const siteWithNum = siteResults.map(r => ({
    ...r,
    _num: r.status === 'encontrado' ? (typeof r.precio === 'number' ? r.precio : extractNumber(r.precio)) : null
  }));
  window._lastSiteResults = siteWithNum;

  // Mostrar botón PDF
  document.getElementById('btn-pdf-wrap').style.display = 'block';
}

// ──────────────────────────────────────────────
// Exportar PDF — generado en código, sin captura de pantalla
// ──────────────────────────────────────────────
async function exportarPDF() {
  const btn = document.querySelector('#btn-pdf-wrap button');
  const originalHTML = btn.innerHTML;
  btn.innerHTML = '<div class="spinner" style="border-top-color:var(--muted);margin-right:8px;display:inline-block;"></div> Generando PDF...';
  btn.disabled = true;

  try {
    const { jsPDF } = window.jspdf;
    const medida  = document.getElementById('producto').value.trim();
    const fecha   = new Date().toLocaleDateString('es-AR', { day:'2-digit', month:'2-digit', year:'numeric' });
    const hora    = new Date().toLocaleTimeString('es-AR', { hour:'2-digit', minute:'2-digit' });

    const pdf   = new jsPDF({ orientation:'p', unit:'mm', format:'a4' });
    const W     = pdf.internal.pageSize.getWidth();   // 210
    const H     = pdf.internal.pageSize.getHeight();  // 297
    const ml    = 14, mr = 14, usable = W - ml - mr;
    let   y     = 0;

    // ── Helpers ──
    const C = { black:'#111111', dark:'#222222', mid:'#444444', muted:'#777777',
                light:'#aaaaaa', border:'#dddddd', bg:'#f7f7f7', bgCard:'#ffffff',
                red:'#c0392b', green:'#1e8449', amber:'#b7770d', blue:'#1a5276' };

    function setC(hex) {
      const r=parseInt(hex.slice(1,3),16), g=parseInt(hex.slice(3,5),16), b=parseInt(hex.slice(5,7),16);
      pdf.setTextColor(r,g,b);
    }
    function setFC(hex) {
      const r=parseInt(hex.slice(1,3),16), g=parseInt(hex.slice(3,5),16), b=parseInt(hex.slice(5,7),16);
      pdf.setFillColor(r,g,b);
    }
    function setDC(hex) {
      const r=parseInt(hex.slice(1,3),16), g=parseInt(hex.slice(3,5),16), b=parseInt(hex.slice(5,7),16);
      pdf.setDrawColor(r,g,b);
    }
    function txt(text, x, fy, opts) { pdf.text(String(text||''), x, fy, opts||{}); }
    function newPage() {
      pdf.addPage();
      setFC('#ffffff'); pdf.rect(0,0,W,H,'F');
      y = 12;
      // mini header en páginas adicionales
      setFC('#c0392b'); pdf.rect(0,0,W,1,'F');
      pdf.setFontSize(7); setC(C.muted);
      txt('ANÁLISIS DE COMPETENCIA · GOMERÍA CENTRAL', ml, 8);
      txt(`${medida} · ${fecha}`, W-mr, 8, {align:'right'});
      setDC(C.border); pdf.setLineWidth(0.2); pdf.line(ml,10,W-mr,10);
      y = 14;
    }
    function checkY(needed) { if (y + needed > H - 16) newPage(); }

    // ── Página 1 ──
    setFC('#ffffff'); pdf.rect(0,0,W,H,'F');

    // Franja roja
    setFC('#c0392b'); pdf.rect(0,0,W,2,'F');

    // Título
    pdf.setFontSize(18); pdf.setFont('helvetica','bold'); setC(C.dark);
    txt('ANÁLISIS DE COMPETENCIA', ml, 14);
    pdf.setFontSize(8); pdf.setFont('helvetica','normal'); setC(C.muted);
    txt('GOMERÍA CENTRAL · COMPARADOR DE PRECIOS', ml, 19);
    pdf.setFontSize(8); setC(C.muted);
    txt(`Generado: ${fecha} ${hora}`, W-mr, 14, {align:'right'});

    // Medida
    pdf.setFontSize(9); setC(C.muted); txt('MEDIDA:', ml, 27);
    pdf.setFontSize(13); pdf.setFont('helvetica','bold'); setC(C.dark); txt(medida, ml+22, 27);

    setDC(C.border); pdf.setLineWidth(0.3); pdf.line(ml,31,W-mr,31);
    y = 37;

    // ── Recolectar datos mostrados en pantalla ──
    // Leer de los datos guardados en window (los guardamos al terminar la búsqueda)
    const siteData = window._lastSiteResults || [];

    // ── Sección: Sitios web ──
    if (siteData.length > 0) {
      pdf.setFontSize(8); pdf.setFont('helvetica','bold'); setC(C.muted);
      txt('SITIOS WEB', ml, y); y += 5;

      const colW = (usable - (Math.min(siteData.length,3)-1)*4) / Math.min(siteData.length,3);
      const rows = [];
      for (let i=0; i<siteData.length; i+=3) rows.push(siteData.slice(i,i+3));

      // Calcular precio mínimo de sitios
      const validPrices = siteData.filter(r=>r._num).map(r=>r._num);
      const minP = validPrices.length ? Math.min(...validPrices) : null;

      for (const row of rows) {
        // Altura estimada de tarjeta
        const cardH = 34;
        checkY(cardH + 4);
        let x = ml;
        for (const r of row) {
          const p = r._num;
          const isBest = minP && p === minP;
          const isHigh = minP && p > minP * 1.1;
          const borderC = isBest ? C.green : isHigh ? C.red : C.border;

          // Fondo y borde de tarjeta
          setFC(C.bgCard); setDC(borderC);
          pdf.setLineWidth(isBest ? 0.5 : 0.3);
          pdf.roundedRect(x, y, colW, cardH, 2, 2, 'FD');

          // Banner "precio más bajo"
          if (isBest) {
            setFC('#e8f5e9');
            pdf.roundedRect(x, y, colW, 5.5, 2, 2, 'F');
            pdf.setFontSize(6); pdf.setFont('helvetica','bold'); setC(C.green);
            txt('★ PRECIO MÁS BAJO', x + 2, y + 3.8);
          }

          const ty = isBest ? y + 7 : y + 4;
          // URL
          pdf.setFontSize(6); pdf.setFont('helvetica','normal'); setC(C.light);
          const hostname = r.url ? r.url.replace(/https?:\/\/(www\.)?/,'').split('/')[0] : '';
          txt(hostname.length > 28 ? hostname.slice(0,27)+'…' : hostname, x+2, ty);
          // Nombre sitio
          pdf.setFontSize(8); pdf.setFont('helvetica','bold'); setC(C.dark);
          txt((r.sitio||'').slice(0,22), x+2, ty+5);

          if (p) {
            // Precio
            pdf.setFontSize(13); pdf.setFont('helvetica','bold');
            setC(isBest ? C.green : isHigh ? C.red : C.amber);
            txt(formatPrice(p), x+2, ty+13);
            // Diferencia
            if (!isBest && minP) {
              const diff = Math.round(((p-minP)/minP)*100);
              pdf.setFontSize(7); pdf.setFont('helvetica','normal'); setC(C.red);
              txt(`+${diff}% vs. más barato`, x+2, ty+18);
            }
            // Disponibilidad y confianza
            pdf.setFontSize(6); setC(C.muted);
            txt(`Disp: ${r.disponibilidad||'—'}  Conf: ${r.confianza||'—'}`, x+2, ty+23);
            // Observaciones
            if (r.observaciones) {
              const obs = r.observaciones.slice(0,40);
              pdf.setFontSize(6); setC(C.light);
              txt(obs, x+2, ty+27);
            }
          } else {
            pdf.setFontSize(8); setC(C.muted);
            txt('Precio no encontrado', x+2, ty+10);
            if (r.observaciones) {
              pdf.setFontSize(6); setC(C.light);
              txt(r.observaciones.slice(0,38), x+2, ty+16);
            }
          }
          x += colW + 4;
        }
        y += cardH + 4;
      }
      y += 4;
    }

    // ── Resumen comparativo ──
    const validSites = siteData.filter(r => r._num);
    if (validSites.length >= 2) {
      checkY(10 + validSites.length * 10 + 20);

      // Título sección
      setFC(C.bg); setDC(C.border); pdf.setLineWidth(0.2);
      pdf.rect(ml, y, usable, 7, 'FD');
      pdf.setFontSize(8); pdf.setFont('helvetica','bold'); setC(C.dark);
      txt('RESUMEN COMPARATIVO', ml+2, y+5);
      y += 9;

      // Encabezados tabla
      pdf.setFontSize(7); pdf.setFont('helvetica','normal'); setC(C.muted);
      txt('POS.', ml+2, y); txt('SITIO', ml+18, y);
      txt('PRECIO', W-mr-30, y, {align:'right'}); txt('DIFERENCIA', W-mr, y, {align:'right'});
      setDC(C.border); pdf.setLineWidth(0.2); pdf.line(ml,y+2,W-mr,y+2);
      y += 5;

      const sorted = [...validSites].sort((a,b)=>a._num-b._num);
      const minSite = sorted[0]._num;
      sorted.forEach((r,i) => {
        checkY(10);
        const p = r._num;
        const isBest = i===0;
        pdf.setFontSize(8); pdf.setFont('helvetica','bold');
        setC(isBest ? C.green : i===sorted.length-1 ? C.red : C.dark);
        txt(`#${i+1}`, ml+2, y);
        pdf.setFont('helvetica','normal'); setC(C.dark);
        txt((r.sitio||'').slice(0,30), ml+18, y);
        pdf.setFont('helvetica','bold'); setC(isBest?C.green:C.amber);
        txt(formatPrice(p), W-mr-30, y, {align:'right'});
        if (isBest) {
          pdf.setFontSize(7); setC(C.green); txt('MÁS BARATO', W-mr, y, {align:'right'});
        } else {
          const diff = Math.round(((p-minSite)/minSite)*100);
          pdf.setFontSize(7); setC(C.red); txt(`+${diff}%`, W-mr, y, {align:'right'});
        }
        setDC(C.border); pdf.setLineWidth(0.15); pdf.line(ml,y+2,W-mr,y+2);
        y += 8;
      });

      // Métricas
      const prices = validSites.map(r=>r._num);
      const minP = Math.min(...prices), maxP = Math.max(...prices);
      const avgP = Math.round(prices.reduce((a,b)=>a+b)/prices.length);
      y += 4; checkY(20);
      const mw = (usable-12)/4;
      const metrics = [
        { label:'PRECIO MÍNIMO', val:formatPrice(minP), color:C.green },
        { label:'PRECIO MÁXIMO', val:formatPrice(maxP), color:C.red },
        { label:'PROMEDIO', val:formatPrice(avgP), color:C.amber },
        { label:'AHORRO POTENCIAL', val:formatPrice(maxP-minP), color:C.green },
      ];
      let mx = ml;
      metrics.forEach(({label,val,color}) => {
        setFC(C.bg); setDC(C.border); pdf.setLineWidth(0.2);
        pdf.roundedRect(mx, y, mw, 14, 1.5, 1.5, 'FD');
        pdf.setFontSize(6); pdf.setFont('helvetica','normal'); setC(C.muted);
        txt(label, mx+2, y+4);
        pdf.setFontSize(10); pdf.setFont('helvetica','bold'); setC(color);
        txt(val, mx+2, y+11);
        mx += mw+4;
      });
      y += 18;
    }

    // ── Footer en todas las páginas ──
    const totalPages = pdf.internal.getNumberOfPages();
    for (let i=1; i<=totalPages; i++) {
      pdf.setPage(i);
      setDC(C.border); pdf.setLineWidth(0.2);
      pdf.line(ml, H-10, W-mr, H-10);
      pdf.setFontSize(7); pdf.setFont('helvetica','normal'); setC(C.light);
      txt(`Gomería Central · Análisis de competencia · ${fecha}`, ml, H-6);
      txt(`Pág. ${i} / ${totalPages}`, W-mr, H-6, {align:'right'});
    }

    const filename = `analisis-competencia-${medida.replace(/[^a-zA-Z0-9]/g,'-').toLowerCase()}-${fecha.replace(/\//g,'-')}.pdf`;
    pdf.save(filename);

  } catch (err) {
    alert('Error al generar el PDF: ' + err.message);
    console.error(err);
  }

  btn.innerHTML = originalHTML;
  btn.disabled = false;
}

// ──────────────────────────────────────────────
// Escape HTML
// ──────────────────────────────────────────────
function escHtml(str) {
  return String(str)
    .replace(/&/g,'&amp;')
    .replace(/</g,'&lt;')
    .replace(/>/g,'&gt;')
    .replace(/"/g,'&quot;');
}

// Abrir instrucciones por defecto si es la primera vez
window.addEventListener('load', () => {
  const body = document.getElementById('instrucciones-body');
  const arrow = document.getElementById('arrow-icon');
  body.style.display = 'block';
  arrow.style.transform = 'rotate(180deg)';

  // Recuperar API key guardada
  try {
    const saved = localStorage.getItem('gc_apikey');
    if (saved) {
      document.getElementById('apiKey').value = saved;
      // Mostrar notificación sutil
      const note = document.createElement('p');
      note.textContent = '✓ Clave API cargada automáticamente';
      note.style.cssText = 'font-size:11px;color:#5DD990;margin-top:4px;';
      document.getElementById('apiKey').parentNode.appendChild(note);
      setTimeout(() => note.remove(), 3000);
    }
  } catch {}
});

// Guardar API key cuando el usuario la ingresa
document.addEventListener('DOMContentLoaded', () => {
  const apiInput = document.getElementById('apiKey');
  if (apiInput) {
    apiInput.addEventListener('blur', () => {
      const val = apiInput.value.trim();
      if (val.startsWith('sk-ant-')) {
        try { localStorage.setItem('gc_apikey', val); } catch {}
      }
    });
  }
});
</script>
</body>
</html>

