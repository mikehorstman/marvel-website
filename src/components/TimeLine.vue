<template>
    <div class="flex-container">
        <div @click="clicked(movie.id)" class="flex-item" v-for="movie in movies">
            <div v-if="movie.id === highlighted">
                <img :src="require(`@/assets/${movie.image_url}`)">
            </div>
        </div>
    </div>
</template>

<script>
    import json from '../../json/movies.json'


    export default {
        name: 'TimeLine',

        data () {
            return {
                movies: json.movies,
                highlighted: 0,
            }
        },

        methods: {
            clicked (id) {
                this.highlighted = id
                this.$emit('clicked', id)
            },
        },
    }
</script>

<style lang="scss" scoped>

    .grid {
        display            : grid;
        grid-template-rows : 100% 100%;
    }

    .flex-container {
        display        : flex;
        flex-direction : row;
        gap            : 3px;
        padding-left   : 10%;
        padding-right  : 10%;

    }

    .flex-item {
        background : rgba(150, 102, 355, 1);
        width      : 100%;
        height     : 26px;

        transition : all 0.5s ease;

        &:hover {
            cursor    : pointer;
            transform : scale(1.2);
        }

    }

</style>
