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
        <h1 class="text-center mb-4">Gestión de Usuarios</h1>
  
        <!-- Formulario de Usuario -->
        <button @click="toggleForm" class="btn btn-primary mb-4">
          {{ form.USU_DNI ? "Editar Usuario" : "Agregar Usuario" }}
        </button>
  
        <form v-if="showForm" @submit.prevent="handleSubmit" class="p-4 border rounded bg-light">
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="USU_DNI">DNI</label>
              <input type="text" id="USU_DNI" v-model="form.USU_DNI" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="USU_NOMBRE">Nombre</label>
              <input type="text" id="USU_NOMBRE" v-model="form.USU_NOMBRE" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="USU_CONTRASENA">Contraseña</label>
              <input type="password" id="USU_CONTRASENA" v-model="form.USU_CONTRASENA" class="form-control" required />
            </div>
          </div>
          <button type="submit" class="btn btn-primary">Guardar</button>
        </form>
  
        <!-- Tabla de Usuarios -->
        <h2 class="mt-4">Lista de Usuarios</h2>
        <div class="table-responsive">
          <table class="table table-striped table-bordered">
            <thead class="table-dark">
              <tr>
                <th>DNI</th>
                <th>Nombre</th>
                <th>Contraseña</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="usuario in usuarios" :key="usuario.USU_DNI">
                <td>{{ usuario.USU_DNI }}</td>
                <td>{{ usuario.USU_NOMBRE }}</td>
                <td>{{ usuario.USU_CONTRASENA }}</td>
                <td>
                  <button @click="editUsuario(usuario)" class="btn btn-sm btn-warning">
                    <i class="fas fa-edit"></i> Editar
                  </button>
                  <button @click="deleteUsuario(usuario.USU_DNI)" class="btn btn-sm btn-danger">
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
    name: "Usuarios",
    data() {
      return {
        apiUrl: "http://sexshop.runasp.net/api/Usuario", // Cambia la URL de la API según corresponda
        usuarios: [],
        showForm: false,
        form: {
          USU_DNI: "",
          USU_NOMBRE: "",
          USU_CONTRASENA: "",
        },
      };
    },
    methods: {
      loadUsuarios() {
        axios
          .get(this.apiUrl)
          .then((response) => {
            this.usuarios = response.data;
          })
          .catch((error) => {
            console.error("Error al cargar usuarios:", error);
          });
      },
      toggleForm() {
        this.resetForm();
        this.showForm = !this.showForm;
      },
      handleSubmit() {
        const existingRecord = this.usuarios.find(
          (usuario) => usuario.USU_DNI === this.form.USU_DNI
        );
  
        if (existingRecord) {
          this.updateUsuario();
        } else {
          this.createUsuario();
        }
      },
      createUsuario() {
        axios
          .post(this.apiUrl, this.form)
          .then(() => {
            alert("Usuario agregado con éxito.");
            this.resetForm();
            this.loadUsuarios();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al agregar usuario:", error);
          });
      },
      updateUsuario() {
        axios
          .put(`${this.apiUrl}/${this.form.USU_DNI}`, this.form)
          .then(() => {
            alert("Usuario actualizado con éxito.");
            this.resetForm();
            this.loadUsuarios();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al actualizar usuario:", error);
          });
      },
      editUsuario(usuario) {
        this.form = { ...usuario };
        this.showForm = true;
      },
      deleteUsuario(usuDNI) {
        if (confirm("¿Estás seguro de que deseas eliminar este usuario?")) {
          axios
            .delete(`${this.apiUrl}?DNI=${usuDNI}`)
            .then(() => {
              alert("Usuario eliminado con éxito.");
              this.loadUsuarios();
            })
            .catch((error) => {
              console.error("Error al eliminar usuario:", error);
            });
        }
      },
      resetForm() {
        this.form = {
          USU_DNI: "",
          USU_NOMBRE: "",
          USU_CONTRASENA: "",
        };
      },
    },
    mounted() {
      this.loadUsuarios();
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
  