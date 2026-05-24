<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Desarrollo Regional Comparado</title>
<script src="https://d3js.org/d3.v7.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/topojson-client@3"></script>
<style>
:root{
  --bg:#f7f8fa;--card:#ffffff;--ink:#243042;--muted:#64748b;--line:#dbe3ec;
  --blue:#2f6f9f;--blue2:#e8f2fb;--green:#6cae75;--green2:#ecf7ef;
  --orange:#f2a65a;--orange2:#fff4e6;--red:#d66a6a;--red2:#fff0f0;
  --shadow:0 14px 32px rgba(31,41,55,.10);--radius:22px;
}
*{box-sizing:border-box}
body{margin:0;font-family:Tahoma, Geneva, sans-serif;background:linear-gradient(180deg,#fff,var(--bg));color:var(--ink)}
header{padding:26px 18px 18px;max-width:1600px;margin:auto}
.hero{background:linear-gradient(135deg,#ffffff,#eef6f1 48%,#eaf3fb);border:1px solid var(--line);border-radius:28px;padding:24px 30px;box-shadow:var(--shadow);position:relative;overflow:hidden}
.hero:after{content:"";position:absolute;width:280px;height:280px;border-radius:50%;right:-80px;top:-100px;background:rgba(242,166,90,.18)}
.kicker{font-size:13px;text-transform:uppercase;letter-spacing:.11em;color:var(--blue);font-weight:700;margin-bottom:8px}
h1{margin:0;font-size:clamp(28px,4vw,46px);line-height:1.08}
.hero p{font-size:16px;max-width:1180px;color:#45556c;line-height:1.55}
main{max-width:1600px;margin:auto;padding:0 18px 36px}
.grid{display:grid;grid-template-columns:1fr;gap:18px}
.panel{background:var(--card);border:1px solid var(--line);border-radius:var(--radius);padding:18px;box-shadow:0 6px 20px rgba(31,41,55,.06)}
.controls{display:grid;grid-template-columns:1.4fr 1fr 1fr;gap:14px;align-items:end}
label{font-size:13px;font-weight:700;color:#334155;display:block;margin-bottom:7px}
select,input{width:100%;padding:12px 14px;border:1px solid #cbd5e1;border-radius:14px;background:#fff;color:var(--ink);font-size:15px}
button{border:0;border-radius:14px;padding:12px 16px;background:var(--blue);color:#fff;font-weight:700;cursor:pointer;box-shadow:0 8px 18px rgba(47,111,159,.18)}
button.secondary{background:#fff;color:var(--blue);border:1px solid #b7cfe4;box-shadow:none}
button:hover{transform:translateY(-1px)}
.tabs{display:flex;gap:8px;flex-wrap:wrap;margin-top:14px}
.tab{padding:9px 12px;border:1px solid var(--line);border-radius:999px;background:#fff;color:#475569;cursor:pointer;font-weight:700;font-size:13px}
.tab.active{background:var(--blue);color:white;border-color:var(--blue)}
.layout{display:grid;grid-template-columns:1.05fr .95fr;gap:18px;margin-top:18px}
.map-wrap{min-height:360px;display:flex;align-items:center;justify-content:center;background:linear-gradient(135deg,#fafafa,#f1f7fb);border-radius:20px;border:1px solid var(--line);padding:12px}
svg{width:100%;height:auto;max-height:410px}
.region{fill:#dfe8e9;stroke:#b7c1c7;stroke-width:2;transition:.25s;cursor:pointer}
.region:hover{fill:#cfe0cf}
.region.active{fill:#f2c078;stroke:#c47b2c;stroke-width:3}
.pin{fill:#f2a65a;stroke:white;stroke-width:3;cursor:pointer;transition:.2s}
.pin:hover{r:13}
.pin.active{fill:#d66a6a}
.cards{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:14px;margin-top:18px}
.country-card{background:#fff;border:1px solid var(--line);border-radius:20px;padding:18px;box-shadow:0 8px 18px rgba(31,41,55,.06);transition:.2s;cursor:pointer}
.country-card:hover{transform:translateY(-2px);box-shadow:var(--shadow)}
.country-card strong{font-size:19px}
.badges{display:flex;flex-wrap:wrap;gap:6px;margin:10px 0}
.badge{font-size:12px;border-radius:999px;padding:5px 8px;background:var(--blue2);color:#205072;font-weight:700}
.badge.strong{background:var(--green2);color:#2f7d3d}
.badge.weak{background:var(--red2);color:#a33a3a}
.detail h2,.compare h2{margin:0 0 8px;font-size:28px;color:#1e3a5f}
.detail .subtitle{color:var(--muted);margin:0 0 15px}
.info-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:10px;margin:14px 0}
.info{background:#f8fafc;border:1px solid var(--line);border-radius:15px;padding:12px}
.info span{font-size:12px;color:var(--muted);font-weight:700;text-transform:uppercase;letter-spacing:.04em}
.info p{margin:5px 0 0;font-weight:700}
.section-title{margin:18px 0 9px;color:#1e3a5f;font-size:18px}
.text{line-height:1.6;color:#334155}
.agent-list,.risks{display:grid;grid-template-columns:1fr;gap:10px}
.agent{border-left:5px solid var(--green);background:#f7fbf8;border-radius:14px;padding:11px 13px}
.agent b{color:#285f31}
.risk{border-left:5px solid var(--orange);background:#fffaf2;border-radius:14px;padding:11px 13px}
.compare-box{display:grid;grid-template-columns:1fr 1fr;gap:16px}
.compare-card{border:1px solid var(--line);border-radius:20px;padding:18px;background:#fff}
.compare-row{display:grid;grid-template-columns:180px 1fr 1fr;gap:10px;align-items:stretch;border-top:1px solid var(--line);padding:10px 0}
.compare-row .label{font-size:13px;color:var(--muted);font-weight:800}
.empty{padding:26px;background:#f8fafc;border:1px dashed #cbd5e1;border-radius:18px;color:#64748b;text-align:center}
.footer-note{font-size:13px;color:#64748b;line-height:1.5;margin-top:18px}
.meter{height:12px;background:#e5e7eb;border-radius:999px;overflow:hidden;margin-top:8px}
.meter span{height:100%;display:block;background:linear-gradient(90deg,var(--green),var(--orange));border-radius:999px}
.gini-tag{display:inline-block;padding:6px 9px;border-radius:999px;font-size:12px;font-weight:800}
.gini-low{background:#ecf7ef;color:#2f7d3d}
.gini-med{background:#e8f2fb;color:#205072}
.gini-high{background:#fff4e6;color:#9a5a12}
.gini-vhigh{background:#fff0f0;color:#a33a3a}
.gini-na{background:#f1f5f9;color:#64748b}

.real-map-panel h2{margin-top:0;color:#1e3a5f}
.real-map-grid{display:grid;grid-template-columns:minmax(720px,2fr) minmax(320px,.75fr);gap:18px;align-items:stretch}
.real-map-container{min-height:470px;background:#fdfefe;border-radius:20px;border:1px solid var(--line);padding:12px;box-shadow:0 4px 16px rgba(0,0,0,.05);display:flex;align-items:center;justify-content:center;position:relative;overflow:hidden}
.real-map-container svg{width:100%;height:auto;display:block;max-height:570px;border-radius:16px}
.pais{fill:#d5dfe8;stroke:#b6c4d2;stroke-width:.8;cursor:pointer;transition:fill .25s ease,stroke .25s ease,stroke-width .25s ease}
.pais:hover{fill:#c0d0dd;stroke:#8d9fb0}
.pais.active-map{fill:#f2d4b0;stroke:#d4a574;stroke-width:1.5}
.pin-d3{cursor:pointer;transition:transform .2s ease}
.pin-d3:hover{transform:scale(1.25)}
.pin-d3.active-map{transform:scale(1.18)}
.pin-d3 circle.outer{fill:rgba(224,115,66,.28)}
.pin-d3 circle.inner{fill:#e07342;stroke:#fff;stroke-width:2.2}
.pin-d3:hover circle.inner{fill:#ec6b3a}
.pin-d3.active-map circle.inner{fill:#c85a2c;stroke-width:3}
.region-info-card{background:#fff;border:1px solid #eaedf1;border-radius:20px;padding:18px;box-shadow:0 8px 24px rgba(31,41,55,.08);min-height:470px;display:flex;flex-direction:column;gap:12px;overflow:auto}
.card-placeholder{display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;min-height:330px;color:#64748b;gap:12px}.card-placeholder .icon-circle{width:58px;height:58px;border-radius:50%;background:#f1f5f9;display:flex;align-items:center;justify-content:center;font-size:1.7rem}
.card-content{display:none;flex-direction:column;gap:10px}.card-content.visible{display:flex}.card-badge{display:inline-block;padding:5px 12px;border-radius:20px;font-size:.78rem;font-weight:700;align-self:flex-start}.badge-mixta{background:#e8f0f8;color:#3a6591}.badge-plan{background:#fde8e4;color:#b5432c}.badge-mercado{background:#e4f5e8;color:#2d6a3f}.badge-desarrollo{background:#fef3e4;color:#8c5a2b}.card-title{font-size:1.35rem;font-weight:800;color:#1a2634;margin:0}.card-subtitle{font-size:.94rem;color:#5a6c7d;font-weight:600;margin:0}.card-type-label{font-size:.85rem;color:#7b8ea3;font-style:italic;margin:0}.card-section-label{font-size:.75rem;text-transform:uppercase;letter-spacing:.05em;font-weight:800;color:#8b9aab;margin:6px 0 0}.card-text{font-size:.9rem;color:#3d4f60;line-height:1.55;margin:0}.card-list{list-style:none;padding:0;display:flex;flex-wrap:wrap;gap:6px;margin:0}.card-list li{font-size:.8rem;background:#f5f7fa;padding:5px 10px;border-radius:16px;color:#3d4f60}.card-list.advantages li{background:#eef7ee;color:#2d5a30}.card-list.risks li{background:#fef4f1;color:#8c3a2b}.card-contrast{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:4px}.contrast-col{background:#f8fafb;border-radius:14px;padding:10px 12px;border:1px solid #e8ecf1}.contrast-col h4{font-size:.9rem;font-weight:800;margin:0 0 6px;color:#1a2634}.contrast-type{font-size:.72rem;font-weight:700;padding:3px 8px;border-radius:12px;display:inline-block;margin-bottom:6px}.contrast-col p{font-size:.78rem;color:#4a5c6b;line-height:1.4;margin:0 0 4px}.method-box{background:linear-gradient(135deg,#fff,#f8fafc);border:1px solid #e2e8f0;border-left:6px solid #4f8fbf;border-radius:18px;padding:16px 18px;margin:12px 0 18px;box-shadow:0 8px 22px rgba(15,23,42,.05)}.method-box p{margin:0 0 10px;color:#334155;line-height:1.55;font-size:.95rem}.method-box p:last-child{margin-bottom:0}.map-legend{display:flex;flex-wrap:wrap;gap:14px;justify-content:center;align-items:center;margin-top:12px;font-size:.78rem;color:#64748b}.legend-dot{display:inline-block;width:10px;height:10px;border-radius:50%;background:#e07342;margin-right:4px}.legend-dot.active-dot{background:#f2d4b0;border:1.5px solid #d4a574;width:12px;height:12px}
.country-chip-list{display:flex;flex-wrap:wrap;gap:6px;margin:0}.country-chip-list li{list-style:none;font-size:.78rem;background:#eef6fb;color:#1e3a5f;border:1px solid #dbeafe;border-radius:999px;padding:5px 10px}.card-help{font-size:.82rem;line-height:1.45;color:#64748b;background:#f8fafc;border:1px solid #e2e8f0;border-radius:12px;padding:9px 11px;margin:0}
@media(min-width:1300px){.cards{grid-template-columns:repeat(4,minmax(0,1fr))}.compare-box{grid-template-columns:1fr 1fr}.compare-row{grid-template-columns:220px 1fr 1fr}.method-box{display:grid;grid-template-columns:1fr 1.15fr 1fr;gap:14px}.method-box p{margin:0}.country-card{min-height:210px}}
@media(max-width:1100px){.real-map-grid{grid-template-columns:1fr}.real-map-container{min-height:300px}.region-info-card{min-height:auto}.card-contrast{grid-template-columns:1fr}}

@media(max-width:900px){.controls,.layout,.compare-box,.compare-row{grid-template-columns:1fr}.cards{grid-template-columns:1fr}.info-grid{grid-template-columns:1fr}header,main{padding-left:12px;padding-right:12px}.hero{padding:22px}.panel{padding:15px}}
</style>
</head>
<body>
<header>
  <div class="hero">
    <div class="kicker">Micro incentivos económicos y macro resultados</div>
    <h1>Desarrollo regional comparado</h1>
    <p>Explora economías por región, revisa qué producen, cómo producen, para quién producen y compara sus incentivos institucionales. La idea central: las economías fuertes no dependen solo de recursos naturales, sino de la alineación entre gobierno, empresas, universidades, talento, sindicatos, financiamiento y comunidades.</p>
  </div>
</header>
<main>
  <section class="panel">
    <div class="controls">
      <div>
        <label>Zona / región</label>
        <select id="regionSelect"></select>
      </div>
      <div>
        <label>País para comparar A</label>
        <select id="compareA"></select>
      </div>
      <div>
        <label>País para comparar B</label>
        <select id="compareB"></select>
      </div>
    </div>
    <div class="tabs" id="tabs"></div>
  </section>

  <section class="panel real-map-panel" style="margin-top:18px">
    <h2>Mapa interactivo por zonas</h2>
    <div class="method-box">
      <p><strong>¿De qué se trata?</strong> Este mapa permite explorar economías regionales a partir de casos representativos. Al seleccionar una zona directamente sobre el mapa, la app filtra los países disponibles, muestra sus contrastes económicos y permite compararlos por modelo económico, orientación del mercado, base productiva, incentivos y riesgos.</p>
      <p><strong>Metodología de selección de países:</strong> la muestra no incluye todos los países del mundo; se eligieron economías de mayor peso regional y economías con mayor vulnerabilidad estructural para contrastar trayectorias de desarrollo. La selección busca mostrar cómo la fortaleza institucional, la estructura productiva, la orientación del mercado y la alineación de incentivos entre agentes ayudan a explicar diferencias en crecimiento, innovación, desigualdad y sostenibilidad.</p>
      <p><strong>Cómo usarlo:</strong> señala o haz clic sobre cualquier país del mapa para activar su zona. La lista que aparece corresponde únicamente a los países incluidos en esta webapp para consulta y comparación.</p>
    </div>
    <div class="real-map-grid">
      <div class="real-map-container" id="mapContainer"></div>
      <div class="region-info-card" id="infoCard">
        <div class="card-placeholder" id="cardPlaceholder">
          <div class="icon-circle">🌍</div>
          <p>Selecciona una región del mapa para explorar cómo las reglas económicas cambian los incentivos y los resultados.</p>
        </div>
        <div class="card-content" id="cardContent"></div>
      </div>
    </div>
    <div class="map-legend">
      <span><span class="legend-dot active-dot"></span> Zona seleccionada</span>
      <span>| Haz clic directamente sobre los países del mapa</span>
    </div>
    <div id="countryCards" class="cards"></div>
  </section>

  <section class="panel compare" style="margin-top:18px">
    <h2>Comparador de países</h2>
    <p class="text">Elige dos países para comparar su modelo, orientación de mercado, desigualdad, base productiva, incentivos y riesgos.</p>
    <div id="compareOutput" class="compare-box"></div>
    <p class="footer-note">Nota: el Índice de Gini se consulta desde el indicador SI.POV.GINI del Banco Mundial cuando hay conexión a internet. 0 representa igualdad perfecta y 100 desigualdad máxima; los años disponibles pueden variar por país.</p>
  </section>
</main>

<script>
const data = [
{region:"América",country:"Estados Unidos",iso:"US",market:"Mercado interno masivo + servicios globales, tecnología, capital financiero y exportación de propiedad intelectual.",class:"Mayor peso regional",model:"Libre mercado hiperfinanciado",orientation:86,
pib:"$32.38 billones proyectado 2026",pc:"$94,430 proyectado 2026",population:"~340 millones",
produce:"Tecnología, servicios financieros, software, propiedad intelectual, manufactura avanzada, energía.",
how:"Capital de riesgo, universidades de élite, automatización, propiedad intelectual y mercados financieros profundos.",
forWhom:"Mercado interno de alto consumo, corporaciones globales e inversionistas internacionales.",
incentives:"Gobierno facilita emprendimiento y política industrial selectiva; empresas buscan disrupción; universidades transfieren patentes; talento global migra hacia altos salarios; bancos y fondos financian riesgo.",
risks:"Desigualdad, polarización, alto costo de salud y educación, deuda fiscal.",
opportunity:"Liderar IA, semiconductores y transición energética."},
{region:"América",country:"Canadá",iso:"CA",market:"Exportación hacia Norteamérica + mercado interno de alto poder adquisitivo; recursos, energía y minerales críticos.",class:"Mayor peso regional",model:"Economía mixta moderna",orientation:68,
pib:"$2.51 billones proyectado 2026",pc:"$60,305 proyectado 2026",population:"~40 millones",
produce:"Energía, minería, servicios financieros, manufactura automotriz, minerales críticos.",
how:"Capital intensivo, regulación predecible, seguridad social y tecnología de extracción avanzada.",
forWhom:"Exportación hacia Estados Unidos, hogares con alto poder adquisitivo y cadenas norteamericanas.",
incentives:"Gobierno reduce incertidumbre; empresas invierten con reglas estables; universidades forman talento; sindicatos dialogan; banca regulada ofrece crédito seguro.",
risks:"Dependencia de EE. UU., endeudamiento hipotecario y concentración exportadora.",
opportunity:"Minerales críticos para transición verde norteamericana."},
{region:"América",country:"Brasil",iso:"BR",market:"Mercado interno grande + exportación de commodities agroindustriales, minería y energía.",class:"Mayor peso regional",model:"Economía mixta proteccionista",orientation:55,
pib:"$2.64 billones proyectado 2026",pc:"$12,313 proyectado 2026",population:"~215 millones",
produce:"Agroindustria, minería, petróleo, servicios, aeronáutica y manufactura.",
how:"Escala, recursos naturales, bancos de desarrollo, agrotecnología y mano de obra masiva.",
forWhom:"Mercado interno grande y exportación de materias primas, especialmente hacia Asia.",
incentives:"Gobierno interviene con banca pública; empresas priorizan escala interna; universidades transfieren tecnología de manera irregular; banca privada cobra spreads altos.",
risks:"Burocracia, complejidad fiscal, desigualdad e informalidad.",
opportunity:"Bioenergía, bonos de carbono y agroindustria de mayor valor agregado."},
{region:"América",country:"México",iso:"MX",market:"Exportación manufacturera e integración a cadenas de Norteamérica + mercado interno de consumo y servicios.",class:"Mayor peso regional",model:"Libre mercado con controles estratégicos",orientation:72,
pib:"$2.12 billones proyectado 2026",pc:"$15,779 proyectado 2026",population:"~130 millones",
produce:"Manufactura automotriz, aeroespacial, electrónica, turismo, servicios y exportación industrial.",
how:"Mano de obra competitiva, integración con Norteamérica, nearshoring, parques industriales y disciplina fiscal.",
forWhom:"Cadenas de suministro de Norteamérica, exportación y mercado interno sostenido por consumo y remesas.",
incentives:"Gobierno da confianza macro pero invierte poco en I+D; multinacionales aprovechan logística; universidades técnicas alimentan parques industriales; talento se divide entre formalidad global e informalidad; bancos son sólidos pero adversos al riesgo.",
risks:"Informalidad, inseguridad, Estado de derecho, energía limpia y agua.",
opportunity:"Consolidar nearshoring y escalar hacia semiconductores, electromovilidad y manufactura avanzada."},
{region:"América",country:"Haití",iso:"HT",market:"Subsistencia local, remesas y ayuda externa + exportación textil básica residual.",class:"Vulnerabilidad estructural",model:"Colapso institucional",orientation:18,
pib:"~$3.08 mil millones",pc:"$2,142 2024",population:"~11.5 millones",
produce:"Agricultura de subsistencia, textil básico y remesas.",
how:"Mano de obra de baja especialización, infraestructura mínima, informalidad y supervivencia.",
forWhom:"Consumo básico local y exportaciones textiles residuales.",
incentives:"El gobierno no logra proveer seguridad; empresas evitan invertir; talento migra; banca es mínima; hogares dependen de remesas y ayuda.",
risks:"Violencia, colapso institucional, crisis humanitaria y ausencia de inversión.",
opportunity:"Reconstrucción institucional, seguridad e infraestructura básica."},

{region:"Europa",country:"Alemania",iso:"DE",market:"Exportación industrial de alta complejidad + mercado interno con Estado de bienestar.",class:"Mayor peso regional",model:"Economía social de mercado",orientation:62,
pib:"$5.45 billones proyectado 2026",pc:"$65,303 proyectado 2026",population:"~83 millones",
produce:"Maquinaria, vehículos, químicos, robótica e ingeniería de precisión.",
how:"Capital humano especializado, automatización, educación dual, Mittelstand e investigación aplicada.",
forWhom:"Exportación industrial global y bienestar interno con alta protección social.",
incentives:"Gobierno da estabilidad; empresas invierten en nichos; universidades y Fraunhofer transfieren tecnología; sindicatos cogestionan; banca regional financia largo plazo.",
risks:"Envejecimiento, rezago digital y transición energética costosa.",
opportunity:"Tecnologías limpias, hidrógeno e infraestructura verde."},
{region:"Europa",country:"Reino Unido",iso:"GB",market:"Servicios financieros, educación, tecnología y conocimiento orientados a mercados globales + consumo interno.",class:"Mayor peso regional",model:"Libre mercado financiarizado",orientation:80,
pib:"$4.26 billones proyectado 2026",pc:"$61,056 proyectado 2026",population:"~67 millones",
produce:"Finanzas, seguros, educación superior, servicios profesionales, tecnología, biotecnología y aeroespacial.",
how:"Capital intelectual, regulación financiera, universidades globales y mercado laboral flexible.",
forWhom:"Mercados financieros globales, corporaciones y consumidores de alto ingreso.",
incentives:"Gobierno busca ventajas post-Brexit; empresas priorizan retornos; universidades atraen talento mundial; sindicatos pesan menos fuera del sector público.",
risks:"Desigualdad regional, baja productividad media y fricciones comerciales.",
opportunity:"Hub europeo de IA, biotecnología e innovación científica."},
{region:"Europa",country:"Francia",iso:"FR",market:"Mercado interno protegido + exportación de bienes de lujo, aeroespacial, energía y turismo.",class:"Mayor peso regional",model:"Economía mixta con dirigismo",orientation:50,
pib:"$3.60 billones proyectado 2026",pc:"$52,083 proyectado 2026",population:"~68 millones",
produce:"Aeroespacial, energía nuclear, lujo, turismo, agroindustria y farmacéuticos.",
how:"Capital intensivo, marcas globales, campeones nacionales, subsidios selectivos y Estado coordinador.",
forWhom:"Mercado interno, consumidores globales de alto poder adquisitivo y sectores estratégicos nacionales.",
incentives:"Gobierno protege sectores estratégicos; empresas se benefician de diplomacia económica; universidades de élite nutren mandos; sindicatos presionan por protección.",
risks:"Déficit público, rigidez laboral, presión fiscal y conflictos sociales.",
opportunity:"Reindustrialización verde y autonomía estratégica europea."},
{region:"Europa",country:"Italia",iso:"IT",market:"Exportación de diseño, maquinaria, moda y turismo + mercado interno europeo.",class:"Mayor peso regional",model:"Economía mixta de distritos industriales",orientation:64,
pib:"$2.74 billones proyectado 2026",pc:"$46,505 proyectado 2026",population:"~59 millones",
produce:"Maquinaria, moda, diseño, turismo, bienes de lujo y componentes industriales.",
how:"Pymes familiares, talento artesanal, mecanización flexible y distritos industriales.",
forWhom:"Exportación europea/global, turismo y consumo interno.",
incentives:"Empresas innovan empíricamente; gobierno limitado por deuda; talento joven migra; banca financia pymes tras saneamiento.",
risks:"Deuda pública, burocracia judicial, envejecimiento y estancamiento demográfico.",
opportunity:"Fondos europeos y modernización institucional."},
{region:"Europa",country:"Moldavia",iso:"MD",market:"Ensamble subcontratado y agricultura para cadenas europeas + consumo local y remesas.",class:"Vulnerabilidad estructural",model:"Transición hacia mercado abierto",orientation:45,
pib:"$18.20 mil millones 2024",pc:"~$19,700 PPP",population:"~2.4 millones",
produce:"Agricultura, vino, textil, ensamble y componentes eléctricos.",
how:"Mano de obra barata, subcontratación, bajo capital tecnológico y mercado pequeño.",
forWhom:"Cadenas de Europa del Este y consumo básico local.",
incentives:"Gobierno busca anclaje UE; empresas evitan innovar por escala limitada; talento joven emigra; hogares dependen de remesas.",
risks:"Fuga demográfica, corrupción judicial y tensión geopolítica.",
opportunity:"Integración con la Unión Europea e infraestructura energética con Rumania."},

{region:"Asia",country:"China",iso:"CN",market:"Exportación manufacturera y tecnológica global + mercado interno de escala continental.",class:"Mayor peso regional",model:"Capitalismo de Estado",orientation:38,
pib:"$20.85 billones proyectado 2026",pc:"$14,874 proyectado 2026",population:"~1,400 millones",
produce:"Manufactura global, vehículos eléctricos, paneles solares, telecomunicaciones, baterías y maquinaria.",
how:"Escala, automatización, subsidios, planeación estatal, infraestructura masiva y financiamiento dirigido.",
forWhom:"Mercado global, corporaciones internacionales y mercado interno creciente.",
incentives:"Estado dirige sectores; empresas compiten bajo vigilancia; universidades producen tecnólogos; bancos financian metas territoriales; hogares ahorran por precaución.",
risks:"Sobrecapacidad, deuda local, tensiones tecnológicas con EE. UU. y consumo interno débil.",
opportunity:"Liderar energías limpias, electromovilidad y cadenas tecnológicas."},
{region:"Asia",country:"Japón",iso:"JP",market:"Exportación de manufactura avanzada y componentes de precisión + mercado interno maduro.",class:"Mayor peso regional",model:"Economía mixta corporativa",orientation:64,
pib:"$4.38 billones proyectado 2026",pc:"$35,703 proyectado 2026",population:"~124 millones",
produce:"Automotriz, robótica industrial, óptica, electrónica, acero de precisión y semiconductores.",
how:"Perfeccionamiento continuo, robotización, capital corporativo estable y redes Keiretsu.",
forWhom:"Cadenas globales de alta precisión y mercado interno maduro.",
incentives:"Gobierno sostiene estabilidad; empresas prefieren seguridad; talento enfrenta jerarquías rígidas; banca opera con bajo costo de capital.",
risks:"Envejecimiento, baja natalidad y escasez de insumos.",
opportunity:"Robótica para cuidados, salud y envejecimiento global."},
{region:"Asia",country:"India",iso:"IN",market:"Servicios TI globales + mercado interno masivo + agricultura de subsistencia.",class:"Mayor peso regional",model:"Economía mixta con herencia planificada",orientation:55,
pib:"$4.15 billones proyectado 2026",pc:"$2,813 proyectado 2026",population:"~1,430 millones",
produce:"Servicios TI, software, farmacéuticos genéricos, agricultura, textiles y manufactura emergente.",
how:"Mano de obra masiva, talento técnico global, subsidios dirigidos, plataformas digitales y regulación compleja.",
forWhom:"Corporaciones globales, mercado interno enorme y agricultura de subsistencia.",
incentives:"Gobierno impulsa chips y manufactura; élite tecnológica exporta servicios; universidades top conviven con educación básica débil; talento busca empleo formal escaso.",
risks:"Infraestructura, empleo juvenil, baja participación femenina e informalidad.",
opportunity:"Estrategia China+1, manufactura y servicios digitales globales."},
{region:"Asia",country:"Corea del Sur",iso:"KR",market:"Exportación de semiconductores, hardware, autos y cultura global + consumo interno sofisticado.",class:"Mayor peso regional",model:"Libre mercado con coordinación estatal-chaebol",orientation:70,
pib:"$1.93 billones proyectado 2026",pc:"$37,412 proyectado 2026",population:"~51 millones",
produce:"Semiconductores, electrónica, astilleros, vehículos, petroquímica y cultura global.",
how:"I+D masiva, chaebols, educación exigente, crédito dirigido y manufactura de frontera.",
forWhom:"Mercado global de hardware, electrónica, autos y entretenimiento.",
incentives:"Gobierno apoya conglomerados; empresas absorben talento; sindicatos corporativos negocian; hogares soportan altos costos educativos e inmobiliarios.",
risks:"Crisis demográfica, dependencia comercial de China/EE. UU. y concentración empresarial.",
opportunity:"Ser proveedor estratégico de semiconductores y tecnología avanzada."},
{region:"Asia",country:"Afganistán",iso:"AF",market:"Subsistencia local, comercio regional básico, remesas e informalidad.",class:"Vulnerabilidad estructural",model:"Economía colapsada y aislada",orientation:8,
pib:"Sin datos macro proyectables",pc:"~$448 nominal proyectado 2025",population:"~42 millones",
produce:"Agricultura básica, ganadería, comercio fronterizo y frutos secos.",
how:"Subsistencia, informalidad, infraestructura casi nula y exclusión financiera.",
forWhom:"Supervivencia local, comercio regional e ingreso vía remesas.",
incentives:"Gobierno bloquea inclusión; empresas no invierten; talento migra; mujeres son excluidas; banca queda aislada.",
risks:"Hambruna, aislamiento, exclusión educativa y pobreza extrema.",
opportunity:"Reintegración logística regional y restauración de derechos civiles y educativos."},

{region:"África",country:"Sudáfrica",iso:"ZA",market:"Exportación de minerales y autos + servicios financieros regionales + mercado interno profundamente desigual.",class:"Mayor peso regional",model:"Economía mixta dual",orientation:58,
pib:"$400.26 mil millones proyectado 2025",pc:"$7,500 proyectado 2026",population:"~60 millones",
produce:"Minería, platino, oro, automotriz, finanzas, telecomunicaciones y servicios.",
how:"Capital intensivo en enclaves, bolsa desarrollada, tecnología minera y sector informal amplio.",
forWhom:"Industria global, mercados financieros y consumo interno desigual.",
incentives:"Empresas invierten para sobrevivir a fallas estatales; universidades forman talento selecto; sindicatos son fuertes; banca es profunda.",
risks:"Apagones, logística portuaria, desempleo juvenil y desigualdad extrema.",
opportunity:"Minerales críticos para transición verde y reforma de infraestructura."},
{region:"África",country:"Egipto",iso:"EG",market:"Mercado interno grande + turismo, logística del Canal de Suez, gas y fertilizantes para exportación.",class:"Mayor peso regional",model:"Capitalismo estatal/militarizado",orientation:34,
pib:"$389.06 mil millones proyectado 2025",pc:"$3,900 proyectado 2026",population:"~110 millones",
produce:"Gas, logística del Canal de Suez, turismo, agricultura y fertilizantes.",
how:"Mano de obra barata, megaproyectos con deuda, control estatal y recursos hídricos limitados.",
forWhom:"Mercado interno grande, turismo europeo/árabe y rutas globales.",
incentives:"Gobierno y corporaciones militares dominan licitaciones; empresas privadas enfrentan competencia estatal; talento migra al Golfo.",
risks:"Deuda, escasez de divisas, agua, subsidios y desempleo juvenil.",
opportunity:"Nodo energético y logístico para Europa y Medio Oriente."},
{region:"África",country:"Nigeria",iso:"NG",market:"Mercado interno masivo + exportación petrolera + servicios digitales y cultura creativa.",class:"Mayor peso regional",model:"Economía mixta distorsionada e informal",orientation:53,
pib:"~$252.26 mil millones 2024",pc:"$1,556 proyectado 2026",population:"~220 millones",
produce:"Petróleo, agricultura, fintech, telecomunicaciones, cultura creativa y servicios.",
how:"Emprendimiento informal, recursos petroleros, talento creativo, infraestructura débil y digitalización móvil.",
forWhom:"Mercado interno masivo, exportación petrolera y consumidores urbanos.",
incentives:"Empresas fintech resuelven fallas de Estado; talento emigra; bancos y plataformas digitales expanden inclusión.",
risks:"Corrupción, inseguridad, dependencia petrolera, inflación y falta de infraestructura.",
opportunity:"Hub fintech, mercado joven y salto digital regional."},
{region:"África",country:"Burundi",iso:"BI",market:"Consumo de subsistencia + exportaciones primarias agrícolas y mineras de bajo valor agregado.",class:"Vulnerabilidad estructural",model:"Preindustrial e informal",orientation:20,
pib:"$8.14 mil millones proyectado 2026",pc:"$546 proyectado 2026",population:"~13 millones",
produce:"Agricultura de subsistencia, café, té, oro y estaño.",
how:"Trabajo manual, minifundios, baja infraestructura, poca banca y baja tecnología.",
forWhom:"Consumo familiar, exportaciones primarias mínimas y supervivencia rural.",
incentives:"Gobierno recauda poco; productores no innovan por falta de crédito; jóvenes quedan vulnerables; hogares dependen del clima.",
risks:"Aislamiento geográfico, pobreza, fragilidad política y shocks climáticos.",
opportunity:"Infraestructura hacia puertos e inversión agrícola básica."},
{region:"África",country:"Somalia",iso:"SO",market:"Ganadería regional, remesas, dinero móvil y mercados locales informales.",class:"Vulnerabilidad estructural",model:"Laissez-faire extremo sin Estado fuerte",orientation:25,
pib:"$14.17 mil millones proyectado 2026",pc:"$764 proyectado 2025",population:"~18 millones",
produce:"Ganadería, telecomunicaciones, remesas, comercio informal y pesca potencial.",
how:"Mercados flexibles, clan, servicios móviles privados y débil infraestructura estatal.",
forWhom:"Mercado local, diáspora, consumidores regionales de ganado y hogares dependientes de remesas.",
incentives:"Empresas privadas suplen vacíos del Estado; gobierno avanza lento; hogares usan remesas y dinero móvil.",
risks:"Sequía, violencia, insurgencia y debilidad institucional.",
opportunity:"Telecomunicaciones, pesca, puertos y reconstrucción federal."},

{region:"Oceanía",country:"Australia",iso:"AU",market:"Exportación de minerales, energía y educación hacia Asia + mercado interno de alto ingreso.",class:"Mayor peso regional",model:"Libre mercado regulado transparente",orientation:76,
pib:"$2.12 billones proyectado 2026",pc:"$75,648 proyectado 2026",population:"~26 millones",
produce:"Minería, gas, hierro, educación internacional, turismo y agricultura.",
how:"Mega minería automatizada, gobernanza transparente, regalías, banca estable y migración calificada.",
forWhom:"Asia industrial, estudiantes internacionales, hogares de alto ingreso y mercados de commodities.",
incentives:"Gobierno captura regalías; empresas invierten con certeza jurídica; universidades exportan educación; bancos financian vivienda y capital.",
risks:"Dependencia de China/Asia, vivienda cara y exposición climática.",
opportunity:"Litio, minerales críticos y cadenas verdes occidentales."},
{region:"Oceanía",country:"Nueva Zelanda",iso:"NZ",market:"Agroindustria premium, turismo y servicios para consumidores globales + mercado interno pequeño.",class:"Mayor peso regional",model:"Libre mercado abierto con regulación ecológica",orientation:78,
pib:"~$260 mil millones 2024",pc:"$49,472 proyectado 2026",population:"~5 millones",
produce:"Lácteos premium, carne, vino, turismo, biotecnología agrícola y software emergente.",
how:"Agrotecnología, cooperativas, baja protección arancelaria, marca natural y sostenibilidad.",
forWhom:"Consumidores premium en Asia, turismo internacional y mercado interno pequeño.",
incentives:"Estado minimiza distorsiones; granjas cooperan; universidades transfieren ciencia agrícola; talento puede migrar a Australia.",
risks:"Aislamiento, costos logísticos, vivienda y dependencia agroexportadora.",
opportunity:"Software, cine, economía creativa y capital de riesgo."},
{region:"Oceanía",country:"Papúa Nueva Guinea",iso:"PG",market:"Enclaves extractivos para Asia + economías locales de subsistencia.",class:"Mayor peso regional insular",model:"Economía dual de enclave",orientation:42,
pib:"$31.80 mil millones 2024",pc:"~$3,000",population:"~10 millones",
produce:"Gas natural, oro, metales, madera, café y agricultura de subsistencia.",
how:"Enclaves extractivos de capital extranjero y economías tribales o comunitarias de baja conectividad.",
forWhom:"Mercados asiáticos de recursos y comunidades locales de subsistencia.",
incentives:"Gobierno captura regalías; empresas extractivas operan aisladas; comunidades reclaman tierras; banca no llega al interior.",
risks:"Conflictos por recursos, infraestructura débil y exclusión local.",
opportunity:"Usar rentas extractivas para infraestructura y capacitación local."},
{region:"Oceanía",country:"Tuvalu",iso:"TV",market:"Rentas por licencias pesqueras, dominio .tv, fondos externos y consumo importado.",class:"Vulnerabilidad estructural",model:"Rentismo soberano micro-dependiente",orientation:30,
pib:"$65 millones proyectado 2025",pc:"$6,543 proyectado 2025",population:"~9,853",
produce:"Licencias pesqueras, dominio .tv, empleo público y pesca local.",
how:"Rentas legales y marítimas, importaciones, fondos fiduciarios y moneda extranjera.",
forWhom:"Sostener al Estado, hogares locales y consumo importado.",
incentives:"Gobierno administra rentas; pymes casi no escalan; talento migra; hogares dependen de remesas y fondos.",
risks:"Cambio climático, nivel del mar, aislamiento y nula base productiva.",
opportunity:"Diplomacia climática, economía digital y protección migratoria."},
{region:"Oceanía",country:"Kiribati",iso:"KI",market:"Rentas pesqueras, copra y empresas estatales + consumo básico importado.",class:"Vulnerabilidad estructural",model:"Estado distribuidor de rentas",orientation:33,
pib:"$343 millones 2024",pc:"$2,695 2024",population:"~127,317",
produce:"Licencias atuneras, copra, pesca y empresas estatales.",
how:"Rentas pesqueras, fondos soberanos, empresas públicas y dispersión geográfica.",
forWhom:"Estado, importaciones básicas, comunidades insulares y flotas externas.",
incentives:"Gobierno administra fondos; empresas públicas dominan; pymes tienen poco financiamiento; trabajadores migran.",
risks:"Cambio climático, ineficiencia estatal, dispersión insular y baja capacitación.",
opportunity:"Agregar valor pesquero y reformar empresas públicas."}
];

const regions = ["Todas","América","Europa","Asia","África","Oceanía"];
const regionSelect = document.getElementById("regionSelect");
const tabs = document.getElementById("tabs");
const countryCards = document.getElementById("countryCards");
const compareA = document.getElementById("compareA");
const compareB = document.getElementById("compareB");
const compareOutput = document.getElementById("compareOutput");
let currentRegion = "Todas";

function init(){
  regionSelect.innerHTML = regions.map(r=>`<option>${r}</option>`).join("");
  tabs.innerHTML = regions.map(r=>`<button class="tab ${r==='Todas'?'active':''}" data-region="${r}">${r}</button>`).join("");
  const opts = data.map((d,i)=>`<option value="${i}">${d.country} · ${d.region}</option>`).join("");
  compareA.innerHTML = opts;
  compareB.innerHTML = opts;
  compareB.selectedIndex = Math.min(3, data.length-1);
  renderCards();
  renderCompare();
  bind();
}
function bind(){
  regionSelect.addEventListener("change",e=>setRegion(e.target.value));
  tabs.querySelectorAll(".tab").forEach(t=>t.addEventListener("click",()=>setRegion(t.dataset.region)));
  compareA.addEventListener("change",renderCompare);
  compareB.addEventListener("change",renderCompare);
}
function setRegion(r){
  currentRegion=r;
  regionSelect.value=r;
  tabs.querySelectorAll(".tab").forEach(t=>t.classList.toggle("active",t.dataset.region===r));
  document.querySelectorAll(".region").forEach(el=>el.classList.toggle("active",el.dataset.region===r));
  document.querySelectorAll(".pin").forEach(el=>el.classList.toggle("active",el.dataset.region===r));
  renderCards();
}
function filtered(){
  return currentRegion==="Todas" ? data : data.filter(d=>d.region===currentRegion);
}
function renderCards(){
  countryCards.innerHTML = filtered().map((d,i)=>`
    <div class="country-card" onclick="selectForCompare('${d.country.replace(/'/g,"\\'")}')">
      <strong>${d.country}</strong>
      <div class="badges">
        <span class="badge">${d.region}</span>
        <span class="badge ${d.class.includes('Vulnerabilidad')?'weak':'strong'}">${d.class}</span>
      </div>
      <div class="text" style="font-size:14px">${d.model}</div>
      <div class="text" style="font-size:13px;margin-top:10px;color:#64748b">Haz clic para usarlo como País A en el comparador.</div>
    </div>`).join("");
}
function selectForCompare(c){
  const idx=data.findIndex(d=>d.country===c);
  if(idx>=0){ compareA.value=idx; renderCompare(); document.querySelector(".compare").scrollIntoView({behavior:"smooth"}); }
}
window.selectForCompare=selectForCompare;

const giniCache = {};
function giniLevel(value){
  if(value===null || value===undefined || isNaN(value)) return {label:"N/D", cls:"gini-na", text:"Sin dato comparable"};
  if(value < 30) return {label:"Bajo", cls:"gini-low", text:"desigualdad baja"};
  if(value < 40) return {label:"Medio", cls:"gini-med", text:"desigualdad media"};
  if(value < 50) return {label:"Alto", cls:"gini-high", text:"desigualdad alta"};
  return {label:"Muy alto", cls:"gini-vhigh", text:"desigualdad muy alta"};
}
function renderGini(g){
  if(!g || g.value===null || g.value===undefined) return `<span class="gini-tag gini-na">N/D</span> <span style="color:#64748b;font-size:13px">Sin dato comparable reciente en Banco Mundial</span>`;
  const lvl=giniLevel(+g.value);
  return `<span class="gini-tag ${lvl.cls}">${(+g.value).toFixed(1)} · ${lvl.label}</span> <span style="color:#64748b;font-size:13px">año ${g.year}; ${lvl.text}</span>`;
}
async function getGini(d){
  if(giniCache[d.iso]) return giniCache[d.iso];
  try{
    const url=`https://api.worldbank.org/v2/country/${d.iso}/indicator/SI.POV.GINI?format=json&per_page=70`;
    const res=await fetch(url);
    const json=await res.json();
    const rows=Array.isArray(json) && json[1] ? json[1] : [];
    const row=rows.find(x=>x.value!==null && x.value!==undefined);
    giniCache[d.iso]= row ? {value:row.value, year:row.date} : {value:null, year:null};
  }catch(e){
    giniCache[d.iso]={value:null, year:null};
  }
  return giniCache[d.iso];
}
function updateGiniElement(d, id){
  const el=document.getElementById(id);
  if(!el) return;
  el.innerHTML='Consultando Banco Mundial...';
  getGini(d).then(g=>{ const target=document.getElementById(id); if(target) target.innerHTML=renderGini(g); });
}

function cardCompare(d){
 return `<div class="compare-card">
  <h3 style="margin-top:0;color:#1e3a5f">${d.country}</h3>
  <div class="badges"><span class="badge">${d.region}</span><span class="badge">${d.model}</span></div>
  <p class="text"><b>Orientación:</b> ${d.market}</p>
  <p class="text"><b>Gini:</b> <span id="gini-card-${d.iso}">Consultando...</span></p>
  <p class="text"><b>Produce:</b> ${d.produce}</p>
  <p class="text"><b>Riesgo clave:</b> ${d.risks}</p>
 </div>`;
}
function renderCompare(){
 const a=data[+compareA.value], b=data[+compareB.value];
 compareOutput.innerHTML = cardCompare(a)+cardCompare(b)+`
 <div style="grid-column:1/-1;margin-top:8px">
  <div class="compare-row"><div class="label">Modelo</div><div>${a.model}</div><div>${b.model}</div></div>
  <div class="compare-row"><div class="label">Orientación del mercado</div><div>${a.market}<div class="meter"><span style="width:${a.orientation}%"></span></div></div><div>${b.market}<div class="meter"><span style="width:${b.orientation}%"></span></div></div></div>
  <div class="compare-row"><div class="label">Índice de Gini</div><div id="gini-row-a-${a.iso}">Consultando Banco Mundial...</div><div id="gini-row-b-${b.iso}">Consultando Banco Mundial...</div></div>
  <div class="compare-row"><div class="label">Qué produce</div><div>${a.produce}</div><div>${b.produce}</div></div>
  <div class="compare-row"><div class="label">Cómo produce</div><div>${a.how}</div><div>${b.how}</div></div>
  <div class="compare-row"><div class="label">Para quién</div><div>${a.forWhom}</div><div>${b.forWhom}</div></div>
  <div class="compare-row"><div class="label">Incentivos</div><div>${a.incentives}</div><div>${b.incentives}</div></div>
  <div class="compare-row"><div class="label">Riesgos</div><div>${a.risks}</div><div>${b.risks}</div></div>
  <div class="compare-row"><div class="label">Oportunidad</div><div>${a.opportunity}</div><div>${b.opportunity}</div></div>
 </div>`;
 updateGiniElement(a, `gini-card-${a.iso}`);
 updateGiniElement(b, `gini-card-${b.iso}`);
 updateGiniElement(a, `gini-row-a-${a.iso}`);
 updateGiniElement(b, `gini-row-b-${b.iso}`);
}

const regionData = {
  'América': {
    nombre:'América', caso:'Estados Unidos, Canadá, Brasil, México y economías vulnerables del Caribe/Centroamérica', tipoSistema:'Contraste entre libre mercado, economías mixtas exportadoras y fragilidad institucional', tipoBadge:'mixta',
    reglasDominantes:'Mercado, tratados comerciales, inversión privada, política industrial selectiva, regulación parcial y programas públicos.',
    incentivos:'Exportación, manufactura, tecnología, remesas, consumo interno, inversión extranjera y búsqueda de estabilidad macroeconómica.',
    ventajas:['Integración comercial','Manufactura y nearshoring','Mercados financieros','Agroindustria','Potencial tecnológico'],
    riesgos:['Informalidad','Desigualdad regional','Inseguridad','Dependencia de commodities o remesas','Brechas institucionales'],
    descripcion:'La región muestra grandes contrastes: economías de frontera tecnológica, economías mixtas exportadoras y países con fuerte vulnerabilidad institucional.'
  },
  'Europa': {
    nombre:'Europa', caso:'Alemania, Reino Unido, Francia, Italia y economías en transición', tipoSistema:'Economías mixtas avanzadas con distintos niveles de bienestar y mercado', tipoBadge:'mixta',
    reglasDominantes:'Mercado competitivo, regulación fuerte, protección social, integración regional y políticas industriales selectivas.',
    incentivos:'Innovación, productividad, derechos laborales, exportación, estabilidad institucional y formación técnica.',
    ventajas:['Alta productividad','Instituciones sólidas','Estado de bienestar','Investigación aplicada','Exportación industrial'],
    riesgos:['Envejecimiento poblacional','Burocracia','Deuda pública','Brechas regionales','Presión fiscal'],
    descripcion:'Europa permite comparar modelos de economía social de mercado, liberalización financiera, dirigismo estatal y economías en transición.'
  },
  'Asia': {
    nombre:'Asia', caso:'China, Japón, India, Corea del Sur y economías frágiles', tipoSistema:'Contraste entre capitalismo de Estado, economías mixtas exportadoras y fragilidad institucional', tipoBadge:'desarrollo', esContraste:true,
    contraste1:{titulo:'China', tipo:'Capitalismo de Estado', reglas:'Planeación estratégica, subsidios, banca dirigida y competencia controlada.', incentivos:'Metas estatales, escala, exportación y dominio tecnológico.', ventajas:'Movilización industrial rápida y cadenas completas.', riesgos:'Sobrecapacidad, deuda, control político y tensiones geopolíticas.'},
    contraste2:{titulo:'Corea/Japón/India', tipo:'Economías mixtas y tecnológicas', reglas:'Mercado, Estado estratégico, conglomerados, servicios TI e innovación.', incentivos:'Exportación, talento técnico, I+D y productividad.', ventajas:'Tecnología, manufactura avanzada y servicios globales.', riesgos:'Demografía, desigualdad, informalidad o presión laboral.'},
    descripcion:'Asia concentra algunos de los contrastes más fuertes: planificación estratégica, exportación tecnológica, servicios digitales y economías frágiles por conflicto o aislamiento.'
  },
  'África': {
    nombre:'África', caso:'Sudáfrica, Egipto, Argelia, Nigeria y estados frágiles', tipoSistema:'Economías mixtas, extractivas y frágiles', tipoBadge:'desarrollo',
    reglasDominantes:'Mercados locales, Estado, cooperación internacional, sectores extractivos y alta informalidad.',
    incentivos:'Recursos naturales, empleo informal, emprendimiento local, ayuda externa, rentas de commodities y servicios digitales emergentes.',
    ventajas:['Población joven','Recursos naturales','Potencial Fintech','Mercados urbanos','Energía y minería'],
    riesgos:['Debilidad institucional','Conflictos','Vulnerabilidad climática','Infraestructura deficiente','Desigualdad'],
    descripcion:'La región combina economías con peso regional, sectores extractivos, servicios emergentes y países con fragilidad institucional severa.'
  },
  'Oceanía': {
    nombre:'Oceanía', caso:'Australia, Nueva Zelanda, Papúa Nueva Guinea, Fiyi y economías insulares', tipoSistema:'Libre mercado regulado, economías duales y rentismo insular', tipoBadge:'mixta',
    reglasDominantes:'Mercado, regulación ambiental, servicios públicos, apertura comercial, recursos naturales y rentas soberanas insulares.',
    incentivos:'Exportación de recursos, turismo, educación, estabilidad institucional, adaptación climática y servicios externos.',
    ventajas:['Calidad institucional','Recursos naturales','Turismo','Educación internacional','Calidad de vida'],
    riesgos:['Aislamiento geográfico','Cambio climático','Dependencia exportadora','Pequeña escala','Fuga de talento'],
    descripcion:'Oceanía permite comparar economías reguladas de altos ingresos con economías insulares vulnerables a clima, escala y dependencia externa.'
  }
};

function normalizarRegionMapa(key){
  const aliases = {
    'america-norte':'América',
    'america-sur':'América',
    'america':'América',
    'América':'América',
    'Europa':'Europa',
    'europa':'Europa',
    'Asia':'Asia',
    'asia':'Asia',
    'África':'África',
    'Africa':'África',
    'africa':'África',
    'Oceanía':'Oceanía',
    'Oceania':'Oceanía',
    'oceania':'Oceanía'
  };
  return aliases[key] || key;
}

function paisesIncluidosPorRegion(region){
  return data.filter(d => d.region === region).map(d => d.country);
}

function listaPaisesHTML(region){
  const paises = paisesIncluidosPorRegion(region);
  if(!paises.length) return '';
  return `<p class="card-section-label">Países incluidos en esta zona</p><p class="card-help">Esta lista corresponde a los países disponibles en la webapp para consultar y comparar; no representa todos los países de la región.</p><ul class="country-chip-list">${paises.map(p=>`<li>${p}</li>`).join('')}</ul>`;
}

function mostrarDatosContinente(key){
  const region = normalizarRegionMapa(key);
  if(regions.includes(region)) setRegion(region);

  if(typeof d3 !== 'undefined'){
    d3.selectAll('.pais').classed('active-map', false);
    d3.selectAll('.pais').filter(function(){ return d3.select(this).attr('data-region') === region; }).classed('active-map', true);
  }

  const dataRegion = regionData[region];
  if(!dataRegion) return;
  const placeholder = document.getElementById('cardPlaceholder');
  const cardContent = document.getElementById('cardContent');
  if(!placeholder || !cardContent) return;
  placeholder.style.display='none';
  cardContent.style.display='flex';
  cardContent.classList.add('visible');

  const paisesHTML = listaPaisesHTML(region);
  if(dataRegion.esContraste){
    cardContent.innerHTML = `<span class="card-badge badge-mixta">Contraste de sistemas</span><h3 class="card-title">${dataRegion.nombre}</h3><p class="card-subtitle">${dataRegion.caso}</p><p class="card-type-label">${dataRegion.tipoSistema}</p><p class="card-text">${dataRegion.descripcion}</p><div class="card-contrast"><div class="contrast-col"><h4>${dataRegion.contraste1.titulo}</h4><span class="contrast-type badge-plan">${dataRegion.contraste1.tipo}</span><p><strong>Reglas:</strong> ${dataRegion.contraste1.reglas}</p><p><strong>Incentivos:</strong> ${dataRegion.contraste1.incentivos}</p><p><strong>Ventajas:</strong> ${dataRegion.contraste1.ventajas}</p><p><strong>Riesgos:</strong> ${dataRegion.contraste1.riesgos}</p></div><div class="contrast-col"><h4>${dataRegion.contraste2.titulo}</h4><span class="contrast-type badge-mercado">${dataRegion.contraste2.tipo}</span><p><strong>Reglas:</strong> ${dataRegion.contraste2.reglas}</p><p><strong>Incentivos:</strong> ${dataRegion.contraste2.incentivos}</p><p><strong>Ventajas:</strong> ${dataRegion.contraste2.ventajas}</p><p><strong>Riesgos:</strong> ${dataRegion.contraste2.riesgos}</p></div></div>${paisesHTML}`;
  }else{
    const badgeClass = dataRegion.tipoBadge === 'mixta' ? 'badge-mixta' : 'badge-desarrollo';
    cardContent.innerHTML = `<span class="card-badge ${badgeClass}">${dataRegion.tipoSistema}</span><h3 class="card-title">${dataRegion.nombre}</h3><p class="card-subtitle">Caso: ${dataRegion.caso}</p><p class="card-text">${dataRegion.descripcion}</p><p class="card-section-label">Reglas dominantes</p><p class="card-text">${dataRegion.reglasDominantes}</p><p class="card-section-label">Incentivos principales</p><p class="card-text">${dataRegion.incentivos}</p><p class="card-section-label">✅ Ventajas</p><ul class="card-list advantages">${dataRegion.ventajas.map(v=>`<li>${v}</li>`).join('')}</ul><p class="card-section-label">⚠️ Riesgos</p><ul class="card-list risks">${dataRegion.riesgos.map(r=>`<li>${r}</li>`).join('')}</ul>${paisesHTML}`;
  }
}
window.mostrarDatosContinente = mostrarDatosContinente;

function initRealMap(){
  const container = document.getElementById('mapContainer');
  if(!container) return;
  const width = Math.max(container.clientWidth || 700, 700);
  const height = 420;
  if(typeof d3 === 'undefined' || typeof topojson === 'undefined'){
    container.innerHTML = '<div class="empty">No se pudieron cargar D3.js o TopoJSON. Revisa tu conexión a internet.</div>';
    return;
  }
  const projection = d3.geoMercator().scale(width/6.25).translate([width/2, height/1.72]);
  const pathGenerator = d3.geoPath().projection(projection);
  const svg = d3.select('#mapContainer').append('svg').attr('viewBox', `0 0 ${width} ${height}`).attr('preserveAspectRatio','xMidYMid meet').attr('aria-label','Mapa mundial con geografía real');
  svg.append('rect').attr('width',width).attr('height',height).attr('fill','#eef2f6');
  const gPaises = svg.append('g').attr('class','paises');
  d3.json('https://unpkg.com/world-atlas@2/countries-110m.json').then(function(topoData){
    const geoJson = topojson.feature(topoData, topoData.objects.countries);
    gPaises.selectAll('path').data(geoJson.features).enter().append('path')
      .attr('d', pathGenerator).attr('class','pais')
      .attr('data-region', d => determinarRegionPorCoordenadas(d))
      .on('click', function(event,d){ const r=d3.select(this).attr('data-region'); if(r) mostrarDatosContinente(r); })
      .append('title').text(d => d.properties.name);
  }).catch(function(){ container.innerHTML = '<div class="empty">No se pudieron cargar los datos geográficos. Verifica tu conexión a internet.</div>'; });
}
function determinarRegionPorCoordenadas(feature){
  // Usamos coordenadas geográficas reales del centroide del país en vez de la posición proyectada.
  // Esto corrige el bug de regiones mal clasificadas al hacer clic sobre el mapa.
  const [lon, lat] = d3.geoCentroid(feature);
  const name = feature && feature.properties ? feature.properties.name : '';

  // Casos especiales para países transcontinentales o islas que el centroide puede ubicar de forma ambigua.
  const europeSpecial = ['Russia','Turkey','Cyprus','Iceland','Greenland'];
  const oceaniaSpecial = ['Australia','New Zealand','Papua New Guinea','Fiji','Solomon Islands','Vanuatu','New Caledonia'];
  const africaSpecial = ['Egypt','South Africa','Nigeria','Algeria','Morocco','Tunisia','Libya','Sudan','South Sudan','Somalia','Ethiopia','Kenya','Ghana','Burundi','Central African Republic'];
  const asiaSpecial = ['China','India','Japan','South Korea','North Korea','Afghanistan','Yemen','Nepal','Tajikistan','Singapore','Malaysia','Indonesia','Philippines','Vietnam','Thailand'];

  if(oceaniaSpecial.includes(name)) return 'Oceanía';
  if(africaSpecial.includes(name)) return 'África';
  if(asiaSpecial.includes(name)) return 'Asia';
  if(europeSpecial.includes(name)) return name === 'Russia' ? 'Asia' : 'Europa';

  // Reglas generales por latitud/longitud.
  if(lon < -30) return 'América';
  if(lon >= -30 && lon <= 60 && lat >= 35) return 'Europa';
  if(lon >= -20 && lon <= 55 && lat < 35 && lat > -40) return 'África';
  if(lon > 55 && lon < 180 && lat > -15) return 'Asia';
  if(lon > 95 && lat <= -10) return 'Oceanía';
  return null;
}

init();
initRealMap();
</script>
</body>
</html>
