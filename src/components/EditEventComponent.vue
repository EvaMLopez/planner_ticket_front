<template>
    <div>
       <h2>Editar Evento</h2>
       <form @submit.prevent="updateEvent">
         <div>
           <label for="title">Título:</label>
           <input id="title" v-model="event.title" type="text" required />
         </div>
         <div>
           <label for="description">Descripción:</label>
           <textarea id="description" v-model="event.description" required></textarea>
         </div>
         <div>
           <label for="date">Fecha:</label>
           <input id="date" v-model="event.date" type="date" required />
         </div>
         <div>
           <label for="time">Hora:</label>
           <input id="time" v-model="event.time" type="time" required />
         </div>
         <div>
           <label for="location">Ubicación:</label>
           <input id="location" v-model="event.location" type="text" required />
         </div>
         <div>
           <label for="capacity">Aforo:</label>
           <input id="capacity" v-model="event.capacity" type="number" required />
         </div>
         <button type="submit">Guardar cambios</button>
       </form>
       <button @click="deleteEvent" v-if="isAdmin">Borrar Evento</button>
    </div>
   </template>
   
   <script setup lang="ts">
   import { ref, onMounted, computed } from 'vue';
   import { useRouter } from 'vue-router';
   import { useEventsStore } from '@/stores/eventsStore';
   import { useAuthStore } from '@/stores/authStore';
   import axios from 'axios';

   
   const router = useRouter();
   const eventsStore = useEventsStore();
   const authStore = useAuthStore();
   const isAdmin = computed(() => authStore.username.role === 'ROLE_ADMIN');
   
   const eventId = router.currentRoute.value.params.id;
   const event = ref({
    title: '',
    description: '',
    date: '',
    time: '',
    location: '',
    capacity: 0,
   });
   
   onMounted(async () => {
    const response = await axios.get(`http://localhost:8080/api/v1/events/${eventId}`);
    event.value = response.data;
   });
   
   const updateEvent = async () => {
    try {
       await axios.put(`http://localhost:8080/api/v1/events/${eventId}`, event.value);
       await eventsStore.fetchEvents();
       router.push('/events');
    } catch (error) {
       console.error('Error al actualizar el evento:', error);
    }
   };
   
   const deleteEvent = async () => {
    try {
       await axios.delete(`http://localhost:8080/api/v1/events/${eventId}`);
       await eventsStore.fetchEvents();
       router.push('/events');
    } catch (error) {
       console.error('Error al borrar el evento:', error);
    }
   };
   </script>
   
   <style scoped>
   </style>
