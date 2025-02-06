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
        <h1 class="text-center mb-4">Gestión de Facturas</h1>
  
        <!-- Formulario de Factura -->
        <button @click="toggleForm" class="btn btn-primary mb-4">
          {{ form.FAC_NUMERO ? "Editar Factura" : "Agregar Factura" }}
        </button>
  
        <form v-if="showForm" @submit.prevent="handleSubmit" class="p-4 border rounded bg-light">
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="FAC_NUMERO">Número de Factura:</label>
              <input type="text" id="FAC_NUMERO" v-model="form.FAC_NUMERO" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="USU_DNI">DNI del Usuario:</label>
              <input type="text" id="USU_DNI" v-model="form.USU_DNI" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="USU_NOMBRE">Nombre del Usuario:</label>
              <input type="text" id="USU_NOMBRE" v-model="form.USU_NOMBRE" class="form-control" required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="FAC_TOTAL">Total:</label>
              <input type="number" step="0.01" id="FAC_TOTAL" v-model="form.FAC_TOTAL" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="FAC_ESTADO">Estado:</label>
              <input type="text" id="FAC_ESTADO" v-model="form.FAC_ESTADO" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="FAC_DIRECCION">Dirección:</label>
              <input type="text" id="FAC_DIRECCION" v-model="form.FAC_DIRECCION" class="form-control" required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="FAC_TELEFONO">Teléfono:</label>
              <input type="text" id="FAC_TELEFONO" v-model="form.FAC_TELEFONO" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="FAC_FECHA">Fecha:</label>
              <input type="datetime-local" id="FAC_FECHA" v-model="form.FAC_FECHA" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="USU_CORREO">Correo:</label>
              <input type="email" id="USU_CORREO" v-model="form.USU_CORREO" class="form-control" required />
            </div>
          </div>
          <button type="submit" class="btn btn-primary">Guardar</button>
        </form>
  
        <!-- Tabla de Facturas -->
        <h2 class="mt-4">Lista de Facturas</h2>
        <div class="table-responsive">
          <table class="table table-striped table-bordered">
            <thead class="table-dark">
              <tr>
                <th>Número</th>
                <th>DNI Usuario</th>
                <th>Nombre Usuario</th>
                <th>Correo</th>
                <th>Dirección</th>
                <th>Teléfono</th>
                <th>Fecha</th>
                <th>Total</th>
                <th>Estado</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="factura in facturas" :key="factura.FAC_NUMERO">
                <td>{{ factura.FAC_NUMERO }}</td>
                <td>{{ factura.USU_DNI }}</td>
                <td>{{ factura.USU_NOMBRE }}</td>
                <td>{{ factura.USU_CORREO }}</td>
                <td>{{ factura.FAC_DIRECCION }}</td>
                <td>{{ factura.FAC_TELEFONO }}</td>
                <td>{{ new Date(factura.FAC_FECHA).toLocaleString() }}</td>
                <td>{{ factura.FAC_TOTAL }}</td>
                <td>{{ factura.FAC_ESTADO }}</td>
                <td>
                  <button @click="editFactura(factura)" class="btn btn-sm btn-warning">
                    <i class="fas fa-edit"></i> Editar
                  </button>
                  <button @click="deleteFactura(factura.FAC_NUMERO)" class="btn btn-sm btn-danger">
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
    name: "Facturas",
    data() {
      return {
        apiUrl: "http://sexshop.runasp.net/api/Factura", // Asegúrate de que esta URL sea correcta
        facturas: [],
        showForm: false,
        form: {
          FAC_NUMERO: "",
          USU_DNI: "",
          USU_NOMBRE: "",
          USU_CORREO: "",
          FAC_DIRECCION: "",
          FAC_TELEFONO: "",
          FAC_FECHA: "",
          FAC_TOTAL: 0,
          FAC_ESTADO: "",
        },
      };
    },
    methods: {
      loadFacturas() {
        axios
          .get(this.apiUrl)
          .then((response) => {
            this.facturas = response.data;
          })
          .catch((error) => {
            console.error("Error al cargar facturas:", error);
          });
      },
      toggleForm() {
        this.resetForm();
        this.showForm = !this.showForm;
      },
      handleSubmit() {
        const existingRecord = this.facturas.find(
          (factura) => factura.FAC_NUMERO === this.form.FAC_NUMERO
        );
        if (existingRecord) {
          this.updateFactura();
        } else {
          this.createFactura();
        }
      },
      createFactura() {
        axios
          .post(this.apiUrl, this.form)
          .then(() => {
            alert("Factura creada con éxito.");
            this.resetForm();
            this.loadFacturas();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al crear la factura:", error);
          });
      },
      updateFactura() {
        axios
          .put(`${this.apiUrl}/${this.form.FAC_NUMERO}`, this.form)
          .then(() => {
            alert("Factura actualizada con éxito.");
            this.resetForm();
            this.loadFacturas();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al actualizar la factura:", error);
          });
      },
      editFactura(factura) {
        this.form = { ...factura };
        this.showForm = true;
      },
      deleteFactura(facNumero) {
        if (confirm("¿Estás seguro de que deseas eliminar esta factura?")) {
          axios
            .delete(`${this.apiUrl}?FacNumero=${facNumero}`)
            .then(() => {
              alert("Factura eliminada con éxito.");
              this.loadFacturas();
            })
            .catch((error) => {
              console.error("Error al eliminar la factura:", error);
            });
        }
      },
      resetForm() {
        this.form = {
          FAC_NUMERO: "",
          USU_DNI: "",
          USU_NOMBRE: "",
          USU_CORREO: "",
          FAC_DIRECCION: "",
          FAC_TELEFONO: "",
          FAC_FECHA: "",
          FAC_TOTAL: 0,
          FAC_ESTADO: "",
        };
      },
    },
    mounted() {
      this.loadFacturas();
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
  