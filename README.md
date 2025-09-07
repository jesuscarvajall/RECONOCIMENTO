<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Reconocimiento - Competencias Blandas</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root{ --gold:#d4af37; --ink:#222; --bg:#f3f3f3; --linkedin:#0077B5 }
    *{ box-sizing:border-box }
    body{
      font-family:Georgia,serif; text-align:center; background:var(--bg);
      margin:0; padding:0 16px 40px; color:var(--ink);
    }
    h1{ margin-top:20px }
    .controls{
      margin:8px auto 0; display:flex; gap:8px; justify-content:center;
      flex-wrap:wrap; max-width:720px
    }
    input,button{ padding:10px 14px; font-size:16px; border-radius:6px; border:1px solid #ccc; outline:none }
    button{ cursor:pointer }

    #certificado{
      width:900px; max-width:100%; margin:24px auto; background:#fff; border:8px solid var(--gold);
      padding:42px 36px; border-radius:14px; box-shadow:0 10px 24px rgba(0,0,0,.15);
      display:none; position:relative; overflow:hidden
    }
    #certificado:before{
      content:""; position:absolute; inset:14px; border:2px solid rgba(212,175,55,.35);
      border-radius:10px; pointer-events:none
    }
    .marca{ letter-spacing:.18em; font-size:12px; color:#666; margin-bottom:8px; text-transform:uppercase }
    .titulo-doc{ font-size:28px; margin:2px 0 10px; font-weight:700 }
    .sub{ font-size:18px; margin:0 0 8px; color:#444 }
    .nombre{ font-size:34px; font-weight:700; margin:6px 0 4px }
    .fecha{ font-size:14px; color:#555; margin-bottom:12px; }
    .leyenda{ font-size:16px; margin:4px 0 18px }

    /* Insignias (emoji) */
    .insignias{ display:flex; justify-content:center; gap:10px; flex-wrap:wrap }
    .insignia{
      width:72px; height:72px; border-radius:50%; display:inline-flex; align-items:center; justify-content:center;
      border:2px solid #eee; box-shadow:0 2px 6px rgba(0,0,0,.08); font-size:40px
    }

    /* —— Selector de tickets —— */
    .ticket-controls{
      margin: 14px auto 4px;
      display: flex;
      gap: 10px;
      justify-content: center;
      flex-wrap: wrap;
    }
    .ticket-btn{
      border: 1px solid #ccc;
      background: #f7f7f7;
      color: #222;
      padding: 8px 14px;
      border-radius: 8px;
      font-size: 14px;
      cursor: pointer;
    }
    .ticket-btn:hover{ background:#eee }
    .ticket-img{
      display: none;
      width: 480px;
      max-width: 95%;
      height: auto;
      margin: 6px auto 0;
    }

    /* Logo sellado en esquina */
    .logo-modelo{ position:absolute; right:22px; bottom:22px; width:110px; height:auto; opacity:.95 }

    /* Botones: extremos opuestos */
    .acciones{
      margin-top:16px; display:flex; justify-content:space-between; align-items:center;
      gap:10px; width:100%;
    }
    .acciones .btn{ border:none; color:#fff; padding:10px 18px; font-size:16px; border-radius:8px }
    .btn-li{ background:var(--linkedin) }
    .btn-li:hover{ background:#005983 }
    .btn-print{ background:#333 }

    .footer{ font-size:12px; color:#888; margin-top:14px; font-style:italic }

    /* IMPRESIÓN: solo el certificado; sin botones ni pie; colores fieles */
    @media print{
      *{ -webkit-print-color-adjust: exact; print-color-adjust: exact; }
      body{ background:#fff; color:#000; }
      body *{ visibility:hidden !important; }
      #certificado, #certificado *{ visibility:visible !important; }
      #certificado{ position:fixed; inset:0; margin:auto; box-shadow:none; border:8px solid var(--gold); }
      .acciones, .footer, .ticket-controls{ display:none !important; }
      .ticket-img{ display:block !important; }
    }
  </style>
</head>
<body>
  <h1>🏅 Generador de Reconocimientos</h1>
  <p>Ingresa tu nombre para generar tu reconocimiento de <b>Competencias Blandas</b></p>

  <div class="controls">
    <input type="text" id="nombre" placeholder="Tu nombre" />
    <button onclick="generar()">Generar</button>
  </div>

  <div id="certificado" aria-live="polite">
    <div class="marca">Programa interno · Competencias Blandas</div>
    <div class="titulo-doc">Reconocimiento de Logro</div>
    <div class="sub">Se otorga a</div>
    <div class="nombre" id="nombreMostrado"></div>
    <div class="fecha" id="fechaEmision"></div>
    <div class="leyenda">
      Por completar satisfactoriamente el curso de <b>Competencias Blandas</b>,
      demostrando habilidades de comunicación, colaboración y pensamiento crítico.
    </div>

    <!-- Insignias -->
    <div class="insignias">
      <div class="insignia" title="Excelencia">⭐</div>
      <div class="insignia" title="Colaboración">🤝</div>
      <div class="insignia" title="Creatividad">💡</div>
    </div>

    <!-- Selector de ticket con links de Imgur directos -->
    <div class="ticket-controls">
      <button class="ticket-btn" onclick="seleccionarTicket('https://i.imgur.com/2kVac8E.png')">
        🎟️ Ticket Modelo (azul)
      </button>
      <button class="ticket-btn" onclick="seleccionarTicket('https://i.imgur.com/3qFzjiR.png')">
        🎟️ Ticket Bono/Otros (verde)
      </button>
    </div>

    <!-- Vista del ticket elegido -->
    <img id="ticketImg" class="ticket-img" alt="Ticket seleccionado"
         onerror="this.style.display='none'; alert('No se pudo cargar el ticket. Verifica que la URL sea directa y termine en .png/.jpg');" />

    <!-- Logo en la esquina -->
    <img src="grupomodelo.png" alt="Logo Grupo Modelo" class="logo-modelo" />

    <!-- Botones a los extremos -->
    <div class="acciones">
      <button class="btn btn-li" onclick="compartir()">🔗 Compartir en LinkedIn</button>
      <button class="btn btn-print" onclick="imprimir()">🖨 Guardar como PDF (Imprimir)</button>
    </div>

    
  </div>

  <script>
    function generar(){
      const nombre = document.getElementById("nombre").value;
      if(!nombre.trim()){ alert("Por favor ingresa tu nombre"); return; }

      document.getElementById("nombreMostrado").innerText = nombre;
      const hoy = new Date();
      const opciones = { year: 'numeric', month: 'long', day: 'numeric' };
      document.getElementById("fechaEmision").innerText =
        "Emitido el: " + hoy.toLocaleDateString('es-ES', opciones);

      const cert = document.getElementById("certificado");
      cert.style.display = "block";
      cert.scrollIntoView({ behavior:"smooth" });
    }

    function compartir(){
      const nombre = document.getElementById("nombreMostrado").innerText || "";
      const urlCompartida = `https://ejemplo.com/reconocimiento?name=${encodeURIComponent(nombre)}`;
      window.open(`https://www.linkedin.com/sharing/share-offsite/?url=${encodeURIComponent(urlCompartida)}`, "_blank");
    }

    function imprimir(){
      const cert = document.getElementById("certificado");
      if (cert.style.display === "none"){ alert("Genera primero tu reconocimiento."); return; }
      window.print();
    }

    function seleccionarTicket(url){
      const img = document.getElementById('ticketImg');
      img.src = url;
      img.style.display = 'block';
    }
  </script>
</body>
</html>

