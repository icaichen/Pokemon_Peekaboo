<script>
  import { computed } from 'vue'

    export default {
        props: {
            matched: {
                type: Boolean,
                default: false
            },
            position:{
                type: Number,
                required: true
            },
            value: {
                type: String,
                required: true
            },
            visible: {
                type: Boolean,
                default: false
            }
        },

        setup(props, context){
            // eslint-disable-next-line vue/return-in-computed-property
            const flippedStyles = computed(()=>{
                if (props.visible) {
                    return "is-flipped"
                }
            })

            const selectCard = () => {
                context.emit('select-card', {
                    position: props.position,
                    faceValue: props.value
                })
            }
            return {
                flippedStyles,
                selectCard
            }
        }
    }
</script>

<template>
    <div class="card" :class="flippedStyles" @click="selectCard" >
        <div  class="card-face is-front">
            <img :src="`/images/${value}.png`" :alt="value" class="card-image">
            <img v-if="matched" src="/images/checkmark.png" class="icon-checkmark"/>
        </div>
        <div class="card-face is-back"></div>
    </div>
</template>

<style>
.card {
    /* border: 5px solid #ccc; */
    position: relative;
    transition: 0.5s transform ease-in;
    transform-style: preserve-3d;
    /* background-color: white; */

}

.card.is-flipped {
    transform: rotateY(180deg);
}
.card-face{
    width: 100%;
    height: 100%;
    position: absolute;
    border-radius: 50%;
    align-items: center;
    justify-content: center;
    backface-visibility: hidden;
}
.card-image {
    width: 100%;
    height: auto;
}
.card-face.is-front {
    /* background-image: url('/images/pikapika.jpeg'); */
    /* background-image: url('/images/card-bg-empty.png'); */
    background-size: 100%;
    color: white;
    transform: rotateY(180deg);

}
.card-face.is-back  {
    background-image: url('/images/pokemonball.png');
    background-size: 105%;
    /* background-color: white; */

}
.icon-checkmark {
    position: absolute;
    right: 10px;
    bottom: 10px;
}
</style>