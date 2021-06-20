<template>
    <div>
        <div class="map" id="chartdiv" />
    </div>
</template>

<script>
    import * as am4core from '@amcharts/amcharts4/core'
    import * as am4maps from '@amcharts/amcharts4/maps'
    import am4themes_animated from '@amcharts/amcharts4/themes/animated'
    import am4geodata_worldLow from '@amcharts/amcharts4-geodata/worldLow'
    import json from '../../json/movies.json'

    am4core.useTheme(am4themes_animated)

    export default {
        name: 'Map',

        data () {
            return {
                map: null,
                polygonSeries: null,
                polygonTemplate: null,
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
                    {
                        'name': 'planet',
                        'id': 2,
                        'image': 'planet.png',
                    },
                ],
            }
        },
        props: {
            deltaLongitude: {
                type: Number,
                required: false,
                default: 0,
            },
            countries: {
                type: Array,
                required: false,
                default () {
                    return []
                },
            },
        },
        watch: {
            deltaLongitude: function (newVal, oldVal) {
                this.moveGlobe(newVal)
            },
            countries: function (newVal, oldVal) {
                this.polygonSeries.data = newVal
            },
        },
        methods: {
            moveGlobe (newLong) {
                for (let i = 1; i <= newLong; ++i) {
                    this.map.deltaLongitude = this.doSetTimeout(i)
                }

            },
            doSetTimeout (i, long) {
                setTimeout(function () {
                    long = long + 1
                    return long
                }, 1000)
                return long
            },
            createMap () {
                // Create map instance
                this.map = am4core.create('chartdiv', am4maps.MapChart)

                // Set map definition
                this.map.geodata = am4geodata_worldLow

                // Set projection
                this.map.projection = new am4maps.projections.EqualEarth()

                // rotate the earth
               // this.map.panBehavior = 'rotateLongLat'

                // Create map polygon series
                this.polygonSeries = this.map.series.push(new am4maps.MapPolygonSeries())

                // Make map load polygon (like country names) data from GeoJSON
                this.polygonSeries.useGeodata = true

                // Configure series
                this.polygonTemplate = this.polygonSeries.mapPolygons.template
                this.polygonTemplate.tooltipText = '{name}'
                this.polygonTemplate.fill = am4core.color('#74B266')

                // Create hover state and set alternative fill color
                const hs = this.polygonTemplate.states.create('hover')
                hs.properties.fill = am4core.color('#367B25')

                // Remove Antarctica
                //this.polygonSeries.exclude = ['AQ']

                // set background color
                this.map.backgroundSeries.mapPolygons.template.polygon.fill = am4core.color('#24B6F2')
                this.map.backgroundSeries.mapPolygons.template.polygon.fillOpacity = 1

                this.highlightPlaces()
                // this.polygonSeries.data = this.countries
                // this.polygonTemplate.propertyFields.fill = 'fill'

            },

            highlightPlaces () {
                // Add some data
                this.polygonSeries.data = [
                    {
                        'id': 'US',
                        'name': 'United States',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'DE',
                        'name': 'germany',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'ET',
                        'name': 'Wakanda',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'IT',
                        'name': 'Italy',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'NL',
                        'name': 'Netherlands',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'VK',
                        'name': 'Londen',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'NL',
                        'name': 'Netherlands',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'VK',
                        'name': 'Londen',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'NL',
                        'name': 'Netherlands',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'BR',
                        'name': 'Brazil',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'AF',
                        'name': 'Afghanistan',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'MC',
                        'name': 'Monaco',
                        'value': 50,
                        'fill': am4core.color('#ff5cc3'),
                    },
                    {
                        'id': 'NO',
                        'name': 'Norway',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'NM',
                        'name': 'New Mexico',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'IN',
                        'name': 'India',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'ZA',
                        'name': 'South africa',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'RU',
                        'name': 'Siberia',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'PT',
                        'name': 'Lagos',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'AT',
                        'name': 'Vienna',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'RO',
                        'name': 'Bucharest',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'KR',
                        'name': 'Busan',
                        'value': 100,
                        'fill': am4core.color('#F05C5C'),
                    }, {
                        'id': 'NP',
                        'name': 'Kathmandu',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'HK',
                        'name': 'Hong kong',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                    {
                        'id': 'MX',
                        'name': 'Mexico',
                        'value': 50,
                        'fill': am4core.color('#5C5CFF'),
                    },
                ]

                // Bind "fill" property to "fill" key in data
                this.polygonTemplate.propertyFields.fill = 'fill'

            },
            setPointers () {
                // Create image series
                var imageSeries = this.map.series.push(new am4maps.MapImageSeries())

                // Create a circle image in image series template so it gets replicated to all new images
                var imageSeriesTemplate = imageSeries.mapImages.template
                var marker = imageSeriesTemplate.createChild(am4core.Image)
                marker.href = 'https://s3-us-west-2.amazonaws.com/s.cdpn.io/t-160/marker.svg'
                marker.width = 20
                marker.height = 20
                marker.nonScaling = true
                marker.tooltipText = '{title}'
                marker.horizontalCenter = 'middle'
                marker.verticalCenter = 'bottom'

                // Set property fields
                imageSeriesTemplate.propertyFields.latitude = 'latitude'
                imageSeriesTemplate.propertyFields.longitude = 'longitude'

                // Add data for the three cities
                imageSeries.data = [
                    {
                        'latitude': 48.856614,
                        'longitude': 2.352222,
                        'title': 'Paris',
                    }, {
                        'latitude': 36.261522,
                        'longitude': -74.005973,
                        'title': 'New York',
                    }, {
                        'latitude': 49.282729,
                        'longitude': -123.120738,
                        'title': 'Vancouver',
                    },
                ]
            },
        },
        mounted () {
            this.createMap()
            // this.highlightPlaces()
            // this.setPointers()

        },

        beforeDestroy () {
            if (this.map) {

                this.map.dispose()
            }
        },
    }

</script>

<style lang="scss" scoped>
    #chartdiv {
        width  : auto;
        height : 500px;
    }

    .inactive {
        display : none;
    }
</style>
