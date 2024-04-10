<script setup>
import { ref, onMounted, watch } from 'vue';

import { db } from './data/guitarras'
//componentes
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue';

//states
const guitarras = ref([])
const carrito = ref([])
const guitarraVAI = ref({})

watch(carrito, () => {
    guardarLocalStorage()
}, {
    deep: true
}

)

onMounted(() => {
    guitarras.value = db
    guitarraVAI.value = db[3]

    const carritoStorage = localStorage.getItem('carrito')
    if (carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }

})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
    mostrarMensajeCarrito
    console.log(existeCarrito)
    if (existeCarrito >= 0) {
        carrito.value[existeCarrito].cantidad++

    } else {
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
    }

}

const agregado = ref(false);

const mostrarMensajeCarrito = () => {
    agregado.value = true;
    setTimeout(() => {
        agregado.value = false;
    }, 2500);
};

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad <= 1) {
        return
    }
    carrito.value[index].cantidad--

}

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    carrito.value[index].cantidad++
}

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
}

const vaciarCarrito = () => {
    carrito.value = []
}



</script>

<template>
    <Header :carrito="carrito" :guitarraVAI="guitarraVAI" @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad" @agregar-carrito="agregarCarrito" @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"> </Header>
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra v-for="guitarra in guitarras" :guitarra="guitarra" @agregar-carrito="agregarCarrito">

            </Guitarra>
        </div>
        <div v-if="agregado" class="mensaje-temporal">
                <p class="text-center m-0">Se agrego al carrito correctamente</p>
        </div>
    </main>
    <Footer> </Footer>
</template>