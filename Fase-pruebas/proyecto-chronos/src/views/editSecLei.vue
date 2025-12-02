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
                <label for="input_1" class="block mb-1 text-sm font-medium text-black">Título de la actividad</label>
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
                     <option>Actividad física</option>
                    <option>Entretenimiento</option>
                    <option>Lectura</option>
                    <option>Social</option>
                    <option>Bienestar</option>
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
                <label for="select_tipo" class="block mb-1 text-sm font-medium text-black">Tipo</label>
                <select 
                    v-model="formData.frecuency"
                    id="select_tipo"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none">
                    <option disabled value="">Selecciona una opción</option>
                    <option>Diaria</option>
                    <option>Día de por medio</option>
                    <option>Semanal</option>
                    <option>Quincenal</option>
                    <option>Mensual</option>
                </select>
            </div>
            
            <div>
                <label for="select_prioridad" class="block mb-1 text-sm font-medium text-black">Prioridad</label>
                <select 
                    v-model="formData.priority"
                    id="select_prioridad"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none">
                    <option disabled value="">Selecciona una opción</option>
                    <option>Baja</option>
                    <option>Media</option>
                    <option>Alta</option>
                </select>
            </div>
            
            <div></div> 

            <div class="col-span-2"> 
                <label for="large-input" class="block mb-1 text-sm font-medium text-black">Descripción de la actividad</label>
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
    frecuency: '',
    priority: '',
    description: '',
    notifications: true, 
});

const leisureData = [
  { id: 1, title: 'Salir a caminar', type: 'Actividad física', description: 'Caminar 30 minutos por el parque o el vecindario.', date: '2025-11-07', frecuency: 'Diaria', priority: 'Media', notifications: true },
  { id: 2, title: 'Ver película pendiente', type: 'Entretenimiento', description: 'Ver la película que quedó a medias el fin de semana pasado.', date: '2025-11-09', frecuency: 'Semanal', priority: 'Baja', notifications: false },
  { id: 3, title: 'Jugar videojuegos', type: 'Entretenimiento', description: 'Disfrutar un rato libre jugando con amigos en línea.', date: '2025-11-10', frecuency: 'Semanal', priority: 'Baja', notifications: false },
  { id: 4, title: 'Leer novela', type: 'Lectura', description: 'Avanzar un capítulo del libro antes de dormir.', date: '2025-11-08', frecuency: 'Día de por medio', priority: 'Media', notifications: true },
  { id: 5, title: 'Tomar café con amigos', type: 'Social', description: 'Compartir un rato agradable con amigos o familiares.', date: '2025-11-11', frecuency: 'Quincenal', priority: 'Alta', notifications: true },
  { id: 6, title: 'Ver serie nueva', type: 'Entretenimiento', description: 'Iniciar una serie recomendada en streaming.', date: '2025-11-13', frecuency: 'Semanal', priority: 'Media', notifications: false },
  { id: 7, title: 'Ir al gimnasio', type: 'Actividad física', description: 'Entrenamiento funcional o de fuerza durante una hora.', date: '2025-11-06', frecuency: 'Día de por medio', priority: 'Alta', notifications: true },
  { id: 8, title: 'Meditar', type: 'Bienestar', description: 'Meditar 10 minutos en la mañana para reducir el estrés.', date: '2025-11-08', frecuency: 'Diaria', priority: 'Alta', notifications: true },
  { id: 9, title: 'Escuchar pódcast', type: 'Lectura', description: 'Escuchar un episodio inspirador o educativo.', date: '2025-11-09', frecuency: 'Día de por medio', priority: 'Media', notifications: false },
  { id: 10, title: 'Día de desconexión', type: 'Bienestar', description: 'Pasar un día sin redes sociales ni trabajo pendiente.', date: '2025-11-16', frecuency: 'Mensual', priority: 'Alta', notifications: true }
];



onMounted(() => {
    const itemId = route.params.id;
    if (itemId) {
        const numericId = parseInt(itemId); 
        const itemToEdit = leisureData.find(item => item.id === numericId);
        
        if (itemToEdit) {
            formData.value = { ...itemToEdit };
        } else {
            console.error(`Registro con ID ${itemId} no encontrado.`);
            router.push('/tiempo-libre'); 
        }
    } else {

        router.push('/tiempo-libre'); 
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
    router.push('/tiempo-libre'); 
};
</script>