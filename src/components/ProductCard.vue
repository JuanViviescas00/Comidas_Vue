<template>
  <div class="tarjeta" :style="{ opacity: item.cantidad_disp === 0 ? 0.5 : 1 }">
    <img :src="item.img" :alt="item.nombre" class="imagen">
    <h3>{{ item.nombre }}</h3>
    <p>Precio: {{ formatearPesos(item.precio) }}</p>
    <span>Disponibles: {{ item.cantidad_disp }}</span>

    <button 
      @click="agregarAlPedido(item)" 
      :disabled="item.cantidad_disp === 0"
      class="btn-agregar"
    >
      {{ item.cantidad_disp > 0 ? 'Agregar' : 'Agotado' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  item: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['agregar-al-pedido'])

const formatearPesos = (valor) => {
  return new Intl.NumberFormat('es-CO', {
    style: 'currency',
    currency: 'COP',
    minimumFractionDigits: 0
  }).format(valor)
}

function agregarAlPedido(item) {
  emit('agregar-al-pedido', item)
}
</script>

<style scoped>
.tarjeta {
  background: white;
  border-radius: 12px;
  padding: 15px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  text-align: center;
  border: 1px solid #eee;
  transition: all 0.3s ease;
  cursor: pointer;
  height: 400px;
  display: flex;
  flex-direction: column;
}

.tarjeta:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.12);
  border-color: #3498db;
}

.imagen {
  width: 100%;
  height: 200px;
  object-fit: contain;
  border-radius: 8px;
  margin-bottom: 12px;
  flex-shrink: 0;
}

h3 {
  margin: 10px 0;
  color: #2c3e50;
  text-transform: capitalize;
  font-weight: 600;
  font-size: 1.25rem;
  flex-grow: 1;
}

p {
  font-weight: bold;
  color: #27ae60;
  margin: 5px 0;
  font-size: 1.1rem;
}

span {
  font-size: 0.85rem;
  color: #7f8c8d;
  display: block;
  margin-top: 5px;
  flex-grow: 1;
}

.btn-agregar {
  width: 60%;
  height: 30px;
  margin: 15px 10px;
  border-radius: 10px;
  background-color: #27ae60;
  color: #1a1a1a;
  border: none;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.2s ease;
  align-self: center;
}

.btn-agregar:hover {
  background-color: #219a52;
}

.btn-agregar:disabled {
  background-color: #bdc3c7;
  cursor: not-allowed;
}

/* Responsividad para tarjetas */
@media (max-width: 768px) {
  .tarjeta {
    height: auto;
  }
  
  .imagen {
    height: 180px;
  }
  
  h3 {
    font-size: 1.2rem;
  }
  
  p {
    font-size: 1rem;
  }
  
  span {
    font-size: 0.8rem;
  }
  
  .btn-agregar {
    width: 70%;
    height: 35px;
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .tarjeta {
    padding: 10px;
  }
  
  .imagen {
    height: 150px;
  }
  
  h3 {
    font-size: 1.1rem;
  }
  
  p {
    font-size: 0.9rem;
  }
  
  span {
    font-size: 0.75rem;
  }
  
  .btn-agregar {
    width: 80%;
    height: 35px;
    font-size: 0.85rem;
  }
}
</style>