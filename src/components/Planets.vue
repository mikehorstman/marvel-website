<template>
    <div class="grid">
        <div class="charList">
            <div v-for="character in characters">
                <img :class="{'absolute': isAbsolute}" :id="`character${character.number}`" class="character" :src="require(`@/assets/characters/${character.image}`)">
            </div>
        </div>

        <div id="earth" class="map">
            <Map :delta-longitude="longitude" />
        </div>
        <img id="asgard" src="../assets/asgard.png">
        <img id="planet" src="../assets/planet.png">
        <div @click="test" style="background: aqua" class="btn">test</div>
    </div>
</template>

<script>
    import Map from '@/components/Map'
    import VueSlider from 'vue-slider-component'
    import 'vue-slider-component/theme/default.css'
    import charactersJson from '../../json/characters.json'
    import locationsJson from '../../json/locations.json'
    import moviesJson from '../../json/movies.json'

    export default {
        name: 'Planets',
        components: {
            Map,
            VueSlider,
        },

        props: {
            movie: {
                type: Number,
                required: true,
            },
        },

        watch: {
            movie: async function (newVal, oldVal) { // watch it
                this.currentMovie = moviesJson.movies.find((m) => m.id === newVal)

                // this.isPlaying = false
                // console.log(this.isPlaying)

                this.newAnimationStarted = true

                console.log('Playing: '+this.isPlaying)

                if (!this.isPlaying) {
                    console.log('startingAnimation')
                    await this.goToStartPosition()
                    await this.getStartPositionOfCharacters()
                    await this.startAnimation()
                }
            },
        },

        methods: {
            test () {
                this.isPlaying = false
            },
            async goToStartPosition () {
                this.characters.forEach((character) => {
                    let char = document.getElementById(`character${character.number}`)
                    char.style.transform = 'translate(0px, 0px)'
                })
                console.log('gone to startpositons')
                console.log(this.characters[1].startPosition)
            },
            getStartPositionOfCharacters () {
                for (let i = 0; i < this.characters.length; i++) {
                    let char = document.getElementById(`character${this.characters[i].number}`)
                    this.characters[i].startPosition = char.getBoundingClientRect().x
                }
                console.log('startpositions are set')
                console.log(this.characters)
            },
            async startAnimation () {
                this.newAnimationStarted = false
                this.isPlaying = true
                // if (!this.isPlaying){
                //     console.log('waiting')
                //     setTimeout(this.startAnimation(),1000)
                //     return
                // }

                const events = this.currentMovie.events
                for (const event of events) {
                    console.log('still in the loop')
                    if (this.newAnimationStarted) {
                        console.log('BREAKING')
                        break
                    }
                    await this.playEvent(event)
                }
                console.log('finally stopped')
                this.isPlaying = false
                if (this.newAnimationStarted) {
                    console.log('STARTING NEW ANIMATION NOW')
                    await this.goToStartPosition()
                    // await this.getStartPositionOfCharacters()
                    await this.startAnimation()
                }
            },
            async playEvent (event) {
                event.locations.forEach((location) => {
                    const characters = this.getCharacters(location.characters)
                    const place = this.getLocation(location.place)

                    characters.forEach((character) => {
                        let placeElement = document.getElementById(place.element).getBoundingClientRect()
                        let char = document.getElementById(`character${character.number}`)

                        let placeX
                        let placeY

                        if (place.element === 'earth') {
                            placeX = placeElement.x + (placeElement.width / 9 * place.position.x)
                            placeY = placeElement.y + (placeElement.height / 9 * place.position.y) - char.getBoundingClientRect().height
                        } else {
                            placeX = placeElement.x
                            placeY = placeElement.y
                        }
                        char.style.transform = 'translate(' + (placeX - character.startPosition) + 'px, ' + placeY + 'px)'
                    })
                })

                await new Promise(resolve => setTimeout(resolve, 3000))
            },

            getLocation (id) {
                return locationsJson.locations.find(l => l.id === id)
            },
            getCharacters (array) {
                const characters = []
                array.forEach((id) => {
                    characters.push(charactersJson.characters.filter(c => c.number === id)[0])
                })
                return characters
            },
        },

        data () {
            return {
                isAbsolute: false,
                longitude: 0,
                characters: charactersJson.characters,
                events: [],
                percentage: 'start',
                currentMovie: null,
                countries: [],
                isPlaying: false,
                newAnimationStarted: false,
            }
        },
    }
</script>

<style lang="scss" scoped>

    .grid {
        width                 : 100%;
        height                : 100%;
        position              : relative;
        display               : grid;
        grid-template-areas   : 'char char char'
                            'planets earth asgard'
                            'slider slider slider';
        grid-template-rows    : 20% auto;
        grid-template-columns : 25% 50% 25%;
    }

    .slider {
        grid-area    : slider;
        margin-right : 100px;
        margin-left  : 100px;
    }

    .map {
        width     : 100%;
        height    : 500px;
        grid-area : earth;
        z-index   : -1;

        img {
            height  : 500px;
            display : block;
            margin  : auto;

        }
    }

    #planet {
        width     : 200px;
        height    : auto;
        grid-area : planets;
    }

    #asgard {
        grid-area : asgard;
    }

    .charList {
        display        : grid;
        float          : left;
        height         : 100px;
        width          : 100%;
        grid-area      : char;
        grid-auto-flow : column;

        .character {
            height     : auto;
            width      : 40px;
            transition : all 1s ease;
            z-index    : 3;
        }

        .absolute {
            position : absolute;
        }
    }

</style>
