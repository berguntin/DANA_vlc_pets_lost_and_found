<template>
  <div class="card bg-base-100 shadow-xl">
    <figure class="relative">
      <swiper-container class="w-full h-80" pagination="true">
        <swiper-slide v-for="(image, index) in pet.images" :key="index">
          <img loading="lazy" :class="['w-full h-80 object-cover', !pet.alive ? 'blur-md' : '']" 
               :src="image" :alt="`${pet.breed} - Image ${index + 1}`">
        </swiper-slide>
      </swiper-container>
      <div v-if="!pet.alive" class="absolute inset-0 flex justify-center items-center flex-col text-gray-50">
        <EyeOffIcon class="w-10 h-10"/>
        <span>Contenido sensible</span>
      </div>
      <div v-if="pet.athome" 
           class="absolute top-2 left-2 badge badge-primary badge-lg gap-2 p-4 z-10">
        <HomeIcon class="w-4 h-4" />
        Reunido con su familia!!
      </div>
      <div v-if="!pet.athome" class="absolute top-2 right-2 badge badge-lg text-gray-100 z-10"
           :class="pet.status === 'lost' ? 'badge-error' : 'badge-warning text-gray-900'">
        {{ pet.status === 'lost' ? 'Me buscan' : 'Encontrado' }}
      </div>
    </figure>
    <div class="card-body p-4">
      <div class="flex justify-between text-sm">
        <div class="flex items-center">
          <MapPinIcon class="w-4 h-4 mr-1" />
          <span>{{ pet.location }}</span>
        </div>
        <div class="flex items-center">
          <CalendarIcon class="w-4 h-4 mr-1" />
          <span>{{ formatDate(pet.date) }}</span>
        </div>
      </div>
      <div class="flex items-center justify-between mt-2">
        <div class="flex items-center">
          <UserIcon class="w-4 h-4 mr-1" />
          <a v-if="isPhone(pet.contact)" :href="`tel:${pet.contact}`" class="link link-hover">{{pet.contact}}</a>
          <a v-else-if="isLink(pet.contact)" :href="pet.contact" target="_blank" class="text-nowrap">{{ pet.contact }}</a>
          <span v-else>{{ pet.contact }}</span>
        </div>
        <div v-if="!pet.alive" class="badge badge-neutral">Fallecido</div>
      </div>
      <p class="text-sm mt-2 line-clamp-2 mb-2">{{ pet.description }}</p>
      
    </div>
    <div class="card-actions p-2 flex justify-between">
      <ShareButton :pet="pet" :onlyIcon="true" className="btn-ghost"/>
      <RouterLink :to="{name: 'Pet', params: {id: pet.id}}" class="w-100 btn btn-ghost">
          Ver detalles <ArrowRight/>
      </RouterLink>
    </div>
  </div>
</template>

<script setup>
import { MapPinIcon, CalendarIcon, HomeIcon, UserIcon, ArrowRight, EyeOffIcon } from 'lucide-vue-next'
import { formatDate } from '@/helpers/dateHelper'
import { ref } from 'vue';
import { defineEmits } from 'vue';
import ShareButton from '@/components/ShareButton.vue';
// import function to register Swiper custom elements
import { register } from 'swiper/element/bundle';
// register Swiper custom elements
register();


const props = defineProps({
  pet: {
    type: Object,
    required: true,

  }
})

const emit = defineEmits(['updateList']);

const error = ref(false);

const isPhone = (string) => {

  const cleanString = string.replace

  return /^\+?[1-9]\d{1,14}$/.test(cleanString)
}
const isLink = (string) => {

  return /^(https?:\/\/)?(www\.)?([a-zA-Z0-9-]+(\.[a-zA-Z]{2,})+)(:[0-9]{1,5})?(\/[^\s]*)?$/.test(string)
}
</script>