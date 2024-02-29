<template>
  <div class="events">
     <div class="events-cards">
       <div v-for="(event, index) in paginatedEvents" :key="event.id" class="event-card">
         <img :src="imageStore.images[event.image]" :alt="event.title" />
         <div class="info-card">
           <h3>{{ event.title }}</h3>
           <h5>Descripción: {{ event.description }}</h5>
           <h5>Fecha: {{ event.date }}</h5>
           <h5>Hora: {{ event.time }}</h5>
           <h5>Aforo: {{ event.capacity }}</h5>
           <h5>Ubicación: {{ event.location }}</h5>
           <div id="buttonAdmin">             
             <button type="button" class="btn btn-secondary" @click="editEvent(event.id)">Editar</button>
             <button type="button" class="btn btn-secondary" @click="deleteEvent(event.id)">Borrar</button>
            </div>
         </div>
       </div>
       <div>
         <nav aria-label="page" class="page">
           <ul class="pagination active pagination-lg justify-content-center" aria-current="page">
             <li class="page-item" :class="{ disabled: currentPage === 1 }">
               <button class="page-link" @click="changePage(currentPage - 1)" :disabled="currentPage === 1">
                 <i class="bi bi-arrow-left"></i>
               </button>
             </li>
             <li class="page-item" v-for="page in totalPages" :key="page">
               <button class="page-link" @click="changePage(page)">
                 {{ page }}
               </button>
             </li>
             <li class="page-item" :class="{ disabled: currentPage === totalPages }">
               <button class="page-link" @click="changePage(currentPage + 1)" :disabled="currentPage === totalPages">
                 <i class="bi bi-arrow-right"></i>
               </button>
             </li>
           </ul>
         </nav>
       </div>
     </div>
  </div>
 </template>
 
 <script setup lang="ts">
 import { ref, onMounted, computed } from 'vue';
 import { useRoute, useRouter } from 'vue-router';
 import axios from 'axios';
 import { useEventsStore } from '@/stores/eventsStore'; 
 import { useImageStore } from '@/stores/imagesStore'; 
 
 const router = useRouter();
 const eventsStore = useEventsStore();
 const imageStore = useImageStore();
 const currentPage = ref(1);
 const itemsPerPage = 3;
 const allEvents = computed(() => eventsStore.allEvents);
 
 const paginatedEvents = computed(() => {
  const startIndex = (currentPage.value - 1) * itemsPerPage;
  const endIndex = currentPage.value * itemsPerPage;
  return allEvents.value.slice(startIndex, endIndex);
 });
 
 const totalPages = computed(() => Math.ceil(allEvents.value.length / itemsPerPage));
 
 const fetchEvents = async () => {
  await eventsStore.fetchEvents();
 };
 
 const editEvent = (eventId) => {
  router.push({ name: 'EditEvent', params: { id: eventId } });
 };
 
 const deleteEvent = async (eventId) => {
  try {
     await axios.delete(`http://localhost:8080/api/v1/events/${eventId}`);
     console.log('Evento borrado con éxito');
     await eventsStore.fetchEvents(); 
  } catch (error) {
     console.error('Error deleting event:', error);
  }
 };
 
 const changePage = (page: number) => {
  if (page >= 1 && page <= totalPages.value) {
     currentPage.value = page;
  }
 };
 
 onMounted(fetchEvents);
 </script>


<style scoped lang="scss">
@import '../assets/admin.scss';

#buttonAdmin {

 justify-content: space-around;
 margin: 0;
 
 font-size: 1rem;
    display: inline-block;
    position: relative;
}


</style>
