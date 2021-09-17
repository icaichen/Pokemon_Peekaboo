<script>
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
            const selectCard = () => {
                context.emit('select-card', {
                    position: props.position,
                    faceValue: props.value
                })
            }
            return {
                selectCard
            }
        }
    }
</script>

<template>
    <div class="card" @click="selectCard" >
        <div v-if="visible" class="card-face is-front">
            <img :src="`/images/${value}.png`" :alt="value" class="card-image">
            <img v-if="matched" src="/images/checkmark.png" class="icon-checkmark"/>
        </div>
        <div v-else class="card-face is-back"></div>
    </div>
</template>

<style>
.card {
    /* border: 5px solid #ccc; */
    position: relative;
    /* background-color: white; */

}

.card-face{
    width: 100%;
    height: 100%;
    position: absolute;
    border-radius: 50%;
    align-items: center;
    justify-content: center;
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