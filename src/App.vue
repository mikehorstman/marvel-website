<template>
    <div class="app">
        <div class="planets">
            <Planets :movie="currentMovie" />
        </div>
        <div class="time">
            <TimeLine @clicked="setMovie" />
        </div>
    </div>
</template>

<script>
    import Map from '@/components/Map'
    import Planets from '@/components/Planets'
    import TimeLine from '@/components/TimeLine'

    export default {
        components: {
            Planets,
            TimeLine,
            Map,
        },

        methods: {
            setMovie (id) {
                this.currentMovie = id
            },
        },

        data () {
            return {
                currentMovie: 0,
            }
        },
    }
</script>

<style lang="scss">
    @import './assets/scss/variables.scss';

    .app {
        height                : 100vh;
        overflow              : hidden;

        display               : grid;
        grid-template-columns : 100%;
    }

    body {
        background  : $background;
        margin      : 0;
        font-family : 'Comic Sans MS', 'Comic Sans', cursive;
    }

    .map {
        width  : 100%;
        height : 800px;
    }

    .time {
        position : fixed;
        bottom   : 20px;
        width    : 100%;
    }

    .planets {
        width  : 100%;
        height : 80%;
    }

</style>
