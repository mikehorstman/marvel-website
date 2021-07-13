<template>
    <div class="grid">
        <div class="title">
            <h2 :style="{'color': currentMovie.color}">{{ currentMovie.title }}</h2>
        </div>
        <transition-group class="flex-container" name="flip-list">
            <div v-if="movie.id!==0" class="flex-item" v-for="movie in movies" v-bind:key="movie.id">
                <p class="label">{{ movie.title }}</p>
                <div @click="clicked(movie.id)" class="unit" v-bind:class="{ chronological: isChronological }" :style="[movie.id === highlighted ? {'background-color': movie.color} : {}]">
                    <img class="icon" :src="require(`@/assets/icons/${movie.icon}`)">
                </div>
            </div>
        </transition-group>
        <div class="toggle-wrapper">
            <toggle-button class="toggle" :color="{checked: '#6061A7', unchecked: '#689775'}" @change="changeOrder" />
            <p class="toggle-label">{{ isChronological ? 'Chronological order' : 'Release order' }}</p>
        </div>
    </div>
</template>

<script>
    import json from '../../json/movies.json'
    import { ToggleButton } from 'vue-js-toggle-button'

    export default {
        name: 'TimeLine',
        components: {
            ToggleButton,
        },

        data () {
            return {
                movies: json.movies,
                highlighted: null,
                isChronological: false,
                currentMovie: 0,
            }
        },

        mounted () {
            this.currentMovie = this.movies.find((m) => m.id === 0)
        },

        methods: {
            changeOrder (value) {
                this.isChronological = value.value
                if (this.isChronological) {
                    this.movies.sort((a, b) => (new Date(a.in_universe_year) > new Date(b.in_universe_year)) ? 1 : -1)
                } else {
                    this.movies.sort((a, b) => (new Date(a.release_year) > new Date(b.release_year)) ? 1 : -1)
                }
            },
            clicked (id) {
                if (this.highlighted === id) {
                    this.highlighted = 0
                    this.currentMovie = this.movies.find((m) => m.id === 0)
                    this.$emit('clicked', 0)
                } else {
                    this.highlighted = id
                    this.currentMovie = this.movies.find((m) => m.id === id)
                    this.$emit('clicked', id)
                }

            },
        },
    }
</script>

<style lang="scss" scoped>
    @import '../assets/scss/variables.scss';

    .grid {
        display             : grid;
        grid-template-rows  : 20% 50% 5%;

        grid-template-areas : 'title '
                            'flex-container'
                            'toggle-wrapper ';
    }

    .title {
        grid-area       : title;
        display         : flex;
        justify-content : center;
        align-items     : center;

        h2 {
            color      : #84B893;
            transition : all 1s ease;

            &:hover {
                transform : scale(1.2);
            }

        }

    }

    .toggle {
        display      : block;
        padding-left : 5%;
        padding-top  : 10px;
        float        : left;
        margin-right : 10px;
        margin-top   : 2px;
        grid-area    : toggle;
    }

    .toggle-label {
        color : #917164;
    }

    .flex-container {
        display        : flex;
        flex-direction : row;
        gap            : 3px;
        padding-left   : 5%;
        padding-right  : 5%;

    }

    .flex-item {
        width      : 100%;
        height     : 50px;

        transition : all 1s;
        text-align : center;

        &:hover {
            .label {
                visibility : visible;
                transform  : translateY(-10px);
            }
        }

        .label {
            color      : $primary_grey;
            visibility : hidden;
            transition : all 0.2s ease;
            margin     : 0;
            font-size  : 12px;
            height     : 50px;
        }

        .unit {
            background : $primary_green;
            width      : 100%;
            height     : 30px;
            box-shadow : 2px 2px;

            transition : all 0.5s ease;

            &:hover {
                cursor    : pointer;
                transform : scale(1.2);

                .icon {
                    transform : scale(1.2);
                }
            }

            &.chronological {
                background : $violet;
            }

            .icon {
                height  : 30px;
                width   : auto;
                display : block;
                margin  : auto;
                filter  : invert(100%) sepia(0%) saturate(7480%) hue-rotate(225deg) brightness(97%) contrast(101%);
            }
        }
    }

</style>
