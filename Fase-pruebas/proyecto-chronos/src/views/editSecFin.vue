<template>
    <div class="p-2 sm:p-4">

    <!--encabezado-->
        <div class="flex items-start justify-center relative">
            <button 
                @click="goBack" 
                class="absolute left-0 top-0 text-blue-600 hover:text-purple-600 transition-colors duration-300 p-2 rounded-full text-5xl z-10"
                aria-label="Volver atrás"
            >
                &#x2190;
            </button>

            <div
                class="mt-8 text-center text-6xl font-bold text-transparent bg-clip-text bg-linear-to-r from-blue-600 to-purple-600 hover:from-orange-600 hover:to-pink-400 transition-all duration-300 mx-auto pt-2 pb-2" style="line-height: 1.25;">
                {{ isEditing ? 'Editar registro' : 'Agregar registro' }}
            </div>
        </div>

    <!--formulario-->
        <form @submit.prevent="handleSubmit" class="grid grid-cols-2 gap-x-8 gap-y-4 p-4 mt-8 max-w-xl mx-auto">
            
            <div>
                <label for="input_1" class="block mb-1 text-sm font-medium text-black">Título del registro</label>
                <input 
                    v-model="formData.title"
                    type="text" 
                    id="input_1" 
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full" 
                    required 
                />
            </div>

            <div>
                <label for="select_tipo" class="block mb-1 text-sm font-medium text-black">Tipo</label>
                <select 
                    v-model="formData.type"
                    id="select_tipo"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none">
                    <option disabled value="">Selecciona una opción</option>
                    <option>Gasto</option>
                    <option>Ingreso</option>
                    <option>Deuda</option>
                    <option>Ahorro</option>
                </select>
            </div>

            <div>
                <label for="date_activity" class="block mb-1 text-sm font-medium text-black">Fecha</label>
                <input 
                    v-model="formData.date"
                    type="date" 
                    id="date_activity"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none"
                />
            </div>
            
           <div>
                <label for="input_1" class="block mb-1 text-sm font-medium text-black">Monto</label>
                <input 
                    v-model="formData.amount"
                    @input="formatCurrency" 
                    type="text" 
                    id="input_1" 
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full" 
                    required 
                />
            </div>
            
            <div></div> 

            <div class="col-span-2"> 
                <label for="large-input" class="block mb-1 text-sm font-medium text-black">Descripción del registro</label>
                <textarea
                    v-model="formData.description"
                    id="large-input"
                    class="block bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full h-32"
                    required
                ></textarea>
            </div>

            <div class="col-span-2 flex justify-center pt-4"> 
                <div class="w-80">
                    <label for="notification_toggle" class="block mb-1 text-sm font-medium text-gray-700 text-center">
                        ¿Deseas activar las notificaciones?
                    </label>
                    
                    <div class="flex items-center justify-center space-x-6 mt-2">
                        <label class="inline-flex items-center cursor-pointer">
                            <input type="checkbox" id="notification_toggle" v-model="formData.notifications" class="sr-only peer">
                            <div class="relative w-11 h-6 bg-gray-200 rounded-full peer peer-focus:ring-4 peer-focus:ring-purple-300 dark:peer-focus:ring-purple-800 dark:bg-gray-700 peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-0.5 after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all dark:border-gray-600 peer-checked:bg-purple-600"></div>
                        </label>
                        
                        <div class="w-8 h-8"> 
                            <img :src="iconoCampana" alt="Icono Campana" class="w-full h-full" />
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-span-2 flex justify-center pt-8">
                <button 
                    type="submit"
                    class="bg-gradient-to-br from-blue-600 to-purple-600 hover:from-orange-600 hover:to-pink-400 text-white font-bold py-2 w-full rounded-lg shadow-lg transition duration-300 transform "
                >
                    {{ isEditing ? 'Guardar Cambios' : 'Agregar' }}
                </button>
            </div>
        </form>

    <!--modal-->
        <ModalBase 
            v-if="showSuccessModal" 
            :title-prop="modalTitle"
            icon-type="success"
            confirm-button-text="Aceptar"
            @confirm="handleSuccessConfirm" 
            @cancel="handleSuccessConfirm"
        >
        </ModalBase>

    </div>
</template>

<script setup>
import { useRouter, useRoute } from 'vue-router';
import { ref, onMounted } from 'vue';

import ModalBase from '../components/modalBase.vue';
import iconoCampana from '../assets/IconoCampana.png';

const router = useRouter();
const route = useRoute();

const showSuccessModal = ref(false);
const modalTitle = ref('');
const isEditing = ref(true);

const formData = ref({
    id: null,
    title: '',
    type: '',
    date: '',
    amount: '',
    description: '',
    notifications: true, 
});

const financesData = [
  { id: 1, title: 'Pago de arriendo', type: 'Gasto', description: 'Pago mensual del apartamento', date: '2025-11-05', amount: 900000, notifications: true },
  { id: 2, title: 'Salario mensual', type: 'Ingreso', description: 'Pago recibido por el trabajo de noviembre', date: '2025-11-01', amount: 2500000, notifications: false },
  { id: 3, title: 'Ahorro mensual', type: 'Ahorro', description: 'Transferencia a la cuenta de ahorros', date: '2025-11-02', amount: 300000, notifications: true },
  { id: 4, title: 'Compra de mercado', type: 'Gasto', description: 'Supermercado y productos del hogar', date: '2025-11-03', amount: 280000, notifications: false },
  { id: 5, title: 'Deuda con tarjeta', type: 'Deuda', description: 'Pago pendiente por compras con tarjeta de crédito', date: '2025-11-10', amount: 450000, notifications: true },
  { id: 6, title: 'Pago servicio de internet', type: 'Gasto', description: 'Pago mensual del plan de internet', date: '2025-11-08', amount: 85000, notifications: false },
  { id: 7, title: 'Venta de laptop usada', type: 'Ingreso', description: 'Venta de computador portátil antiguo', date: '2025-11-12', amount: 1200000, notifications: true },
  { id: 8, title: 'Ahorro para vacaciones', type: 'Ahorro', description: 'Depósito programado para viaje de fin de año', date: '2025-11-15', amount: 200000, notifications: true },
  { id: 9, title: 'Préstamo a un amigo', type: 'Deuda', description: 'Dinero prestado a un amigo, pendiente de devolución', date: '2025-11-20', amount: 150000, notifications: false },
  { id: 10, title: 'Cena con amigos', type: 'Gasto', description: 'Salida a comer con amigos el fin de semana', date: '2025-11-25', amount: 95000, notifications: false }
];


onMounted(() => {
    const itemId = route.params.id;
    isEditing.value = !!itemId; 
    
    if (itemId) {
        const numericId = parseInt(itemId); 
        

        const itemToEdit = financesData.find(item => item.id === numericId);
        
        if (itemToEdit) {
         
            const amountAsString = itemToEdit.amount.toString();
            

            formData.value = { ...itemToEdit, amount: amountAsString };
            

            if (formData.value.amount) {
                formatCurrency();
            }

        } else {
            console.error(`Registro con ID ${itemId} no encontrado.`);
           
            router.push('/finanzas'); 
        }
    } else {
        router.push('/finanzas'); 
    }
});
const handleSubmit = () => {
    
    // Simular GUARDAR CAMBIOS (Aquí iría la llamada PUT/PATCH a la API)
    modalTitle.value = '¡Registro actualizado con éxito!';
    console.log('Datos a actualizar:', formData.value);
    
    showSuccessModal.value = true;
};

const goBack = () => {
    router.back();
};

const handleSuccessConfirm = () => {
    showSuccessModal.value = false;
    router.push('/finanzas'); 
};

const formatCurrency = () => {

    let value = formData.value.amount;

    value = value.toString().replace(/[$. ,]/g, ''); 
    
    if (!value) {
        formData.value.amount = '';
        return;
    }
    
    const numericValue = parseInt(value, 10);


    formData.value.amount = new Intl.NumberFormat('es-CO', { 
        style: 'currency',
        currency: 'COP', 
        minimumFractionDigits: 0, 
        maximumFractionDigits: 0, 
    }).format(numericValue);
};
</script>