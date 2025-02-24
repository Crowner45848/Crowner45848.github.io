<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Carrito de Productos</title>
  <!-- Incluir Select2 CSS -->
  <link href="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css" rel="stylesheet" />
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
    }
    h1 {
      color: #333;
    }
    .producto {
      border: 1px solid #ccc;
      padding: 10px;
      margin-bottom: 10px;
      border-radius: 5px;
    }
    .carrito {
      margin-top: 20px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: left;
    }
    th {
      background-color: #f2f2f2;
    }
    button {
      padding: 10px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background-color: #218838;
    }
    select, input {
      padding: 5px;
      margin: 5px 0;
      width: 100%;
      box-sizing: border-box;
    }
  </style>
</head>
<body>
  <h1>Carrito de Productos</h1>

  <!-- Formulario para agregar productos -->
  <div class="producto">
    <h2>Agregar Producto</h2>
    <label for="mesa">Mesa:</label>
    <select id="mesa" required>
      <option value="Mesa 1">Mesa 1</option>
      <option value="Mesa 2">Mesa 2</option>
      <option value="Mesa 3">Mesa 3</option>
      <option value="Mesa 4">Mesa 4</option>
    </select>
    <br><br>
    <label for="producto">Producto:</label>
    <select id="producto" required>
      <option value="Vodka">Vodka</option>
      <option value="Cerveza">Cerveza</option>
      <option value="Refresco">Refresco</option>
      <option value="Agua">Agua</option>
      <option value="Vino Tinto">Vino Tinto</option>
      <option value="Vino Blanco">Vino Blanco</option>
      <option value="Whisky">Whisky</option>
      <option value="Ron">Ron</option>
    </select>
    <br><br>
    <label for="cantidad">Cantidad:</label>
    <input type="number" id="cantidad" placeholder="2" required>
    <br><br>
    <label for="estado">Estado:</label>
    <select id="estado">
      <option value="Pendiente">Pendiente</option>
      <option value="Entregado">Entregado</option>
    </select>
    <br><br>
    <button onclick="agregarProducto()">Agregar al Carrito</button>
  </div>

  <!-- Resumen del carrito -->
  <div class="carrito">
    <h2>Resumen del Carrito</h2>
    <table id="tablaCarrito">
      <thead>
        <tr>
          <th>Mesa</th>
          <th>Producto</th>
          <th>Cantidad</th>
          <th>Estado</th>
          <th>Acción</th>
        </tr>
      </thead>
      <tbody>
        <!-- Aquí se agregarán los productos dinámicamente -->
      </tbody>
    </table>
    <br>
    <button onclick="enviarPedido()">Enviar Pedido</button>
  </div>

  <!-- Resultado del envío -->
  <div id="resultado"></div>

  <!-- Incluir jQuery (requerido por Select2) -->
  <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
  <!-- Incluir Select2 JS -->
  <script src="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js"></script>
  <script>
    // Inicializar Select2 en el campo "producto"
    $(document).ready(function() {
      $('#producto').select2({
        placeholder: "Buscar producto...",
        allowClear: true
      });
    });

    let carrito = [];

    // Función para agregar un producto al carrito
    function agregarProducto() {
      const mesa = document.getElementById('mesa').value;
      const producto = document.getElementById('producto').value;
      const cantidad = parseInt(document.getElementById('cantidad').value);
      const estado = document.getElementById('estado').value;

      if (mesa && producto && cantidad && estado) {
        const nuevoProducto = { mesa, producto, cantidad, estado };
        carrito.push(nuevoProducto);
        actualizarCarrito();
      } else {
        alert("Por favor, completa todos los campos.");
      }
    }

    // Función para actualizar la tabla del carrito
    function actualizarCarrito() {
      const tbody = document.querySelector('#tablaCarrito tbody');
      tbody.innerHTML = '';

      carrito.forEach((producto, index) => {
        const row = document.createElement('tr');
        row.innerHTML = `
          <td>${producto.mesa}</td>
          <td>${producto.producto}</td>
          <td>${producto.cantidad}</td>
          <td>${producto.estado}</td>
          <td><button onclick="eliminarProducto(${index})">Eliminar</button></td>
        `;
        tbody.appendChild(row);
      });
    }

    // Función para eliminar un producto del carrito
    function eliminarProducto(index) {
      carrito.splice(index, 1);
      actualizarCarrito();
    }

    // Función para enviar el pedido al script de Google Apps Script
    function enviarPedido() {
      if (carrito.length === 0) {
        alert("El carrito está vacío.");
        return;
      }

      const url = "https://script.google.com/macros/s/AKfycbzj4LgjNBq3UOFuYZS2d9ZFoFN8HdgAq0Xy8XblKpIDbtlLq02QFthmY0_YPE-KDzY/exec?accion=anadir";
      const valores = JSON.stringify(carrito);

      fetch(`${url}&valores=${encodeURIComponent(valores)}`, {
        method: 'GET',
      })
        .then(response => {
          if (!response.ok) {
            throw new Error(`Error HTTP: ${response.status}`);
          }
          return response.json();
        })
        .then(data => {
          console.log("Respuesta del servidor:", data);
          document.getElementById('resultado').innerHTML = `
            <h2>Respuesta del Servidor</h2>
            <pre>${JSON.stringify(data, null, 2)}</pre>
          `;
          carrito = []; // Vaciar el carrito después de enviar
          actualizarCarrito();
        })
        .catch(error => {
          console.error("Error en la solicitud:", error);
          document.getElementById('resultado').innerHTML = `
            <h2>Error</h2>
            <p>${error.message}</p>
          `;
        });
    }
  </script>
</body>
</html>
