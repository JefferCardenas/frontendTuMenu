<template>

   <userHeader @logOut="logOut" />

  <div>
    <div class="container-opciones">
      <button
        class="btn btn-success"
        type="button"
        @click="showModalAgregarC()"
      >
        <i class="fas fa-plus"></i> AGREGAR CATEGORIA
      </button>
      <button
        class="btn btn-success"
        type="button"
        @click="showModalAgregarP()"
      >
        <i class="fas fa-plus"></i> AGREGAR PRODUCTO
      </button>
      <button
        class="btn btn-success"
        type="button"
        @click="showModalAgregarListaO()"
      >
        <i class="fas fa-plus"></i> AGREGAR LISTA DE OPCIONALES
      </button>
      <button
        class="btn btn-success"
        type="button"
        @click="showModalAgregarListaA()"
      >
        <i class="fas fa-plus"></i> AGREGAR LISTA DE ADICIONALES
      </button>
    </div>

    <div class="container-titulo">
      <Navbar
        @cambioSeccion="cambioSeccion()"
        :seleccion1="seleccion1"
        :seleccion2="seleccion2"
      ></Navbar>
    </div>

    <div v-if="contenedorCategorias">
      <div class="row">
        <div class="col">
          <div class="lista-categorias">
            <div class="list-group">
              <a
                href="#"
                v-for="categoria in categorias"
                :key="categoria"
                class="list-group-item list-group-item-action"
                data-toggle="list"
                @click="(id = categoria.id), toggleActive($event)"
              >
                {{ categoria.nombre }}
              </a>
            </div>
          </div>
        </div>
        <div class="col-10 categorias">
          <div v-if="mostrarC">
            <Categoria :categoria="categoria" ref="Categoria"></Categoria>
          </div>
          <div v-else>
            <div class="alert alert-success" role="alert">
              <h4 class="alert-heading text-center">
                Elige una Categoria...!
              </h4>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="contenedorOpcionales">
      <div class="row">
        <div class="col">
          <div class="lista-categorias">
            <div class="list-group">
              <a
                href="#"
                v-for="opcion in opcionales"
                :key="opcion"
                class="list-group-item list-group-item-action"
                data-toggle="list"
                @click="(idOpcional = opcion.id), toggleActiveOpcional($event)"
              >
                {{ opcion.nombre }}
              </a>
            </div>
          </div>
        </div>
        <div class="col-10">
          <div v-if="mostrarO">
            <Opcionales :opcional="opcional" ref="Opcionales"></Opcionales>
          </div>
          <div v-else>
            <div class="alert alert-success" role="alert">
              <h4 class="alert-heading text-center">Elige una opcion...!</h4>
            </div>
          </div>
        </div>
      </div>
    </div>

    <br />
    <br />

    <div class="contenedor-url">
      <div class="url">
        <p>https://www.tumenu.com/pidea/mirestaurante/menu</p>
      </div>
    </div>
  </div>

  <MdAgregarCategoria></MdAgregarCategoria>
  <MdAgregarProducto
    :categorias="categorias"
    :adicionales="adicionales"
  ></MdAgregarProducto>
  <MdAgregarListaOpcionales></MdAgregarListaOpcionales>
  <MdAgregarListaAdicionales></MdAgregarListaAdicionales>
</template>

<script>
import axios from "axios";
import Navbar from "./Navbar.vue";
import Categoria from "./Categoria.vue";
import Opcionales from "./Opcionales.vue";
import MdAgregarCategoria from "./ModalAgregarCategoria.vue";
import MdAgregarProducto from "./ModalAgregarProducto.vue";
import MdAgregarListaAdicionales from "./ModalAgregarListaAdicionales.vue";
import MdAgregarListaOpcionales from "./ModalAgregarListaOpcionales.vue";
import userHeader from "../Restaurant/UserHeader.vue";

export default {
  name: "App",
  components: {
    Navbar,
    Categoria,
    Opcionales,
    MdAgregarCategoria,
    MdAgregarProducto,
    MdAgregarListaOpcionales,
    MdAgregarListaAdicionales,
    userHeader
  },
  data() {
    return {
      activo: true,
      id: null,
      idOpcional: null,
      categoria: [
        {
          id: null,
          nombre: "",
          descripcion: "",
          urlImg: "",
        },
      ],
      opcional: [
        {
          id: null,
          nombre: "",
          descripcion: "",
        },
      ],
      adicionales: {},
      mostrarC: false,
      mostrarO: false,
      contenedorCategorias: true,
      contenedorOpcionales: false,
      seleccion1: true,
      seleccion2: false,
      categorias: null,
      opcionales: null,
    };
  },

  mounted() {
    this.getCategorias();
    this.getOpcionales();
  },

  methods: {
    toggleActive(e) {
      //this.$router.push('/categorias/' + this.id)
      console.log(this.id);
      this.getCategoria(this.id);
      this.mostrarC = true;
      setTimeout(() => {
        this.$refs.Categoria.getProductos(this.id);
      }, 100);
    },

    toggleActiveOpcional(e) {
      this.getOpcional(this.idOpcional);
      this.mostrarO = true;
      setTimeout(() => {
        this.$refs.Opcionales.getAdicionales(this.idOpcional);
      }, 100);
    },

    cambioSeccion() {
      if (this.contenedorCategorias) {
        this.contenedorCategorias = false;
        this.mostrarC = false;
        this.contenedorOpcionales = true;
        this.seleccion1 = false;
        this.seleccion2 = true;
      } else if (
        this.contenedorCategorias == false &&
        this.contenedorOpcionales
      ) {
        this.contenedorCategorias = true;
        this.contenedorOpcionales = false;
        this.mostrarO = false;
        this.seleccion1 = true;
        this.seleccion2 = false;
      }
    },

    getCategorias() {
      axios.get("http://localhost:3000/categorias").then((data) => {
        console.log(data.data);
        this.categorias = data.data;
      });
    },

    getCategoria(id) {
      axios.get(`http://localhost:3000/categorias?id=${id}`).then((data) => {
        console.log(data.data[0]);
        this.categoria.id = data.data[0].id;
        this.categoria.nombre = data.data[0].nombre;
        this.categoria.descripcion = data.data[0].descripcion;
        this.categoria.urlImg = data.data[0].url_imagen_cat;
      });
    },

    getOpcionales() {
      axios.get("http://localhost:3000/opcionales").then((data) => {
        this.opcionales = data.data;
        console.log(this.opcionales);
      });
    },

    getOpcional(id) {
      axios.get(`http://localhost:3000/opcionales?id=${id}`).then((data) => {
        console.log(data.data[0].id);
        this.opcional.id = data.data[0].id;
        this.opcional.nombre = data.data[0].nombre;
        this.opcional.descripcion = data.data[0].descripcion;
      });
    },

    showModalAgregarC() {
      this.modal = new bootstrap.Modal(
        document.getElementById("mymodalAgregarC")
      );
      this.modal.show();
    },
    showModalAgregarP() {
      this.modal = new bootstrap.Modal(
        document.getElementById("mymodalAgregarP")
      );
      this.modal.show();

      setTimeout(() => {
        this.getCategorias();
        this.getAdicionales();
      }, 400);
    },

    showModalAgregarListaO() {
      this.modal = new bootstrap.Modal(
        document.getElementById("mymodalAgregarListaO")
      );
      this.modal.show();
    },

    showModalAgregarListaA() {
      this.modal = new bootstrap.Modal(
        document.getElementById("mymodalAgregarListaA")
      );
      this.modal.show();
    },

    getAdicionales() {
      axios.get("http://localhost:3000/adicionales").then((data) => {
        this.adicionales = data.data;
      });
    },

    logOut: function() {
      localStorage.clear();
      alert("Sesión Cerrada");
      // this.verifyAuth();
      this.$router.push({ name: "home" });
    }
  },
};
/**Jeffer Cardenas */
</script>

<style>
.container-opciones {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
}

.container-opciones button {
  margin: 15px;
}

.container-categorias {
  display: flex;
}

.categorias {
  padding: 10px;
  border-radius: 25px;
  margin-left: auto;
  margin-right: auto;
}

.contenedor-url {
  display: flex;
  justify-content: center;
  flex-flow: row wrap;
}

.url {
  border: 1px solid;
  padding: 15px;
}

.url p {
  font-weight: bold;
}
</style>
