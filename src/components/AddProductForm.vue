<template>
  <div class="formulario-agregar" v-show="mostrando">
    <div class="formulario-contenedor">
      <div class="formulario-header">
        <h3>{{ titulo }}</h3>
        <button @click="cerrar" class="btn-cerrar">&times;</button>
      </div>
      
      <form @submit.prevent="submitFormulario" novalidate>
        <div class="campo-formulario">
          <label for="nombre">Nombre:</label>
          <input 
            type="text" 
            id="nombre" 
            name="nombre" 
            v-model="nombre"
            required
            placeholder="Ej: Hamburguesa Especial"
            :class="{ 'campo-error': errores.nombre }"
          >
          <div v-if="errores.nombre" class="mensaje-error">
            {{ errores.nombre }}
          </div>
        </div>
        
        <div class="campo-formulario">
          <label for="precio">Precio:</label>
          <input 
            type="number" 
            id="precio" 
            name="precio" 
            v-model.number="precio"
            min="0"
            step="50"
            required
            placeholder="0.00"
            :class="{ 'campo-error': errores.precio }"
          >
          <div v-if="errores.precio" class="mensaje-error">
            {{ errores.precio }}
          </div>
        </div>
        
        <div class="campo-formulario">
          <label for="cantidad">Cantidad Disponible:</label>
          <input 
            type="number" 
            id="cantidad" 
            name="cantidad" 
            v-model.number="cantidad"
            min="0"
            required
            placeholder="10"
            :class="{ 'campo-error': errores.cantidad }"
          >
          <div v-if="errores.cantidad" class="mensaje-error">
            {{ errores.cantidad }}
          </div>
        </div>
        
        <div class="campo-formulario">
          <label for="img">URL de Imagen:</label>
          <input 
            type="url" 
            id="img" 
            name="img" 
            v-model="img"
            required
            placeholder="https://ejemplo.com/imagen.jpg"
            :class="{ 'campo-error': errores.img }"
          >
          <div v-if="errores.img" class="mensaje-error">
            {{ errores.img }}
          </div>
        </div>
        
        <div class="campo-formulario">
          <label for="tipo">Tipo:</label>
          <select 
            id="tipo" 
            name="tipo" 
            v-model="tipo"
            required
            :class="{ 'campo-error': errores.tipo }"
          >
            <option value="">Seleccione tipo...</option>
            <option value="comida">Comida</option>
            <option value="bebida">Bebida</option>
          </select>
          <div v-if="errores.tipo" class="mensaje-error">
            {{ errores.tipo }}
          </div>
        </div>

        <div class="campo-formulario" v-if="tipo">
          <label for="categoria">Categoría:</label>
          <input
            type="text"
            id="categoria"
            name="categoria"
            v-model="categoria"
            list="categorias-existentes"
            required
            :placeholder="tipo === 'comida'
              ? 'Ej: Hamburguesas, Pizzas, Tacos...'
              : 'Ej: Jugos, Malteadas, Café...'"
            :class="{ 'campo-error': errores.categoria }"
          >
          <datalist id="categorias-existentes">
            <option
              v-for="cat in categoriasDisponibles"
              :key="cat"
              :value="cat"
            />
          </datalist>
          <div v-if="errores.categoria" class="mensaje-error">
            {{ errores.categoria }}
          </div>
          <span class="campo-ayuda">Elija una categoría existente o escriba una nueva</span>
        </div>
        
        <div class="acciones-formulario">
          <button type="submit" class="btn-guardar">
            Guardar Producto
          </button>
          <button type="button" @click="cerrar" class="btn-cancelar">
            Cancelar
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import Swal from 'sweetalert2'

function isNumber(value) {
  return value !== '' && value !== null && !Number.isNaN(Number(value))
}

const props = defineProps({
  mostrando: {
    type: Boolean,
    default: false
  },
  titulo: {
    type: String,
    default: 'Agregar Nuevo Producto'
  },
  categoriasComida: {
    type: Array,
    default: () => []
  },
  categoriasBebida: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['cerrar', 'producto-agregado'])

const nombre = ref('')
const precio = ref('')
const cantidad = ref('')
const img = ref('')
const tipo = ref('')
const categoria = ref('')

const errores = ref({
  nombre: '',
  precio: '',
  cantidad: '',
  img: '',
  tipo: '',
  categoria: ''
})

const categoriasDisponibles = computed(() => {
  if (tipo.value === 'comida') return props.categoriasComida
  if (tipo.value === 'bebida') return props.categoriasBebida
  return []
})

watch(tipo, () => {
  categoria.value = ''
})

function limpiarErrores() {
  errores.value = {
    nombre: '',
    precio: '',
    cantidad: '',
    img: '',
    tipo: '',
    categoria: ''
  }
}

function validarFormulario() {
  limpiarErrores()
  let valido = true

  if (!nombre.value || !nombre.value.trim()) {
    errores.value.nombre = 'El nombre del producto es obligatorio'
    valido = false
  }
  if (!isNumber(precio.value) || Number(precio.value) < 0) {
    errores.value.precio = 'Ingrese un precio válido mayor o igual a 0'
    valido = false
  }
  if (!isNumber(cantidad.value) || Number(cantidad.value) < 0) {
    errores.value.cantidad = 'Ingrese una cantidad válida mayor o igual a 0'
    valido = false
  }
  if (!img.value || !img.value.trim()) {
    errores.value.img = 'La URL de imagen es obligatoria'
    valido = false
  }
  if (!tipo.value) {
    errores.value.tipo = 'Seleccione si es comida o bebida'
    valido = false
  }
  if (tipo.value && (!categoria.value || !categoria.value.trim())) {
    errores.value.categoria = 'La categoría es obligatoria'
    valido = false
  }

  return valido
}

function submitFormulario() {
  if (!validarFormulario()) {
    Swal.fire({
      icon: 'warning',
      title: 'Campos incompletos',
      text: 'Por favor, complete todos los campos con valores válidos',
      confirmButtonColor: '#e74c3c',
      customClass: {
        popup: 'swal2-popup-custom',
        confirmButton: 'swal2-btn-custom'
      }
    })
    return
  }

  const precioNumerico = Number(precio.value)
  const cantidadNumerica = Number(cantidad.value)

  const nuevoProducto = {
    nombre: nombre.value.trim(),
    comida: nombre.value.trim(),
    categoria: categoria.value.trim(),
    precio: precioNumerico,
    cantidad_disp: Math.trunc(cantidadNumerica),
    img: img.value.trim()
  }

  emit('producto-agregado', {
    producto: nuevoProducto,
    tipo: tipo.value
  })

  Swal.fire({
    icon: 'success',
    title: 'Producto agregado',
    text: `Producto "${nombre.value.trim()}" agregado exitosamente`,
    confirmButtonColor: '#27ae60',
    timer: 2500,
    showConfirmButton: false,
    customClass: {
      popup: 'swal2-popup-custom',
      confirmButton: 'swal2-btn-custom'
    }
  })
  
  // Reset formulario
  nombre.value = ''
  precio.value = ''
  cantidad.value = ''
  img.value = ''
  tipo.value = ''
  categoria.value = ''
  limpiarErrores()
  
  emit('cerrar')
}

function cerrar() {
  limpiarErrores()
  emit('cerrar')
}
</script>

<style scoped>
.formulario-agregar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.formulario-contenedor {
  background-color: white;
  padding: 25px;
  border-radius: 12px;
  width: 90%;
  max-width: 500px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.formulario-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.formulario-header h3 {
  margin: 0;
  color: #2c3e50;
  font-size: 1.5rem;
}

.btn-cerrar {
  background: none;
  border: none;
  font-size: 1.5rem;
  color: #95a5a6;
  cursor: pointer;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.2s ease;
}

.btn-cerrar:hover {
  background-color: #f8f9fa;
  color: #e74c3c;
  transform: rotate(90deg);
}

.campo-formulario {
  margin-bottom: 20px;
}

.campo-formulario label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #34495e;
  font-size: 0.95rem;
}

.campo-formulario input,
.campo-formulario select {
  width: 100%;
  padding: 12px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
  box-sizing: border-box;
}

.campo-formulario input:focus,
.campo-formulario select:focus {
  outline: none;
  border-color: #3498db;
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.2);
}

.campo-formulario input::placeholder,
.campo-formulario select::placeholder {
  color: #bdc3c7;
}

.campo-ayuda {
  display: block;
  margin-top: 6px;
  font-size: 0.8rem;
  color: #7f8c8d;
}

/* Estilos de validación (reemplazo de Bootstrap) */
.campo-error {
  border-color: #e74c3c !important;
  box-shadow: 0 0 0 3px rgba(231, 76, 60, 0.15) !important;
}

.mensaje-error {
  display: block;
  margin-top: 4px;
  font-size: 0.85rem;
  color: #e74c3c;
  font-weight: 500;
}

.acciones-formulario {
  display: flex;
  gap: 15px;
  justify-content: flex-end;
  margin-top: 30px;
  padding-top: 20px;
  border-top: 1px solid #eee;
}

.btn-guardar,
.btn-cancelar {
  padding: 12px 24px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.btn-guardar {
  background-color: #27ae60;
  color: white;
}

.btn-guardar:hover {
  background-color: #219a52;
  transform: translateY(-2px);
}

.btn-cancelar {
  background-color: #95a5a6;
  color: white;
}

.btn-cancelar:hover {
  background-color: #7f8c8d;
  transform: translateY(-2px);
}

/* Responsividad */
@media (max-width: 480px) {
  .formulario-contenedor {
    width: 95%;
    padding: 20px;
    margin: 10px;
  }
  
  .formulario-header h3 {
    font-size: 1.25rem;
  }
  
  .campo-formulario input,
  .campo-formulario select {
    padding: 10px;
    font-size: 0.9rem;
  }
  
  .acciones-formulario {
    flex-direction: column;
  }
  
  .btn-guardar,
  .btn-cancelar {
    width: 100%;
    padding: 14px;
    font-size: 0.9rem;
  }
}
</style>
