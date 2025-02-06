<template>
    <div class="d-flex">
      <!-- Sidebar -->
      <nav class="bg-dark text-white sidebar vh-100">
        <div class="sidebar-header p-4">
          <h3><i class="fas fa-cogs"></i> Gestión</h3>
        </div>
        <ul class="list-unstyled px-2">
                <li><RouterLink to="/DatosGeneralesView" class="text-white nav-link">
                        <i class="fas fa-database"></i> Datos Generales
                    </RouterLink>
                </li>
                <li>
                    <RouterLink to="/ProductosView" class="text-white nav-link">
                        <i class="fas fa-box"></i> Productos
                    </RouterLink>
                </li>
                <li>
                    <RouterLink to="/UsuariosView" class="text-white nav-link">
                        <i class="fas fa-users"></i> Usuarios
                    </RouterLink>
                </li>
                <li>
                    <RouterLink to="/FacturasView" class="text-white nav-link">
                        <i class="fas fa-file"></i> Facturas
                    </RouterLink>
                </li>
                <li>
                    <RouterLink to="/DetalleFacturaView" class="text-white nav-link">
                        <i class="fas fa-file-invoice"></i> Detalle Factura
                    </RouterLink>
                </li>
            </ul>
      </nav>
  
      <!-- Main Content -->
      <div class="main-content p-4 w-100">
        <h1 class="text-center mb-4">Gestión de Detalles de Factura</h1>
  
        <!-- Formulario de Detalle de Factura -->
        <button @click="toggleForm" class="btn btn-primary mb-4">
          {{ form.FAC_NUMERO && form.PRD_ID ? "Editar Detalle" : "Agregar Detalle" }}
        </button>
  
        <form v-if="showForm" @submit.prevent="handleSubmit" class="p-4 border rounded bg-light">
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="FAC_NUMERO">Número de Factura:</label>
              <input type="text" id="FAC_NUMERO" v-model="form.FAC_NUMERO" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_ID">ID del Producto:</label>
              <input type="text" id="PRD_ID" v-model="form.PRD_ID" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_CANTIDAD">Cantidad:</label>
              <input type="number" id="PRD_CANTIDAD" v-model="form.PRD_CANTIDAD" class="form-control" required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="PRD_SUBTOTAL">Subtotal:</label>
              <input type="number" step="0.01" id="PRD_SUBTOTAL" v-model="form.PRD_SUBTOTAL" class="form-control" required />
            </div>
          </div>
          <button type="submit" class="btn btn-primary">Guardar</button>
        </form>
  
        <!-- Tabla de Detalles de Factura -->
        <h2 class="mt-4">Lista de Detalles de Factura</h2>
        <div class="table-responsive">
          <table class="table table-striped table-bordered">
            <thead class="table-dark">
              <tr>
                <th>Factura</th>
                <th>Producto</th>
                <th>Cantidad</th>
                <th>Subtotal</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="detalle in detallesFactura" :key="detalle.FAC_NUMERO + detalle.PRD_ID">
                <td>{{ detalle.FAC_NUMERO }}</td>
                <td>{{ detalle.PRD_ID }}</td>
                <td>{{ detalle.PRD_CANTIDAD }}</td>
                <td>{{ detalle.PRD_SUBTOTAL.toFixed(2) }}</td>
                <td>
                  <button @click="editDetalleFactura(detalle)" class="btn btn-sm btn-warning">
                    <i class="fas fa-edit"></i> Editar
                  </button>
                  <button @click="deleteDetalleFactura(detalle.FAC_NUMERO, detalle.PRD_ID)" class="btn btn-sm btn-danger">
                    <i class="fas fa-trash"></i> Eliminar
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    name: "DetalleFactura",
    data() {
      return {
        apiUrl: "http://sexshop.runasp.net/api/Detalle_Factura", // Asegúrate de que esta URL sea correcta
        detallesFactura: [],
        showForm: false,
        form: {
          FAC_NUMERO: "",
          PRD_ID: "",
          PRD_CANTIDAD: 0,
          PRD_SUBTOTAL: 0,
        },
      };
    },
    methods: {
      loadDetalleFacturas() {
        axios
          .get(this.apiUrl)
          .then((response) => {
            this.detallesFactura = response.data;
          })
          .catch((error) => {
            console.error("Error al cargar detalles de factura:", error);
          });
      },
      toggleForm() {
        this.resetForm();
        this.showForm = !this.showForm;
      },
      handleSubmit() {
        const existingRecord = this.detallesFactura.find(
          (detalle) =>
            detalle.FAC_NUMERO === this.form.FAC_NUMERO && detalle.PRD_ID === this.form.PRD_ID
        );
        if (existingRecord) {
          this.updateDetalleFactura();
        } else {
          this.createDetalleFactura();
        }
      },
      createDetalleFactura() {
        axios
          .post(this.apiUrl, this.form)
          .then(() => {
            alert("Detalle de factura creado con éxito.");
            this.resetForm();
            this.loadDetalleFacturas();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al crear detalle de factura:", error);
          });
      },
      updateDetalleFactura() {
        axios
          .put(`${this.apiUrl}`, this.form)
          .then(() => {
            alert("Detalle de factura actualizado con éxito.");
            this.resetForm();
            this.loadDetalleFacturas();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al actualizar detalle de factura:", error);
          });
      },
      editDetalleFactura(detalle) {
        this.form = { ...detalle };
        this.showForm = true;
      },
      deleteDetalleFactura(facNumero, prdId) {
  if (confirm("¿Estás seguro de que deseas eliminar este detalle?")) {
    axios
      .delete(`${this.apiUrl}?numFac=${facNumero}&prdNombre=${prdId}`)
      .then(() => {
        alert("Detalle de factura eliminado con éxito.");
        this.loadDetalleFacturas(); // Recargar la tabla
      })
      .catch((error) => {
        console.error("Error al eliminar detalle de factura:", error);
      });
  }
}
,
      resetForm() {
        this.form = {
          FAC_NUMERO: "",
          PRD_ID: "",
          PRD_CANTIDAD: 0,
          PRD_SUBTOTAL: 0,
        };
      },
    },
    mounted() {
      this.loadDetalleFacturas();
    },
  };
  </script>
  
  <style scoped>
  .table-responsive {
    max-width: 800px;
    margin: auto;
  }
  
  form {
    background-color: #f8f9fa;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }
  
  .sidebar {
    width: 250px;
    position: fixed;
    height: 100%;
    overflow-y: auto;
  }
  
  .main-content {
    margin-left: 250px;
    padding: 20px;
    width: calc(100% - 250px);
  }
  
  .vh-100 {
    min-height: 100vh;
  }
  
  h2 {
    text-align: center;
  }
  </style>
  