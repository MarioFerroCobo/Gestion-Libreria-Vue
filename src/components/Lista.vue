<template>
  <section id="listaLibros">
    <!-- Muestra de forma reactiva el numero de elementos -->
    <h2>Lista de lectura ({{ listaLectura.length }})</h2>

    <!-- Grid para mostrar libros en la lista de lectura en 2 columnas -->
    <div class="gridLista">
      <div v-for="(libros, indice) in listaLectura" :key="indice" class="tarjetaLibro">
        <img
          :src="libros.book.cover"
          alt="libros.book.title"
          class="portada"
          @click="eliminarLibro(libros)"
        />
      </div>
    </div>
  </section>
</template>

<style scoped>
#listaLectura {
  width: 30%;
  padding: 15px;
  background-color: #222;
  color: white;
  border: none;
}

/* Hace que los libros se coloquen en 2 columnas */
.gridLista {
  display: grid;
  grid-template-columns: repeat(
    2,
    1fr
  ); 
  gap: 15px;
}

.tarjetaLibro {
  width: 150px;
  height: 230px;
  background: #1f1f1f;
  padding: 10px;
  border-radius: 5px;
  text-align: center;
  transition: transform 0.2s ease-in-out;
}

.tarjetaLibro:hover {
  transform: scale(1.05);
}

.portada {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 5px;
  cursor: pointer;
}

h2{
  color: white;
}
</style>

<script setup>
defineProps(["listaLectura", "eliminarLibro"]);
</script>
