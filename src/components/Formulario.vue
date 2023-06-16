<script setup>
import { reactive, computed } from 'vue';
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';
import Alerta from './Alerta.vue';

const alerta = reactive({
    mensaje: '',
    tipo: ''
});

const emit = defineEmits(['update:nombre', 'update:propietario', 'update:email', 'update:alta', 'update:sintomas', 'guardar-paciente']);

const props = defineProps({
    id: {
        type: [String, null],
        required: true
    },
    nombre: {
        type: String,
        required: true
    },
    propietario: {
        type: String,
        required: true
    },
    email: {
        type: String,
        required: true
    },
    alta: {
        type: String,
        required: true
    },
    sintomas: {
        type: String,
        required: true
    }
});

const validar = () => {
    if (Object.values(props).includes('')) {
        alerta.tipo = 'error';
        alerta.mensaje = 'Todos los campos son obligatorios';
        toast.error(alerta.mensaje);

        setTimeout(() => {
            Object.assign(alerta, {
                mensaje: '',
                tipo: ''
            })
        }, 5000);
        return;
    }

    emit('guardar-paciente');
    alerta.tipo = 'success';
    alerta.mensaje = 'Paciente registrado correctamente';
    toast.success(alerta.mensaje);

    setTimeout(() => {
        Object.assign(alerta, {
            mensaje: '',
            tipo: ''
        })
    }, 3000);
}

const editando = computed(() => {
    return props.id;
})

</script>

<template>
    <div class="md:w-1/2">
        <h2 class="font-black text-3xl text-center">Segumiento Pacientes</h2>
        <p class="text-lg mt-5 text-center mb-10">Añade Pacientes y
            <span class="text-indigo-600 font-bold">Adminístralos</span>
        </p>
        <Alerta v-if="alerta.mensaje" :alerta="alerta" />
        <form class="bg-white rounded-lg shadow-md py-10 px-5 mb-10" @submit.prevent="validar">
            <div class="mb-5">
                <label for="mascota" class="block text-gray-700 text-sm font-bold uppercase">
                    Nombre Mascota
                </label>
                <input type="text" id="mascota" placeholder="Nombre de la mascota"
                    class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md" :value="nombre"
                    @input="$emit('update:nombre', $event.target.value)" />
            </div>
            <div class="mb-5">
                <label for="propietario" class="block text-gray-700 text-sm font-bold uppercase">
                    Nombre propietario
                </label>
                <input type="text" id="propietario" placeholder="Nombre del propietario"
                    class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md" :value="propietario"
                    @input="$emit('update:propietario', $event.target.value)" />
            </div>
            <div class="mb-5">
                <label for="email" class="block text-gray-700 text-sm font-bold uppercase">
                    Email
                </label>
                <input type="email" id="email" placeholder="Email del propietario"
                    class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md" :value="email"
                    @input="$emit('update:email', $event.target.value)" />
            </div>
            <div class="mb-5">
                <label for="alta" class="block text-gray-700 text-sm font-bold uppercase">
                    Fecha de Alta
                </label>
                <input type="date" id="alta" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md" :value="alta"
                    @input="$emit('update:alta', $event.target.value)" />
            </div>
            <div class="mb-5">
                <label for="sintomas" class="block text-gray-700 text-sm font-bold uppercase">
                    Síntomas
                </label>
                <textarea id="sintomas" class="border-2 w-full p-2 mt-2 placeholder-gray-400 rounded-md h-40"
                    :value="sintomas" @input="$emit('update:sintomas', $event.target.value)" />
            </div>

            <input type="submit"
                class="bg-indigo-600 w-full p-3 text-white uppercase font-bold hover:bg-indigo-700 rounded-md cursor-pointer transition-colors"
                :value="[editando ? 'Guardar cambios' : 'Registrar Paciente']" />
        </form>
    </div>
</template>