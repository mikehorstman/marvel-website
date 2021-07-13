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
            }
        },
        props: {
            deltaLongitude: {
                type: Number,
                required: false,
                default: 0,
            }
        },
        methods: {
            createMap () {
                // Create map instance
                this.map = am4core.create('chartdiv', am4maps.MapChart)

                // Set map definition
                this.map.geodata = am4geodata_worldLow

                // Set projection
                this.map.projection = new am4maps.projections.EqualEarth()

                // Create map polygon series
                this.polygonSeries = this.map.series.push(new am4maps.MapPolygonSeries())

                this.map.maxZoomLevel = 1;

                // Make map load polygon (like country names) data from GeoJSON
                this.polygonSeries.useGeodata = true

                // Configure series
                this.polygonTemplate = this.polygonSeries.mapPolygons.template
                this.polygonTemplate.tooltipText = '{name}'
                this.polygonTemplate.fill = am4core.color('#84B893')

                // Create hover state and set alternative fill color
                const hs = this.polygonTemplate.states.create('hover')
                hs.properties.fill = am4core.color('#367B25')

                // set background color
                this.map.backgroundSeries.mapPolygons.template.polygon.fill = am4core.color('#39829A')
                this.map.backgroundSeries.mapPolygons.template.polygon.fillOpacity = 1


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
