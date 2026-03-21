<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Verde Vivo | Gestión de Paisajismo</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600&family=Jost:wght@200;300;400;500&display=swap" rel="stylesheet">
<style>
  :root { --v-oscuro: #1a2e1a; --v-musgo: #3d5a2e; --v-salvia: #7a9e6a; --crema: #f5f0e8; --blanco: #fdfaf5; }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { font-family: 'Jost', sans-serif; background: var(--blanco); color: var(--v-oscuro); line-height: 1.6; }
  
  /* Interfaz Web */
  nav { position: fixed; top: 0; width: 100%; z-index: 100; padding: 20px 60px; display: flex; justify-content: space-between; background: rgba(253,250,245,0.9); backdrop-filter: blur(10px); }
  .logo { font-family: 'Cormorant Garamond', serif; font-size: 1.8rem; text-decoration: none; color: var(--v-oscuro); }
  .hero { height: 60vh; background: var(--v-oscuro); display: flex; align-items: center; justify-content: center; text-align: center; color: var(--crema); }
  
  /* Formulario */
  .seccion { padding: 80px 20px; max-width: 700px; margin: 0 auto; }
  .campo { margin-bottom: 15px; }
  label { font-size: 0.7rem; text-transform: uppercase; letter-spacing: 1px; color: var(--v-musgo); }
  input, select, textarea { width: 100%; padding: 12px; border: 1px solid #ddd; margin-top: 5px; font-family: 'Jost'; }
  .btn { background: var(--v-musgo); color: white; padding: 15px; border: none; width: 100%; cursor: pointer; text-transform: uppercase; letter-spacing: 1px; }

  /* Panel de Gestión (Solo para ti) */
  #admin { background: #f0f0f0; padding: 40px 20px; border-top: 3px solid var(--v-musgo); }
  .tabla-container { overflow-x: auto; background: white; padding: 20px; border-radius: 8px; margin-top: 20px; }
  table { width: 100%; border-collapse: collapse; font-size: 0.8rem; }
  th { background: var(--v-oscuro); color: white; padding: 10px; text-align: left; }
  td { padding: 10px; border-bottom: 1px solid #eee; }
  .badge { background: #e8f5e9; color: #2e7d32; padding: 2px 8px; border-radius: 10px; font-size: 0.7rem; }
</style>
</head>
<body>

<nav><a href="#" class="logo">Verde <b>Vivo</b></a></nav>

<section class="hero">
  <div><h1>Tu jardín, nuestra <em>pasión</em></h1><p>Solicita presupuesto en 1 minuto</p></div>
</section>

<section class="seccion">
  <form id="leadForm">
    <div class="campo"><label>Nombre</label><input type="text" id="n" required></div>
    <div class="campo"><label>WhatsApp / Teléfono</label><input type="tel" id="t" required></div>
    <div class="campo"><label>Servicio</label>
      <select id="s">
        <option>Diseño de Jardín</option>
        <option>Mantenimiento</option>
        <option>Piscinas</option>
      </select>
    </div>
    <div class="campo"><label>Mensaje</label><textarea id="m"></textarea></div>
    <button type="submit" class="btn">Enviar Solicitud</button>
  </form>
</section>

<section id="admin">
  <div style="max-width: 900px; margin: 0 auto;">
    <h3>🔐 Panel de Control Verde Vivo</h3>
    <div id="login">
      <input type="password" id="pass" placeholder="Clave de acceso" style="width: 200px; margin-right: 10px;">
      <button onclick="entrar()" class="btn" style="width: auto; padding: 10px 20px;">Entrar</button>
    </div>
    <div id="dashboard" style="display:none;">
      <button onclick="descargar()" style="margin-bottom: 10px; padding: 5px 10px; cursor: pointer;">📥 Descargar Excel</button>
      <div class="tabla-container">
        <table>
          <thead><tr><th>Fecha</th><th>Cliente</th><th>Contacto</th><th>Servicio</th><th>Acción</th></tr></thead>
          <tbody id="lista"></tbody>
        </table>
      </div>
    </div>
  </div>
</section>

<script>
  // GUARDAR CLIENTE
  document.getElementById('leadForm').onsubmit = (e) => {
    e.preventDefault();
    const cliente = {
      id: Date.now(),
      f: new Date().toLocaleDateString(),
      n: document.getElementById('n').value,
      t: document.getElementById('t').value,
      s: document.getElementById('s').value,
      m: document.getElementById('m').value
    };
    const db = JSON.parse(localStorage.getItem('clientes_vv')) || [];
    db.push(cliente);
    localStorage.setItem('clientes_vv', JSON.stringify(db));
    alert("¡Recibido! Nos pondremos en contacto.");
    e.target.reset();
  };

  // MANEJAR PANEL
  function entrar() {
    if(document.getElementById('pass').value === "verde2026") {
      document.getElementById('login').style.display = 'none';
      document.getElementById('dashboard').style.display = 'block';
      verClientes();
    } else { alert("Clave incorrecta"); }
  }

  function verClientes() {
    const db = JSON.parse(localStorage.getItem('clientes_vv')) || [];
    document.getElementById('lista').innerHTML = db.map(c => `
      <tr>
        <td>${c.f}</td>
        <td><b>${c.n}</b></td>
        <td>${c.t}</td>
        <td><span class="badge">${c.s}</span></td>
        <td><button onclick="borrar(${c.id})" style="color:red; border:none; background:none; cursor:pointer;">Eliminar</button></td>
      </tr>`).reverse().join('');
  }

  function borrar(id) {
    if(confirm("¿Eliminar registro?")) {
      let db = JSON.parse(localStorage.getItem('clientes_vv'));
      db = db.filter(c => c.id !== id);
      localStorage.setItem('clientes_vv', JSON.stringify(db));
      verClientes();
    }
  }

  function descargar() {
    const db = JSON.parse(localStorage.getItem('clientes_vv')) || [];
    let csv = "Fecha;Nombre;Telefono;Servicio;Mensaje\n";
    db.forEach(c => csv += `${c.f};${c.n};${c.t};${c.s};${c.m}\n`);
    const blob = new Blob([csv], {type: 'text/csv'});
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'clientes_verdevivo.csv';
    a.click();
  }
</script>
</body>
</html>
