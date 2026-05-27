<template>
  <div v-show="mostrando" class="factura-modal">
    <div class="factura-contenedor">
      <div class="factura-header">
        <h2>FACTURA DE VENTA</h2>
      </div>
      
      <hr class="factura-hr">
      
      <div v-if="items.length === 0" class="factura-vacia">
        <p>No hay productos en el carrito</p>
      </div>
      
      <div v-else class="factura-items">
        <div 
          v-for="item in items" 
          :key="item.nombre" 
          class="factura-item"
        >
          <span class="factura-item-cantidad">{{ item.cantidad }}x </span>
          <span class="factura-item-nombre">
            <template v-if="item.categoria && item.categoria !== 'General'">{{ item.categoria }} · </template>{{ item.nombre }}
          </span>
          <span class="factura-item-precio">{{ formatearPesos(item.subtotal) }}</span>
          <button 
            @click="quitarItem(item.nombre)" 
            class="btn-quitar-item"
          >
            −
          </button>
        </div>
      </div>
      
      <hr class="factura-hr">
      
      <div class="factura-total">
        <strong>TOTAL A COBRAR: {{ formatearPesos(total) }}</strong>
      </div>
      
      <div class="factura-acciones">
        <button 
          @click="limpiarCarrito" 
          class="btn-limpiar-carrito"
        >
          Limpiar
        </button>
       
        <button 
          @click="generarPDF" 
          class="btn-generar-pdf"
        >
          Generar Factura
        </button>
         <button 
          @click="cerrar" 
          class="btn-cerrar-factura"
        >
          Cerrar
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  mostrando: {
    type: Boolean,
    default: false
  },
  items: {
    type: Array,
    default: () => []
  },
  total: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['cerrar', 'limpiar-carrito', 'quitar-item', 'generar-pdf'])

const formatearPesos = (valor) => {
  return new Intl.NumberFormat('es-CO', {
    style: 'currency',
    currency: 'COP',
    minimumFractionDigits: 0
  }).format(valor)
}

function cerrar() {
  emit('cerrar')
}

function limpiarCarrito() {
  emit('limpiar-carrito')
}

function quitarItem(nombre) {
  emit('quitar-item', nombre)
}

function generarPDF() {
  emit('generar-pdf')
}
</script>

<style scoped>
.factura-modal {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  background: rgba(2, 6, 23, 0.62);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;

  transition: opacity 0.25s ease, visibility 0.25s ease;

  /* si hace falta, permite scroll del overlay */
  overflow: auto;
}

.factura-contenedor {
  background: radial-gradient(140% 100% at 20% 0%, rgba(39, 174, 96, 0.12) 0%, rgba(255, 255, 255, 1) 45%, rgba(251, 251, 253, 1) 100%);
  border-radius: 16px;
  width: 92%;
  max-width: 520px;
  box-shadow:
    0 18px 45px rgba(2, 6, 23, 0.22),
    0 2px 0 rgba(39, 174, 96, 0.15) inset;
  animation: fadeInUp 0.35s ease-out;
  overflow: hidden;
  border: 1px solid rgba(15, 23, 42, 0.06);

  /* Para que el scroll funcione bien en móvil */
  max-height: calc(100vh - 40px);
  display: flex;
  flex-direction: column;
}



@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.factura-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 25px;
  border-bottom: 1px solid #eee;
  background-color: #f8f9fa;
}

.factura-header h2 {
  margin: 0;
  color: #2c3e50;
  font-size: 1.75rem;
  font-family: 'Courier New', Courier, monospace;
}

.btn-cerrar-factura {
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

.btn-cerrar-factura:hover {
  background-color: #f8f9fa;
  color: #e74c3c;
  transform: rotate(90deg);
}

.factura-hr {
  border: none;
  height: 1px;
  background-color: #ddd;
  margin: 0;
}

.factura-vacia {
  text-align: center;
  padding: 30px;
  color: #95a5a6;
  font-style: italic;
}

.factura-items {
  /* Lista scrollable */
  overflow-y: auto;
  padding: 0 25px;

  /* Ocupa el espacio restante del modal */
  flex: 1;
  min-height: 0;

  -webkit-overflow-scrolling: touch;
}

/* Contenedor interno para usar flex y controlar el scroll */
.factura-contenedor {
  display: flex;
  flex-direction: column;
}

/* Asegura que el overlay no se quede bloqueado por el scroll del fondo */
.factura-modal {
  overflow: auto;
  -webkit-overflow-scrolling: touch;
}

.factura-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid #f0f0f0;
  font-family: 'Courier New', Courier, monospace;
  font-size: 0.95rem;
}

.factura-item:last-child {
  border-bottom: none;
}

.factura-item-cantidad {
  min-width: 30px;
  text-align: center;
  color: #2c3e50;
  font-weight: bold;
}

.factura-item-nombre {
  flex-grow: 1;
  text-align: left;
  color: #34495e;
}

.factura-item-precio {
  min-width: 80px;
  text-align: right;
  color: #27ae60;
  font-weight: bold;
}

.btn-quitar-item {
  background-color: #e74c3c;
  color: white;
  border: none;
  width: 25px;
  height: 25px;
  border-radius: 50%;
  cursor: pointer;
  font-weight: bold;
  font-size: 0.9rem;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

.btn-quitar-item:hover {
  background-color: #c0392b;
  transform: scale(1.1);
}

.factura-total {
  text-align: right;
  padding: 20px 25px;
  font-size: 1.25rem;
  background-color: #f8f9fa;
}

.factura-total strong {
  color: #2c3e50;
  font-size: 1.4rem;
}

.factura-acciones {
  display: flex;
  gap: 12px;
  justify-content: center;
  padding: 20px 25px;
  background-color: #f8f9fa;
}

.btn-limpiar-carrito,
.btn-cerrar-factura,
.btn-generar-pdf {
  flex: 1;
  min-width: 80px;
  padding: 12px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.btn-limpiar-carrito {
  background-color: #95a5a6;
  color: white;
}

.btn-limpiar-carrito:hover {
  background-color: #7f8c8d;
  transform: translateY(-2px);
}

.btn-cerrar-factura {
  background-color: #e74c3c;
  color: white;
}

.btn-cerrar-factura:hover {
  background-color: #c0392b;
  transform: translateY(-2px);
}

.btn-generar-pdf {
  background-color: #27ae60;
  color: white;
}

.btn-generar-pdf:hover {
  background-color: #219a52;
  transform: translateY(-2px);
}

/* Responsividad */
@media (max-width: 480px) {
  .factura-contenedor {
    width: 95%;
    margin: 10px;
  }
  
  .factura-header {
    padding: 15px 20px;
  }
  
  .factura-header h2 {
    font-size: 1.5rem;
  }
  
  .factura-item {
    padding: 10px 0;
    font-size: 0.9rem;
  }
  
  .factura-total {
    padding: 15px 20px;
    font-size: 1.1rem;
  }
  
  .factura-total strong {
    font-size: 1.2rem;
  }
  
  .factura-acciones {
    padding: 15px 20px;
    gap: 8px;
  }
  
  .btn-limpiar-carrito,
  .btn-cerrar-factura,
  .btn-generar-pdf {
    padding: 10px;
    font-size: 0.85rem;
  }
}

</style>

