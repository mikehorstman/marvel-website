<template>
    <div class="grid">
        <div class="charList">
            <div class="char-wrapper" v-for="character in characters">
                <img :id="`character${character.number}`" class="character" :src="require(`@/assets/characters/${character.image}`)">
                <p class="label">{{ character.alias }}</p>
            </div>
        </div>
        <div class="planets-left">
            <div id="planet">
                <Info class="info" id="planet-info" :text="locationInformation.planet.text" :place="locationInformation.planet.location" />
                <img src="../assets/planet.png">
            </div>
            <div id="planet2">
                <Info class="info" id="planet2-info" :text="locationInformation.planet2.text" :place="locationInformation.planet2.location" />
                <img src="../assets/planet2.png">
            </div>
            <div id="planet3">
                <Info class="info" id="planet3-info" :text="locationInformation.planet3.text" :place="locationInformation.planet3.location" />
                <img src="../assets/planet3.png">
            </div>
            <div id="space">
                <Info class="info" id="space-info" :text="locationInformation.space.text" :place="locationInformation.space.location" />
                <div />
            </div>

        </div>

        <div id="earth" class="map">
            <div v-for="event in currentMovie.events">
                <div v-for="location in event.locations">
                    <Info :id="`earth-${event.id}-${getLocation(location.place).id}-info`" :text="location.information" :place="`${getLocation(location.place).name}`" />
                </div>
            </div>
            <Map />
        </div>

        <div class="planets-right">
            <div id="asgard">
                <Info class="info" id="asgard-info" :text="locationInformation.asgard.text" :place="locationInformation.asgard.location" />
                <img src="../assets/asgard.png">
            </div>
            <div id="planet4">
                <Info class="info" id="planet4-info" :text="locationInformation.planet4.text" :place="locationInformation.planet4.location" />
                <img src="../assets/planet4.png">
            </div>

        </div>
    </div>
</template>

<script>
    import Info from '@/components/Info'
    import Map from '@/components/Map'
    import VueSlider from 'vue-slider-component'
    import 'vue-slider-component/theme/default.css'
    import charactersJson from '../../json/characters.json'
    import locationsJson from '../../json/locations.json'
    import moviesJson from '../../json/movies.json'
    import { store, mutations } from '@/store.js'

    export default {
        name: 'Planets',
        components: {
            Info,
            Map,
            VueSlider,
        },

        props: {
            movie: {
                type: Number,
                required: false,
            },
        },

        watch: {
            movie: async function (newVal, oldVal) {
                this.currentMovie = moviesJson.movies.find((m) => m.id === newVal)
                this.newAnimationStarted = true

                if (!this.isPlaying) {
                    await this.goToStartPosition()
                    await this.startAnimation()
                }
            },
        },

        mounted () {
            this.setStartPositionOfCharacters()
        },

        methods: {
            setInformation (element, info, location) {
                switch (element) {
                    case 'planet':
                        this.locationInformation.planet.text = info
                        this.locationInformation.planet.location = location
                        break
                    case 'planet2':
                        this.locationInformation.planet2.text = info
                        this.locationInformation.planet2.location = location
                        break
                    case 'planet3':
                        this.locationInformation.planet3.text = info
                        this.locationInformation.planet3.location = location
                        break
                    case 'planet4':
                        this.locationInformation.planet4.text = info
                        this.locationInformation.planet4.location = location
                        break
                    case 'asgard':
                        this.locationInformation.asgard.text = info
                        this.locationInformation.asgard.location = location
                        break
                    default:
                        this.locationInformation.space.text = info
                        this.locationInformation.space.location = location
                }
            },
            async goToStartPosition () {
                this.characters.forEach((character) => {
                    let char = document.getElementById(`character${character.number}`)
                    char.style.transform = 'translate(0px, 0px)'
                })

            },
            async setStartPositionOfCharacters () {
                for (let i = 0; i < this.characters.length; i++) {
                    let char = document.getElementById(`character${this.characters[i].number}`)
                    this.characters[i].startPosition = char.getBoundingClientRect().x
                }
            },
            async startAnimation () {
                this.newAnimationStarted = false
                this.isPlaying = true

                const events = this.currentMovie.events
                for (const event of events) {
                    if (this.newAnimationStarted) {
                        break
                    }
                    await this.playEvent(event)
                }
                this.isPlaying = false
                if (this.newAnimationStarted) {
                    await this.goToStartPosition()
                    await this.startAnimation()
                }
            },
            async playEvent (event) {
                const refreshRate = (event.locations.length * 1500) + 1000

                event.locations.forEach((location) => {
                    const characters = this.getCharacters(location.characters)
                    const place = this.getLocation(location.place)

                    let count = 0
                    characters.forEach((character) => {
                        let placeElement = document.getElementById(place.element).getBoundingClientRect()
                        let char = document.getElementById(`character${character.number}`)

                        let placeX
                        let placeY

                        if (place.element === 'earth') {
                            placeX = placeElement.x + (placeElement.width / 9 * place.position.x) + count
                            placeY = placeElement.y + (placeElement.height / 9 * place.position.y) - char.getBoundingClientRect().height + count
                        } else {
                            placeX = placeElement.x + count + place.position.x
                            placeY = placeElement.y + count + place.position.y
                        }
                        count += 10
                        char.style.transform = 'translate(' + (placeX - character.startPosition) + 'px, ' + placeY + 'px)'

                    })

                    let placeElement = document.getElementById(place.element).getBoundingClientRect()

                    let placeX
                    let placeY

                    let info

                    if (place.element === 'earth') {
                        info = document.getElementById(`${place.element}-${event.id}-${place.id}-info`)

                        placeX = placeElement.x + (placeElement.width / 9 * place.position.x) + 30
                        placeY = placeElement.y + (placeElement.height / 9 * place.position.y) - 150

                    } else {
                        info = document.getElementById(`${place.element}-info`)

                        placeX = placeElement.x + 30 + place.position.x
                        placeY = placeElement.y - placeElement.height + place.position.y

                    }
                    this.setInformation(place.element, location.information, this.getLocation(location.place).name)

                    if (place.element === 'planet' || place.element === 'planet2') {
                        info.style.top = '100px'
                    }
                    info.style.transform = 'translate(' + (placeX) + 'px, ' + placeY + 'px)'

                    setTimeout(function () {
                        info.style.visibility = 'visible'
                        info.style.opacity = '1'
                        info.style.transition = 'visibility 0s linear 0s, opacity 300ms'
                    }, 500)

                    setTimeout(function () {
                        info.style.visibility = 'hidden'
                        info.style.opacity = '0'
                        info.style.transition = 'visibility 0s linear 300ms, opacity 300ms'
                    }, refreshRate - 500)
                })
                await new Promise(resolve => setTimeout(resolve, refreshRate))
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
                currentMovie: moviesJson.movies[0],
                isPlaying: false,
                newAnimationStarted: false,
                locationInformation: {
                    planet: {
                        text: '',
                        place: '',
                    },
                    planet2: {
                        text: '',
                        place: '',
                    },
                    planet3: {
                        text: '',
                        place: '',
                    },
                    planet4: {
                        text: '',
                        place: '',
                    },
                    asgard: {
                        text: '',
                        place: '',
                    },
                    space: {
                        text: '',
                        place: '',
                    },
                },
            }
        },
    }
</script>

<style lang="scss" scoped>
    @import '../assets/scss/variables.scss';

    .info {
        position   : fixed;
        visibility : hidden;
        left       : 0;
        top        : 0;
        z-index    : 3;
        opacity    : 0;
        transition : visibility 0s linear 300ms, opacity 300ms;
    }

    .grid {
        width                 : 100%;
        height                : 100%;
        position              : relative;
        display               : grid;
        grid-template-areas   : 'char char char'
                            'planets-left earth planets-right'
                            'title title title';
        grid-template-rows    : 20% auto 10%;
        grid-template-columns : 25% 50% 25%;
    }

    .title {
        grid-area       : title;
        display         : flex;
        justify-content : center;
        align-items     : center;

        h1 {
            color      : #84B893;
            transition : all 1s ease;

            &:hover {
                transform : scale(1.2);
            }

        }

    }

    .map {
        width     : 100%;
        height    : 500px;
        grid-area : earth;
        z-index   : 0;

        img {
            height  : 500px;
            display : block;
            margin  : auto;

        }
    }

    .planets-left {
        display               : grid;
        grid-area             : planets-left;
        grid-template-areas   : 'planet1 planet2'
                                'planet3 planet3'
                                'space space';
        grid-template-rows    : 200px auto;
        grid-template-columns : 200px auto;
        z-index               : -1;

        #planet img {
            width     : 200px;
            height    : auto;
            grid-area : planet1;
            z-index   : -1;
        }

        #planet2 img {
            width     : 50px;
            height    : auto;
            grid-area : planet2;
            z-index   : -1;
        }

        #planet3 img {
            width     : 100px;
            height    : auto;
            grid-area : planet3;
            z-index   : -1;
        }

        #space {
            width       : 100px;
            height      : auto;
            grid-area   : space;
            margin-left : 50px;
            z-index     : -1;
        }
    }

    .planets-right {
        display               : grid;
        grid-area             : planets-right;
        grid-template-areas   : 'asgard asgard '
                            ' empty planet4';
        grid-template-rows    : 100px auto;
        grid-template-columns : 200px auto;
        z-index               : -3;

        #asgard img {
            grid-area : asgard;
        }

        #planet4 img {
            width     : 500px;
            height    : auto;
            grid-area : planet4;
        }

    }

    .charList {
        display         : grid;
        float           : left;
        height          : 100px;
        width           : 100%;
        grid-area       : char;
        grid-auto-flow  : column;
        justify-content : center;
        grid-gap        : 5px;

        .char-wrapper {

            .label {
                color      : $primary_grey;
                visibility : hidden;
                transition : all 0.2s ease;
                margin-top : 10px;
                font-size  : 12px;
                height     : 50px;
                width      : 40px;
                text-align : center;
            }

            .character {
                position   : relative;
                height     : auto;
                width      : 40px;
                transition : all 1s ease;
                z-index    : 3;
            }

            &:hover {
                .label {
                    visibility : visible;
                    transform  : translateY(-10px);
                }
            }
        }
    }

</style>
