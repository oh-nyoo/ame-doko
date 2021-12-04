<template>
  <v-container div="globeViz" ref="globe">
  </v-container>
</template>

<script>
import Globe from 'globe.gl';

const gmap = "/img/map_dark4.png"
const bmap = "/img/bump12.png"
const back = "/img/night-sky.png"

const height = 725
const width = 544

const pointHeight = 0.1
const pointRadius = 0.8

const lat_off = -20
const lng_off = +10

const camera_alt = 2
const camera_alt_zoom = 0.8

export default {
    name: 'Globe',
    mounted: function () {
        this.myGlobe = Globe({animateIn: true});
        this.myGlobe(this.$refs["globe"])
            .globeImageUrl(gmap)
            .bumpImageUrl(bmap)
            .backgroundImageUrl(back)
            .width(width)
            .height(height)

        if(this.country) {
            this.myGlobe.pointsData([this.country.pointsData])
                    .pointAltitude(pointHeight)
                    .pointColor('color')
                    .pointLabel('country')
                    .pointRadius(pointRadius)
                    .pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500)
        }
    },
    watch: {
        'origin'() {
            console.log('origin')
        },
        'country'() {
            if(this.country) {
                this.myGlobe.pointsData([this.country.pointsData])
                        .pointAltitude(0.1)
                        .pointColor('color')
                        .pointLabel('country')
                        .pointRadius(pointRadius)
                        .pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500)
            }
        },
        'tutorial'() {
            if(this.country) {
                this.myGlobe.pointsData([this.country.pointsData])
                        .pointAltitude(0.1)
                        .pointColor('color')
                        .pointLabel('country')
                        .pointRadius(pointRadius)
                        .pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500)
            }
        },
        'investigate'() {
            if(this.investigate) {
                this.myGlobe.pointOfView({ lat: this.country.pointsData.lat, lng: this.country.pointsData.lng, altitude: camera_alt_zoom}, 500)
                    .pointRadius(pointRadius * (camera_alt_zoom / camera_alt))
            }
            else {
                this.myGlobe
                        .pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500).pointRadius(pointRadius)
            }

        },
        'departures'() {
            if(this.departing > 0) { return }
            if(this.investigate) {
                //this.myGlobe.pointOfView({ lat: this.country.pointsData.lat, lng: this.country.pointsData.lng, altitude: camera_alt_zoom}, 50)
                    //.pointRadius(pointRadius * (camera_alt_zoom / camera_alt))

            }
            else {
                this.myGlobe.pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500)
                this.myGlobe.pointsData([this.country.pointsData])
                    .pointAltitude(0.1)
                    .pointColor('color')
                    .pointLabel('country')
                    .pointRadius(pointRadius)
                    .pointOfView({ lat: this.country.pointsData.lat+lat_off, lng: this.country.pointsData.lng+lng_off, altitude: camera_alt}, 500)
            }
        },
        'destination'() {
            if(this.departing > 0) { return }
            if(this.destination) {
                this.myGlobe.pointsData([this.destination.pointsData, this.country.pointsData])
                    .pointAltitude(0.1)
                    .pointColor('color')
                    .pointLabel('country')
                    .pointRadius(pointRadius)
                    .pointOfView({ lat: this.destination.pointsData.lat+lat_off, lng: this.destination.pointsData.lng+lng_off, altitude: camera_alt}, 500)
            }
        },
        'departing'() {
            if(this.departing > 0) {
                let arcs = [{ startLat: this.country.pointsData.lat,
                              startLng: this.country.pointsData.lng,
                              endLat: this.destination.pointsData.lat, 
                              endLng: this.destination.pointsData.lng, 
                }]
                this.myGlobe
                    .pointAltitude(0)
                    .pointOfView({ lat: this.country.pointsData.lat, lng: this.country.pointsData.lng, altitude: camera_alt_zoom}, 500)
                    .pointRadius(pointRadius * 0.75)
                    .arcsData(arcs)
                    .arcDashLength(0.10)
                    .arcDashGap(0.2)
                    .arcDashAnimateTime(2500)
                    .arcAltitude(0.2)
                    .arcStroke(2)
                    .arcColor(() => '#00ff00')
                let interval = setInterval(() => {
                    clearInterval(interval)
                    this.myGlobe
                        .pointAltitude(0)
                        .pointOfView({ lat: this.destination.pointsData.lat, lng: this.destination.pointsData.lng, altitude: camera_alt_zoom}, 2000)
                    .pointRadius(pointRadius * 0.75)
                }, 500)
                let interval2 = setInterval(() => {
                    clearInterval(interval2)
                    this.myGlobe.arcsData([])
                }, 2500)

            }
        }
    },
    props: ['country', 'destination', 'investigate', 'departures', 'departing', 'tutorial'],
    data: () => ({
    }),
  }
</script>
<style>
.container {
        padding: 0 !important;
}
canvas {
    border-radius: 4px;
}
</style>
