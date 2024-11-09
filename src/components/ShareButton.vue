<template>
    <button v-if="canShare" 
            :class="['btn w-100 flex gap-1 px-2 py-1 items-center', className]"
             @click="share"
             aria-label="Compartir"
             >
        <Share2Icon/><span v-if="!onlyIcon">Compartir</span>
    </button>
</template>
<script setup>
import { Share2Icon } from 'lucide-vue-next';
import { defineProps } from 'vue';

const props = defineProps({
    pet: Object,
    onlyIcon: {
        type: Boolean,
        default: false
    },
    className: String
})

const share = () => {
   navigator.share({
    title : getTitle(),
    text: getText(),
    url: `${window.location.origin}/mascota/${props.pet.id}`
   })
}


const canShare = () => {
      return navigator.canShare()
    }

const getTitle = () => {

    if (props.pet.athome) {
        return `${props.pet.name || 'Esta mascota'} ya está con su familia!`
    }

    return props.pet.status === 'lost' ? 
                    `Ayúdame a encontrar a ${props.pet.name || 'mi mascota'}` :
                    `¿Alguien lo busca?`
}

const getText = () => {

    if (props.pet.athome) {
        return 'Una historia con final feliz'
    }

    if (!props.pet.alive) {
        return `Tristemente, ha sido encontrado fallecido`
    }

    return props.pet.description || 'Mascotas perdidas y encontradas DANA Valencia'
}
</script>