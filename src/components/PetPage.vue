<template>
    <div v-if="pet" class="container mx-auto mt-5 mb-5" >
      <div class="flex flex-col md:flex-row md:space-x-6">
        <!-- Photo Slider -->
        <div class="md:w-1/2 mb-6 md:mb-0 h-full bg-transparent">
          <div class="relative rounded-lg overflow-hidden">
            <swiper-container pagination="true" scrollbar="true">
              <swiper-slide  v-for="(photo, index) in pet.images" >
                <img :src="photo" :alt="`imagen de ${pet.name || 'la mascota'}`" class="m-auto">
              </swiper-slide>
            </swiper-container>
          </div>
        </div>
        <!-- Pet Information Card -->
        <div class="md:w-1/2 h-full" >
          <div class="bg-white shadow-lg rounded-lg overflow-hidden">
            <div class="p-4">
              <div class="flex justify-between items-center my-4" v-if="pet.name">
                <h1 class="text-3xl font-bold text-gray-800">{{ pet.name }}</h1>
              </div>
              <div class="flex flex-row justify-between my-1">
                <div class="flex items-center">
                <MapPinIcon class="w-4 h-4 mr-1" />
                    <span class="text-gray-600">{{ pet.location }}</span>
                </div>
                <div class="flex items-center">
                    <CalendarIcon class="w-4 h-4 mr-1"/>
                    <span class="text-gray-600"> {{ formatDate(pet.date) }}</span>
                </div>
              
              </div>
              
              <div class="flex justify-start gap-3 align-middle mb-4" v-if="pet.status === 'found'">
                
                <span v-if="pet.athome" class="badge badge-accent badge-lg p-4">Reunido con su familia!!</span>
                <span v-else 
                  :class="[
                    'px-3 py-1 badge font-semibold',
                    pet.status === 'lost' ? 'badge-error' : 'badge-warning text-gray-900'
                  ]"
                >
                  {{ pet.status === 'lost' ? 'Me buscan' : 'Encontrado' }}
                </span>
                <span 
                  :class="[
                    'px-3 py-1 badge font-semibold mr-2',
                    pet.alive ? 'badge-success' : 'bg-red-100 text-red-800'
                  ]"
                >
                  {{ pet.alive ? 'Vivo' : 'Fallecido' }}
                </span>
              </div>
              <p class="text-gray-700 my-4">{{ pet.description }}</p>
              <div class="mb-4">
                <h2 class="text-md font-semibold text-gray-800 mb-2">Contacto:</h2>
                <p class="text-gray-600">{{ pet.contact }}</p>
              </div>
              <div class="flex justify-between align-middle mt-4 gap-1">
                <div v-if="pet.alive && !pet.athome">
                  <button :class="['btn w-100 m-auto text-gray-50', error ? 'btn-warning' :  'btn-primary']" 
                          @click="confirmSetAtHome" :disabled="isLoading">
                      {{error ? error : 'Marcar como "en casa"'}}
                  </button>
                </div>
                <ShareButton :pet="pet"/>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
<script setup>
    import { onMounted, ref } from 'vue'
    import { CalendarIcon, MapPinIcon } from 'lucide-vue-next'
    import { useRoute } from 'vue-router';
    import { formatDate } from '@/helpers/dateHelper'
    import { getPetById } from '../api';
    import { setAtHome } from '../api';
    import ShareButton from '@/components/ShareButton.vue'
    // import function to register Swiper custom elements
    import { register } from 'swiper/element/bundle';
    // register Swiper custom elements
    register();


    const petId = useRoute().params.id
    
    const error = ref(false);
    const isLoading = ref(false)
    const pet = ref(null)
    const currentPhotoIndex = ref(0)
    
    const confirmSetAtHome = () => {
        if (confirm('¿Estás seguro de que quieres marcar esta mascota como devuelta a casa?')) {
        
            pet.value.athome = true
        
            markAtHome(pet.value.id)

        }
    }

    const markAtHome = async (petId) => {
        try {
            isLoading.value = true
            const response = await setAtHome(petId);
            console.log(response)
            if (response && response.ok) { 
             pet.value.athome = true
            } else {
            error.value = 'Ha ocurrido un error';
            }
        } catch (err) {
            error.value = 'Ha ocurrido un error';
            console.error(err);
        }
        isLoading.value = false
    };

    onMounted( async () => {
        pet.value = await getPetById(petId)
        pet.value = {
            ...pet.value,
            images: JSON.parse(pet.value.images)
        }
    })
</script>
