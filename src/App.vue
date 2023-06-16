<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { uid } from "uid";
import Swal from 'sweetalert2';
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';
import Header from "./components/Header.vue";
import Formulario from "./components/Formulario.vue";
import Paciente from "./components/Paciente.vue";

const pacientes = ref([]);

const paciente = reactive({
  id: null,
  nombre: '',
  propietario: '',
  email: '',
  alta: '',
  sintomas: ''
});

const guardarLocalStorage = () => {
  localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
}

onMounted(() => {
  const pacientesStorage = JSON.parse(localStorage.getItem('pacientes'));
  if (pacientesStorage) {
    pacientes.value = pacientesStorage;
  }
});

watch(pacientes, () => {
  guardarLocalStorage();
}, {
  deep: true
});

const guardarPaciente = () => {
  if(paciente.id){
    const { id } = paciente;
    const index = pacientes.value.findIndex(paciente => paciente.id === id);
    pacientes.value[index] = { ...paciente };
  } else {    
    pacientes.value.push({ ...paciente, id: uid() });
  }
  //Object assign (reiniciar el objeto)
  Object.assign(paciente, {
    id: null,
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas: ''
  });
}

const actualizarPaciente = (id) => {
  const pacienteEditar = pacientes.value.filter(paciente => paciente.id === id)[0];
  Object.assign(paciente, pacienteEditar);
}

const eliminarPaciente = (id) => {
  Swal.fire({
    title: '¿Estás seguro?',
    text: 'Esta acción no se puede deshacer',
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#3085d6',
    cancelButtonColor: '#d33',
    confirmButtonText: 'Eliminar',
    cancelButtonText: 'Cancelar',
  }).then((result) => {
    if (result.isConfirmed) {
      pacientes.value = pacientes.value.filter(paciente => paciente.id !== id);
      Swal.fire(
        'Eliminado',
        'El paciente ha sido eliminado',
        'success'
      );
    }
  });    
}
</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />

    <div class="mt-12 md:flex">
      <Formulario 
        v-model:nombre="paciente.nombre"      v-model:propietario="paciente.propietario"
        v-model:email="paciente.email" 
        v-model:alta="paciente.alta" 
        v-model:sintomas="paciente.sintomas"
        @guardar-paciente="guardarPaciente" 
        :id="paciente.id"
        />
      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">
          Administra tus Pacientes
        </h3>

        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">Información de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <Paciente 
            v-for="paciente in pacientes" :paciente="paciente" 
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-20 text-2xl text-center">
          No hay pacientes, comienza creando uno
        </p>
      </div>
    </div>
  </div>
</template>
