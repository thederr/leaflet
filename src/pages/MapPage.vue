<template>
    <div class="q-pt-xl">
        <div id="map" style="height: 500px;"></div>
        <!-- Add UI elements here to allow the user to pick a color -->
        <button @click="updateDrawControlColor('red')">Red</button>
        <button @click="updateDrawControlColor('yellow')">Yellow</button>
        <button @click="updateDrawControlColor('green')">Green</button>
    </div>
</template>
  
<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import 'leaflet-draw';
import 'leaflet-draw/dist/leaflet.draw.css';

export default {
    name: 'LeafletMap',

    data() {
        return {
            map: null,
            drawControl: null,
            currentColor: 'red',
            redLayers: new L.FeatureGroup(),
            yellowLayers: new L.FeatureGroup(),
            greenLayers: new L.FeatureGroup(),
        };
    },

    methods: {
        updateDrawControlColor(color) {
            this.currentColor = color;
            this.map.removeControl(this.drawControl);
            this.drawControl = new L.Control.Draw(this.getDrawOptions(color));
            this.map.addControl(this.drawControl);
        },

        getDrawOptions(color) {
            return {
                edit: {
                    featureGroup: this.getLayerGroup(color),
                    poly: {
                        allowIntersection: false,
                    },
                },
                draw: {
                    polyline: false,
                    rectangle: false,
                    polygon: true,
                    marker: true,
                    circlemarker: false,
                    circle: {
                        shapeOptions: {
                            color: color,
                        },
                    },
                },
            };
        },

        getLayerGroup(color) {
            switch (color) {
                case 'red':
                    return this.redLayers;
                case 'yellow':
                    return this.yellowLayers;
                case 'green':
                    return this.greenLayers;
                default:
                    return new L.FeatureGroup(); // Fallback layer group
            }
        },

        addLayerToCorrectGroup(layer) {
            this.getLayerGroup(this.currentColor).addLayer(layer);
        },
    },

    mounted() {
        this.map = L.map('map').setView([51.505, -0.09], 13);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors',
        }).addTo(this.map);

        this.map.addLayer(this.redLayers);
        this.map.addLayer(this.yellowLayers);
        this.map.addLayer(this.greenLayers);

        this.drawControl = new L.Control.Draw(this.getDrawOptions(this.currentColor));
        this.map.addControl(this.drawControl);

        this.map.on(L.Draw.Event.CREATED, (event) => {
            const layer = event.layer;
            this.addLayerToCorrectGroup(layer);
        });
    },
};
</script>
  
<style scoped>
/* Add styles if needed */
</style>
  