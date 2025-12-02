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
                    <option>Tarea</option>
                    <option>Examen</option>
                    <option>Proyecto</option>
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
                <label for="input_materia" class="block mb-1 text-sm font-medium text-black">Materia</label>
                <input 
                    v-model="formData.subject"
                    type="text" 
                    id="input_materia" 
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full" 
                    required 
                />
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
    subject: '',
    priority: '',
    description: '',
    notifications: true, 
});

const tasksData = [
  { id: 1, title: 'Reporte Final de Cálculo', type: 'Tarea', description: 'Revisar todos los ejercicios y gráficos para el día de entrega.', date: '2025-12-15', subject: 'Matemáticas', priority: 'Alta', notifications: true },
  { id: 2, title: 'Examen parcial de IA', type: 'Examen', description: 'Repasar los temas de aprendizaje automático y redes neuronales.', date: '2025-11-28', subject: 'Tecnología', priority: 'Alta', notifications: true },
  { id: 3, title: 'Proyecto de Filosofía', type: 'Proyecto', description: 'Desarrollar una presentación sobre el pensamiento existencialista.', date: '2025-11-20', subject: 'Filosofía', priority: 'Media', notifications: true },
  { id: 4, title: 'Taller de Álgebra Lineal', type: 'Tarea', description: 'Resolver los ejercicios sobre matrices y determinantes.', date: '2025-11-12', subject: 'Matemáticas', priority: 'Media', notifications: false },
  { id: 5, title: 'Examen final de Programación', type: 'Examen', description: 'Prueba práctica sobre estructuras de datos y lógica.', date: '2025-12-08', subject: 'Programación', priority: 'Alta', notifications: true },
  { id: 6, title: 'Proyecto de Base de Datos', type: 'Proyecto', description: 'Diseñar una base de datos relacional y generar el modelo ER.', date: '2025-12-10', subject: 'Informática', priority: 'Alta', notifications: true },
  { id: 7, title: 'Tarea de Historia Moderna', type: 'Tarea', description: 'Analizar las causas y consecuencias de la Revolución Industrial.', date: '2025-11-15', subject: 'Historia', priority: 'Baja', notifications: false },
  { id: 8, title: 'Examen de Física', type: 'Examen', description: 'Estudiar dinámica, leyes de Newton y movimiento circular.', date: '2025-11-22', subject: 'Física', priority: 'Media', notifications: true },
  { id: 9, title: 'Proyecto de IA aplicada', type: 'Proyecto', description: 'Crear un modelo de clasificación con datos reales.', date: '2025-12-01', subject: 'Tecnología', priority: 'Alta', notifications: true },
  { id: 10, title: 'Tarea de Redacción Técnica', type: 'Tarea', description: 'Redactar un texto formal aplicando normas APA.', date: '2025-11-18', subject: 'Comunicación', priority: 'Media', notifications: false }
];


onMounted(() => {
    const itemId = route.params.id;
    if (itemId) {
        const numericId = parseInt(itemId); 
        const itemToEdit = tasksData.find(item => item.id === numericId);
        
        if (itemToEdit) {
            formData.value = { ...itemToEdit };
        } else {
            console.error(`Registro con ID ${itemId} no encontrado.`);
            router.push('/estudio'); 
        }
    } else {

        router.push('/estudio'); 
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
    router.push('/estudio'); 
};
</script>