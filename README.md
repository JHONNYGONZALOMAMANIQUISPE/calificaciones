<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Notas de Estudiantes</title>
  <script src="https://cdn.jsdelivr.net/npm/tailwindcss-jit-cdn"></script>
  <script>
    tailwind.config = { theme: { extend: {} } }
  </script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">

  <div class="w-full max-w-lg bg-white shadow-2xl rounded-2xl p-6 space-y-4">
    <h1 class="text-2xl font-bold text-center text-indigo-600">Sistema de Calificaciones</h1>

    <!-- Login -->
    <div id="loginSection" class="space-y-3">
      <label class="block">
        <span class="text-gray-700">Ingrese su código:</span>
        <input id="codigoInput" type="text" class="mt-1 block w-full rounded-lg border-gray-300 shadow-sm p-2" placeholder="Ej: STU123 o ADMIN123">
      </label>
      <button onclick="login()" class="w-full bg-indigo-600 text-white py-2 rounded-lg shadow hover:bg-indigo-700">Ingresar</button>
      <p id="loginError" class="text-red-500 text-sm text-center hidden">Código incorrecto</p>
    </div>

    <!-- Panel estudiante -->
    <div id="studentSection" class="hidden space-y-4">
      <h2 class="text-xl font-semibold text-gray-700">Bienvenido, <span id="studentName"></span></h2>
      <table class="w-full border rounded-lg">
        <thead>
          <tr class="bg-indigo-100">
            <th class="p-2 text-left">Materia</th>
            <th class="p-2 text-left">Nota</th>
          </tr>
        </thead>
        <tbody id="gradesTable" class="divide-y"></tbody>
      </table>
      <p class="font-semibold text-gray-700">Promedio: <span id="average"></span></p>
      <button onclick="logout()" class="bg-gray-500 text-white px-4 py-2 rounded-lg">Salir</button>
    </div>

    <!-- Panel administrador -->
    <div id="adminSection" class="hidden space-y-4">
      <h2 class="text-xl font-semibold text-gray-700">Panel del Docente</h2>
      
      <!-- Formulario agregar estudiante -->
      <div class="p-4 bg-gray-50 rounded-lg shadow-inner space-y-2">
        <h3 class="font-semibold">Registrar/Actualizar Estudiante</h3>
        <input id="codigoEstudiante" type="text" placeholder="Código (ej: STU123)" class="w-full p-2 rounded border">
        <input id="nombreEstudiante" type="text" placeholder="Nombre del estudiante" class="w-full p-2 rounded border">
        <input id="materia" type="text" placeholder="Materia (ej: Matemática)" class="w-full p-2 rounded border">
        <input id="nota" type="number" placeholder="Nota (0-100)" class="w-full p-2 rounded border">
        <button onclick="guardarNota()" class="bg-green-600 text-white px-4 py-2 rounded-lg w-full">Guardar Nota</button>
      </div>

      <!-- Tabla de estudiantes -->
      <div>
        <h3 class="font-semibold">Lista de estudiantes registrados</h3>
        <ul id="studentsList" class="list-disc list-inside text-gray-700"></ul>
      </div>

      <button onclick="logout()" class="bg-gray-500 text-white px-4 py-2 rounded-lg">Salir</button>
    </div>
  </div>

  <script>
    // Cargar datos desde LocalStorage
    let estudiantes = JSON.parse(localStorage.getItem("estudiantes")) || {};

    function login() {
      const codigo = document.getElementById("codigoInput").value.trim();
      if (codigo === "ADMIN123") {
        document.getElementById("loginSection").classList.add("hidden");
        document.getElementById("adminSection").classList.remove("hidden");
        actualizarLista();
      } else if (estudiantes[codigo]) {
        mostrarNotas(codigo);
        document.getElementById("loginSection").classList.add("hidden");
        document.getElementById("studentSection").classList.remove("hidden");
      } else {
        document.getElementById("loginError").classList.remove("hidden");
      }
    }

    function logout() {
      document.getElementById("codigoInput").value = "";
      document.getElementById("loginSection").classList.remove("hidden");
      document.getElementById("studentSection").classList.add("hidden");
      document.getElementById("adminSection").classList.add("hidden");
      document.getElementById("loginError").classList.add("hidden");
    }

    function guardarNota() {
      const codigo = document.getElementById("codigoEstudiante").value.trim();
      const nombre = document.getElementById("nombreEstudiante").value.trim();
      const materia = document.getElementById("materia").value.trim();
      const nota = parseFloat(document.getElementById("nota").value);

      if (!codigo || !nombre || !materia || isNaN(nota)) {
        alert("Complete todos los campos");
        return;
      }

      if (!estudiantes[codigo]) {
        estudiantes[codigo] = {nombre: nombre, notas: {}};
      } else {
        estudiantes[codigo].nombre = nombre;
      }

      estudiantes[codigo].notas[materia] = nota;
      localStorage.setItem("estudiantes", JSON.stringify(estudiantes));
      actualizarLista();
      alert("Nota guardada correctamente");
    }

    function mostrarNotas(codigo) {
      const estudiante = estudiantes[codigo];
      document.getElementById("studentName").textContent = estudiante.nombre;
      const tabla = document.getElementById("gradesTable");
      tabla.innerHTML = "";

      let suma = 0;
      let count = 0;
      for (const [materia, nota] of Object.entries(estudiante.notas)) {
        let fila = `<tr><td class="p-2">${materia}</td><td class="p-2">${nota}</td></tr>`;
        tabla.innerHTML += fila;
        suma += nota;
        count++;
      }
      const promedio = count > 0 ? (suma / count).toFixed(2) : "Sin notas";
      document.getElementById("average").textContent = promedio;
    }

    function actualizarLista() {
      const lista = document.getElementById("studentsList");
      lista.innerHTML = "";
      for (const [codigo, data] of Object.entries(estudiantes)) {
        const li = document.createElement("li");
        li.textContent = `${codigo} - ${data.nombre}`;
        lista.appendChild(li);
      }
    }
  </script>
</body>
</html>
