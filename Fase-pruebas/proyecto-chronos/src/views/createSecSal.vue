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
                    Crear registro
                </div>

        </div>

    <!--formulario-->
        <div class="grid grid-cols-2 gap-x-8 gap-y-4 p-4 mt-8 max-w-xl mx-auto">
            
            <div>
                <label for="input_1" class="block mb-1 text-sm font-medium text-black">Título de la actividad</label>
                <input 
                    type="text" 
                    id="input_1" 
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full" 
                    required 
                />
            </div>

            <div>
                <label for="select_tipo" class="block mb-1 text-sm font-medium text-black">Tipo</label>
                <select 
                    id="select_tipo"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none">
                    <option disabled selected class="text-gray-500">Selecciona una opción</option>
                    <option>Cita médica</option>
                    <option>Medicamento</option>
                    <option>Hábito saludable</option>
                    <option>Control</option>
                    <option>Tratamiento</option>
                </select>
            </div>

            <div>
                <label for="date_activity" class="block mb-1 text-sm font-medium text-black">Fecha</label>
                <input 
                    type="date" 
                    id="date_activity"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none"
                />
            </div>
            
            <div>
                <label for="select_prioridad" class="block mb-1 text-sm font-medium text-black">Prioridad</label>
                <select 
                    id="select_prioridad"
                    class="bg-gray-50 border-2 border-purple-600 text-gray-900 text-sm rounded-lg p-2.5 w-full appearance-none">
                    <option disabled selected class="text-gray-500">Selecciona una opción</option>
                    <option>Baja</option>
                    <option>Media</option>
                    <option>Alta</option>
                </select>
            </div>
            
            <div class="col-span-2"> 
                <label for="large-input" class="block mb-1 text-sm font-medium text-black">Descripción de la actividad</label>
                <textarea
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
                            <input type="checkbox" id="notification_toggle" value="" class="sr-only peer" checked>
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
                    @click="navigateToSectionHealth"
                    class="bg-gradient-to-br from-blue-600 to-purple-600 hover:from-orange-600 hover:to-pink-400 text-white font-bold py-2 w-full rounded-lg shadow-lg transition duration-300 transform "
                >
                    Agregar
                </button>
            </div>
            
        <ModalBase 
                v-if="showSuccessModal" 
                title-prop="Registro agregado con éxito"
                confirm-button-text="Aceptar"
                
                icon-type="success" @confirm="handleSuccessConfirm" 
                @cancel="handleSuccessConfirm"
            >
        </ModalBase>
            
        </div>

    </div>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { ref } from 'vue';

import iconoCampana from '../assets/IconoCampana.png';
import iconoSuccess from '../assets/Success.png';
import ModalBase from '../components/modalBase.vue';

const router = useRouter();
const showSuccessModal = ref(false);


const goBack = () => {
    router.back();
};

const navigateToSectionHealth = () => {

    showSuccessModal.value = true;
};


const handleSuccessConfirm = () => {
    showSuccessModal.value = false;
    
    router.push('/salud'); 
};
</script>