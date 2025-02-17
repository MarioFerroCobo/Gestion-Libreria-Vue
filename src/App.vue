<template>
   <!-- Cabecera principal con titulo -->
  <header>
    <h1>Libreria Vue</h1>
    <!-- Muestra el numero de libros disponibles si hay libros filtrados usando vif -->
    <h2 v-if="filtrarLibros.length>0">Numero de libros disponibles {{ filtrarLibros.length }}</h2>
     <!-- Mensaje cuando no hay libros disponibles el else del if -->
    <h2 v-else>No hay libros disponibles</h2>
  </header>
  
    <main>
       <!-- Seccion donde se muestran los libros disponibles -->
      <section id="mostrarLibros">

        <!-- Menu de filtros -->
        <div class="menu">
          <label for="genre">Genero</label>
          <select name="genre" id="slectorGnero" v-model="generoSeleccionado">
          <option value="Fantasía">Fantasía</option>
          <option value="Ciencia ficción">Ciencia ficción</option>
          <option value="Zombies">Zombies</option>
          <option value="Terror">Terror</option>
        </select>
        <label for="paginas">Numero de Paginas</label>
        <input type="range" name="paginas" id="paginas" v-model="numPaginas" min="100" max="1500">
        <p> Menos de {{ numPaginas }} paginas</p>
        <!-- Boton para reiniciar filtros con un evento click -->
        <button @click="todos" id="mostrarTodo">Mostrar Todo</button>
        </div>

        <!-- Grid donde se muestran los libros disponibles -->
        <div class="gridLibros">
         <!--  v-for es una directiva de Vue que permite iterar sobre listas de datos y renderizar elementos en el DOM.
                elemento o libro es la variable temporal que representa cada ítem dentro de filtrarLibros o listaLectura.
                indice es el índice de cada elemento en la lista (opcional, pero util).
                :key="indice" es necesario para que Vue optimice el renderizado y diferencie cada elemento. -->
          <div v-for="(elemento, indice) in filtrarLibros" :key="indice" class="tarjetaLibro">
            <!-- (:) Indica que son elementos que van a cambiar es decir reactivos, (@) Evento en este caso de click que llama a una funcion y le pasa el elemento en el que hemos clicado -->
          <img :src="elemento.book.cover" :alt="elemento.book.title" class="portada" @click="moverALista(elemento)"/>
          </div>

        </div>
        
      </section>

      <!-- Componente Lista que recibe la lista de lectura -->
       <!-- Vue renderiza el componente Lista.vue importado, a su vez le pasamos la listaLectura y eliminarLibro como props -->
      <Lista :listaLectura="listaLectura" :eliminarLibro="eliminarLibro" />
    </main>
  
</template>

<style scoped>
/* Estilos generales */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Estilos de la cabecera */
header {
  text-align: center;
  background-color: #1f1f1f;
  padding: 15px;
  color: white;
}

/* Contenedor principal */
main {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  background-color: #3a3a3a;
  min-height: 100vh;
}

/* Estilos del menú de filtros */
.menu {
  display: flex;
  align-items: center;
  justify-content: space-around;
  background-color: #252525;
  padding: 15px;
  border-radius: 10px;
  margin-bottom: 20px;
  gap: 10px;
  color: white;
}


/* Estilos para el select e input del filtro */
.menu select,
.menu input {
  padding: 8px;
  background: #444;
  color: white;
  border: none;
  border-radius: 5px;
}

/* Estilos del boton */
.menu button {
  padding: 8px 15px;
  background: #ff9800;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

/* Cambio de color al pasar el raton sobre el boton */
.menu button:hover {
  background: #e68900;
}

/* Seccion donde se muestran los libros */
#mostrarLibros {
  width: 65%;
}

/* Grid de libros */
.gridLibros {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 20px;
  justify-content: center;
}

/* Tarjetas de los libros */
.tarjetaLibro {
  width: 150px;
  height: 230px;
  background: #1f1f1f;
  padding: 10px;
  border-radius: 5px;
  text-align: center;
  transition: transform 0.2s ease-in-out;
}

/* Efecto de escala al pasar el mouse */
.tarjetaLibro:hover {
  transform: scale(1.05);
}

/* Imagenes de los libros */
.portada {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 5px;
  cursor: pointer;
}

h2 {
  margin-bottom: 10px;
}
</style>

<script setup>
//Importa el JSON
import books from "/src/data/books.json";
//Importa el componente vue Lista
import Lista from "./components/Lista.vue";
//Importa funcionalidades reactivas de vue(ref, computed) y de localStorage(onMounted, watch)
import { ref,computed, onMounted, watch } from "vue";

/* Variables reactivas */
const library = ref(books.library);
const numPaginas = ref(2000);
const generoSeleccionado = ref("");
const listaLectura = ref([]);

//Para cargar la lista de lectura desde localStorage al iniciar la aplicaion
/* onMounted (Se ejecuta al iniciar la aplicacion)

    onMounted(() => { ... }) → Se ejecuta una sola vez cuando el componente se monta en el DOM.
    localStorage.getItem("listaLectura") → Busca en localStorage si hay una lista guardada.
    if (listaGuardada) { listaLectura.value = JSON.parse(listaGuardada); }
    → Si hay datos en localStorage, los convierte de texto JSON a array y los asigna a listaLectura. */
onMounted(() =>{
  const listaGuardada = localStorage.getItem("listaLectura");
  if (listaGuardada) {
    listaLectura.value = JSON.parse(listaGuardada);
  }
})

//Guarda la lista de lectura en localStorage cada vez que cambie
/* watch(listaLectura, (nuevaLista) => { ... }, { deep: true });
→ Observa la variable listaLectura y ejecuta código cada vez que cambie.
localStorage.setItem("listaLectura", JSON.stringify(nuevaLista));
→ Convierte listaLectura a texto JSON y lo guarda en localStorage para que no se pierda tras recargar. */
watch(listaLectura, (nuevaLista) =>{
  localStorage.setItem("listaLectura", JSON.stringify(nuevaLista));
}, {deep: true}); // deep: true asegura que Vue detecte cambios dentro del array

/* Filtra los libros segun los criterios seleccionados */
/* computed(() => { ... }) → Usamos computed para que el filtrado se recalcula automáticamente cuando cambian los valores (generoSeleccionado, numPaginas o listaLectura).
    library.value.filter((elemento) => { ... }) → Filtra los libros en library, devolviendo solo los que cumplen ciertas condiciones.
    Condiciones del filtrado:
    (generoSeleccionado.value === "" || elemento.book.genre === generoSeleccionado.value)
    → Muestra todos los géneros si no se selecciona uno, o solo los libros del género elegido.
    elemento.book.pages <= numPaginas.value
    → Filtra libros con páginas menores o iguales al número seleccionado en el filtro.
    !listaLectura.value.some(libro => libro.book.ISBN === elemento.book.ISBN)
    → Evita que los libros que ya están en la lista de lectura vuelvan a aparecer en la biblioteca. */
const filtrarLibros = computed(() => {
  return library.value.filter((elemento) => {
    return (
      (generoSeleccionado.value === "" || elemento.book.genre === generoSeleccionado.value) &&
      elemento.book.pages <= numPaginas.value &&
      !listaLectura.value.some(libro => libro.book.ISBN === elemento.book.ISBN) // Excluir libros guardados en listaLectura
    );
  });
});

/* Resetea los filtros */
const todos = ()=>{
  generoSeleccionado.value = "";
  numPaginas.value = 2000;
}

/* Agrega un libro a la lista de lectura */
const moverALista = (libro)=>{
  listaLectura.value.push(libro);
}

/* Elimina un libro de la lista de lectura */
const eliminarLibro = (libro) =>{
  listaLectura.value = listaLectura.value.filter((elemento)=> elemento !==libro);
};
</script>
