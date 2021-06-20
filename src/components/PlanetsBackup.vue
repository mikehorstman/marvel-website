<template>
    <div class="grid">
        <div class="planetList">
            <div v-for="planet in planets">
                <img id="planet" v-if="planet.id !== planetId" @click="selectPlanet(planet.id)" class="planet" :src="require(`@/assets/${planet.image}`)">
            </div>
        </div>
        <div id="bigPlanet" class="map">
            <Map v-if="planetId === 0" />
            <img v-else :src="require(`@/assets/${planets.find(planet => planetId === planet.id).image}`)">
        </div>
    </div>
</template>

<script>
    import Map from '@/components/Map'

    export default {
        name: 'Planets',
        components: { Map },
        props: {
            currentPlanet: {
                type: Number,
                required: true,
            },
        },
        methods: {
            selectPlanet (id) {
                this.planetId = id
                this.$emit('clicked', id)

                var bigPlanet = document.getElementById('bigPlanet');
                var planet = document.getElementById('planet');
            },
        },

        data () {
            return {
                planetId: 0,
                planets: [
                    {
                        'name': 'earth',
                        'id': 0,
                        'image': 'earth.png',
                    },
                    {
                        'name': 'asgard',
                        'id': 1,
                        'image': 'asgard.png',
                    },
                    // {
                    //     'name': 'planet',
                    //     'id': 2,
                    //     'image': 'planet.png',
                    // },
                ],
            }
        },
    }
</script>

<style lang="scss" scoped>

    .grid {
        display               : grid;
        width                 : 100%;
        grid-template-columns : 25% 50% 25%;
        position: relative;
    }

    .map {
        width  : 100%;
        height : 500px;

        img {
            height  : 500px;
            display : block;
            margin  : auto;

        }
    }

    .planetList {
        display        : flex;
        flex-direction : column;
        float          : left;
        position: relative;

        .planet {
            height     : 100px;
            width      : 100px;
            position: absolute;

            transition : all 0.5s ease;

            input[type=checkbox]:checked {
                transform : scale(10) translateX(50vw);
            }
        }
    }

</style>
