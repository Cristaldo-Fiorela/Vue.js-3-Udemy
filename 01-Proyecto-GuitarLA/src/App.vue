<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";

import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';

const guitarras = ref(db);
const carrito = ref([]);
const guitarraBanner = ref({});
// const state = reactive({
//     guitarras: []
// })

  watch(carrito, () => {
    guardarLocalStorage();
  }, { 
    deep: true,
  });

  onMounted(() => {
    console.log("componente listo");
    guitarras.value = db; // utilizando ref
    // state.guitarras = db; // utilizando reactive
    guitarraBanner.value = db[3];

    const carritoStorage = localStorage.getItem('carrito');
    if(carritoStorage) {
      carrito.value = JSON.parse(carritoStorage);
    }
  });

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

const agregarCarrito = (guitarra) => {
    const existeEnCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);

    if(existeEnCarrito >= 0) {
      carrito.value[existeEnCarrito].cantidad++;
    } else {
      guitarra.cantidad = 1;
      carrito.value.push(guitarra);
    }

    guardarLocalStorage();
  }

  const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id); //  retorna la posicion en el carrito de compras
    if(carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--;

    guardarLocalStorage();
  }

  const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id); //  retorna la posicion en el carrito de compras
    console.log(index);
    if(carrito.value[index].cantidad >= 5) return;
    carrito.value[index].cantidad++;

    guardarLocalStorage();
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id);

    guardarLocalStorage();
  }

  const vaciarCarrito = () => {
    carrito.value = [];
    guardarLocalStorage();
  }

</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarraBanner"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <!-- GUITARRA -->
      <Guitarra
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>
