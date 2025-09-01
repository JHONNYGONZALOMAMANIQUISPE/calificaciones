<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Gestión de Calificaciones</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">
  <div class="bg-white shadow-xl rounded-xl p-6 w-full max-w-6xl">
    <h1 class="text-2xl font-bold mb-4 text-center">Gestión de Calificaciones</h1>

    <!-- Login -->
    <div id="loginDiv" class="text-center">
      <p class="mb-2">Ingrese su código:</p>
      <input type="text" id="codigoInput" class="border px-2 py-1 rounded" placeholder="Ej: STU123 o ADMIN">
      <button onclick="login()" class="bg-blue-500 text-white px-4 py-1 rounded ml-2">Ingresar</button>
    </div>

    <!-- Panel estudiante -->
    <div id="estudianteDiv" class="hidden">
      <h2 class="text-xl font-bold mb-4 text-center">Notas del Estudiante</h2>
      <div id="infoEstudiante" class="mb-4 font-semibold"></div>
      <table class="w-full border text-center" id="tablaEstudiante">
        <thead>
          <tr class="bg-gray-200">
            <th class="border px-2 py-1">Sección</th>
            <th class="border px-2 py-1">Notas</th>
            <th class="border px-2 py-1">Promedio</th>
          </tr>
        </thead>
        <tbody id="tablaNotasEstudiante"></tbody>
      </table>
      <p class="mt-4 font-bold text-lg">Promedio General: <span id="promGeneralEst">-</span></p>
    </div>

    <!-- Panel administrador -->
    <div id="adminDiv" class="hidden">
      <h2 class="text-xl font-bold mb-4 text-center">Panel del Administrador</h2>
      
      <!-- Selección estudiante -->
      <div class="mb-4">
        <label for="selEstudiante" class="mr-2 font-semibold">Seleccionar Estudiante:</label>
        <select id="selEstudiante" class="border px-2 py-1 rounded"></select>
      </div>

      <!-- Agregar nota -->
      <div class="mb-4">
        <label for="selSeccion" class="mr-2 font-semibold">Sección:</label>
        <select id="selSeccion" class="border px-2 py-1 rounded">
          <option value="SER">SER</option>
          <option value="SABER">SABER</option>
          <option value="HACER">HACER</option>
          <option value="DECIDIR">DECIDIR</option>
        </select>
        <input type="number" id="notaInput" class="border px-2 py-1 rounded ml-2 w-20" placeholder="Nota">
        <button onclick="agregarNota()" class="bg-green-500 text-white px-4 py-1 rounded ml-2">Agregar Nota</button>
      </div>

      <!-- Tabla administrador -->
      <table class="w-full border text-center" id="tablaAdmin">
        <thead>
          <tr class="bg-gray-200">
            <th class="border px-2 py-1">Código</th>
            <th class="border px-2 py-1">Nombre</th>
            <th class="border px-2 py-1">SER</th>
            <th class="border px-2 py-1">SABER</th>
            <th class="border px-2 py-1">HACER</th>
            <th class="border px-2 py-1">DECIDIR</th>
            <th class="border px-2 py-1">Promedio General</th>
          </tr>
        </thead>
        <tbody id="tablaNotasAdmin"></tbody>
      </table>
    </div>
  </div>

<script>
const adminCode = "ADMIN";
let usuarioActual = null;

// Base de datos simulada
const estudiantes = {
  "STU123": {nombre: "Ana Pérez", notas: {SER: [80], SABER: [90], HACER: [70], DECIDIR: [85]}},
  "STU456": {nombre: "Carlos Gómez", notas: {SER: [60, 75], SABER: [70], HACER: [80], DECIDIR: [65]}},
  "STU789": {nombre: "María López", notas: {SER: [95], SABER: [88], HACER: [92], DECIDIR: [90]}},
};

// Función para promediar
function promedio(arr) {
  if (!arr || arr.length === 0) return 0;
  return arr.reduce((a, b) => a + b, 0) / arr.length;
}

// Login
function login() {
  const codigo = document.getElementById("codigoInput").value.trim();
  if (codigo === adminCode) {
    usuarioActual = "ADMIN";
    document.getElementById("loginDiv").classList.add("hidden");
    document.getElementById("adminDiv").classList.remove("hidden");
    cargarSelectEstudiantes();
    renderTablaAdmin();
  } else if (estudiantes[codigo]) {
    usuarioActual = codigo;
    document.getElementById("loginDiv").classList.add("hidden");
    document.getElementById("estudianteDiv").classList.remove("hidden");
    renderTablaEstudiante();
  } else {
    alert("Código no válido.");
  }
}

// Render tabla estudiante
function renderTablaEstudiante() {
  const est = estudiantes[usuarioActual];
  document.getElementById("infoEstudiante").innerText = `${usuarioActual} - ${est.nombre}`;
  const tbody = document.getElementById("tablaNotasEstudiante");
  tbody.innerHTML = "";
  let promGeneral = 0, count = 0;
  for (let sec of ["SER", "SABER", "HACER", "DECIDIR"]) {
    let prom = promedio(est.notas[sec]);
    promGeneral += prom;
    count++;
    tbody.innerHTML += `
      <tr>
        <td class="border px-2 py-1 font-semibold">${sec}</td>
        <td class="border px-2 py-1">${est.notas[sec].join(", ")}</td>
        <td class="border px-2 py-1">${prom.toFixed(2)}</td>
      </tr>
    `;
  }
  document.getElementById("promGeneralEst").innerText = (promGeneral / count).toFixed(2);
}

// Render tabla administrador
function renderTablaAdmin() {
  const tbody = document.getElementById("tablaNotasAdmin");
  tbody.innerHTML = "";
  for (let codigo in estudiantes) {
    let est = estudiantes[codigo];
    let promGeneral = 0, count = 0;
    let fila = `<tr>
      <td class="border px-2 py-1">${codigo}</td>
      <td class="border px-2 py-1">${est.nombre}</td>`;
    for (let sec of ["SER", "SABER", "HACER", "DECIDIR"]) {
      let prom = promedio(est.notas[sec]);
      promGeneral += prom; count++;
      fila += `<td class="border px-2 py-1">${prom.toFixed(2)}</td>`;
    }
    fila += `<td class="border px-2 py-1 font-bold">${(promGeneral / count).toFixed(2)}</td></tr>`;
    tbody.innerHTML += fila;
  }
}

// Cargar estudiantes en select
function cargarSelectEstudiantes() {
  const sel = document.getElementById("selEstudiante");
  sel.innerHTML = "";
  for (let codigo in estudiantes) {
    sel.innerHTML += `<option value="${codigo}">${codigo} - ${estudiantes[codigo].nombre}</option>`;
  }
}

// Agregar nota desde admin
function agregarNota() {
  const codigo = document.getElementById("selEstudiante").value;
  const seccion = document.getElementById("selSeccion").value;
  const nota = parseFloat(document.getElementById("notaInput").value);
  if (isNaN(nota)) { alert("Ingrese una nota válida"); return; }
  estudiantes[codigo].notas[seccion].push(nota);
  renderTablaAdmin();
  if (usuarioActual !== "ADMIN" && usuarioActual === codigo) {
    renderTablaEstudiante();
  }
  document.getElementById("notaInput").value = "";
}
</script>
</body>
</html>
