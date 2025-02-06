<template>
    <div class="d-flex">
      <!-- Sidebar -->
      <nav class="bg-dark text-white sidebar vh-100">
        <div class="sidebar-header p-4">
          <h3><i class="fas fa-box"></i> Productos</h3>
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
        <h1 class="text-center mb-4">Gestión de Productos</h1>
  
        <!-- Formulario de Producto -->
        <button @click="toggleForm" class="btn btn-primary mb-4">
          {{ form.PRD_ID ? "Editar Producto" : "Agregar Producto" }}
        </button>
  
        <form v-if="showForm" @submit.prevent="handleSubmit" class="p-4 border rounded bg-light">
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="PRD_ID">ID:</label>
              <input type="text" id="PRD_ID" v-model="form.PRD_ID" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_NOMBRE">Nombre:</label>
              <input type="text" id="PRD_NOMBRE" v-model="form.PRD_NOMBRE" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_DESC">Descripción:</label>
              <input type="text" id="PRD_DESC" v-model="form.PRD_DESC" class="form-control" required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-4">
              <label for="PRD_MARCA">Marca:</label>
              <input type="text" id="PRD_MARCA" v-model="form.PRD_MARCA" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_STOCK">Stock:</label>
              <input type="number" id="PRD_STOCK" v-model="form.PRD_STOCK" class="form-control" required />
            </div>
            <div class="form-group col-md-4">
              <label for="PRD_PRECIO">Precio:</label>
              <input type="number" id="PRD_PRECIO" v-model="form.PRD_PRECIO" class="form-control" required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-12">
              <label for="PRD_IMAGEN">Imagen (URL):</label>
              <input type="text" id="PRD_IMAGEN" v-model="form.PRD_IMAGEN" class="form-control" />
            </div>
          </div>
          <button type="submit" class="btn btn-primary"><i class="fas fa-save"></i> Guardar</button>
        </form>
  
        <!-- Tabla de Productos -->
        <h2 class="mt-4">Lista de Productos</h2>
        <div class="table-responsive">
          <table class="table table-striped table-bordered">
            <thead class="table-dark">
              <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Descripción</th>
                <th>Marca</th>
                <th>Stock</th>
                <th>Precio</th>
                <th>Imagen</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in productos" :key="item.PRD_ID">
                <td>{{ item.PRD_ID }}</td>
                <td>{{ item.PRD_NOMBRE }}</td>
                <td>{{ item.PRD_DESC }}</td>
                <td>{{ item.PRD_MARCA }}</td>
                <td>{{ item.PRD_STOCK }}</td>
                <td>{{ item.PRD_PRECIO.toFixed(2) }}</td>
                <td><img :src="item.PRD_IMAGEN" alt="Imagen del Producto" width="50" /></td>
                <td>
                  <button @click="editProducto(item)" class="btn btn-sm btn-warning">
                    <i class="fas fa-edit"></i> Editar
                  </button>
                  <button @click="deleteProducto(item.PRD_ID)" class="btn btn-sm btn-danger">
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
    name: "Productos",
    data() {
      return {
        apiUrl: "http://sexshop.runasp.net/api/Producto",
        productos: [],
        showForm: false,
        form: {
          PRD_ID: null,
          PRD_NOMBRE: "",
          PRD_DESC: "",
          PRD_MARCA: "",
          PRD_STOCK: 0,
          PRD_PRECIO: 0,
          PRD_IMAGEN: "",
        },
      };
    },
    methods: {
      loadProductos() {
        axios.get(this.apiUrl)
          .then((response) => {
            this.productos = response.data;
          })
          .catch((error) => {
            console.error("Error al cargar productos:", error);
          });
      },
      toggleForm() {
        this.resetForm();
        this.showForm = !this.showForm;
      },
      handleSubmit() {
        const existingRecord = this.productos.find(item => item.PRD_ID === this.form.PRD_ID);
        if (existingRecord) {
          this.updateProducto();
        } else {
          this.createProducto();
        }
      },
      createProducto() {
        axios.post(this.apiUrl, this.form)
          .then(() => {
            alert("Producto agregado con éxito.");
            this.resetForm();
            this.loadProductos();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al agregar producto:", error);
          });
      },
      updateProducto() {
        axios.put(`${this.apiUrl}/${this.form.PRD_ID}`, this.form)
          .then(() => {
            alert("Producto actualizado con éxito.");
            this.resetForm();
            this.loadProductos();
            this.showForm = false;
          })
          .catch((error) => {
            console.error("Error al actualizar producto:", error);
          });
      },
      editProducto(item) {
        this.form = { ...item };
        this.showForm = true;
      },
      deleteProducto(prdId) {
        if (confirm("¿Estás seguro de que deseas eliminar este producto?")) {
            axios.delete(`${this.apiUrl}?PrdNombre=${prdId}`)
            .then(() => {
              alert("Producto eliminado con éxito.");
              this.loadProductos();
            })
            .catch((error) => {
              console.error("Error al eliminar producto:", error);
            });
        }
      },
      resetForm() {
        this.form = {
          PRD_ID: null,
          PRD_NOMBRE: "",
          PRD_DESC: "",
          PRD_MARCA: "",
          PRD_STOCK: 0,
          PRD_PRECIO: 0,
          PRD_IMAGEN: "",
        };
      },
    },
    mounted() {
      this.loadProductos();
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
  