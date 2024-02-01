<template>
    <div>
        <div id="map" style="height: 500px; width: 100%"></div>
        <q-toggle v-model="drawMode" :label="'Draw Mode: ' + (drawMode ? 'Circles' : 'Markers')" color="green" />
    </div>
</template>
  
<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';

import { defineComponent, ref } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'

export default defineComponent({
    name: 'MapPage',

    components: {
        EssentialLink
    },

    setup() {
        const leftDrawerOpen = ref(false)
        const drawMode = ref(false); // false for markers, true for circles

        const toggleLeftDrawer = () => {
            leftDrawerOpen.value = !leftDrawerOpen.value;
        };

        return {
            drawMode,
            essentialLinks: linksList,
            leftDrawerOpen,
            toggleLeftDrawer() {
                leftDrawerOpen.value = !leftDrawerOpen.value
            }
        }
    },
    mounted() {
        const map = L.map('map').setView([51.505, -0.09], 13);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // Marker
        const marker = L.marker([51.5, -0.09]).addTo(map);
        marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup();

        // Circle
        L.circle([51.508, -0.11], {
            color: 'red',
            fillColor: '#f03',
            fillOpacity: 0.5,
            radius: 500
        }).addTo(map);

        // Polygon
        L.polygon([
            [51.509, -0.08],
            [51.503, -0.06],
            [51.51, -0.047]
        ]).addTo(map);

        map.on('click', function (e) {
            let marker = L.marker(e.latlng).addTo(map);
            marker.bindPopup('<div>Marker at ' + e.latlng.toString() + '<br><button class="marker-delete-btn">Delete</button></div>');

            // Event listener for the delete button in the popup
            marker.on('popupopen', function () {
                let deleteButton = document.querySelector('.marker-delete-btn');
                deleteButton.addEventListener('click', function () {
                    map.removeLayer(marker);
                });
            });
        });

        map.on('click', function (e) {
            if (drawMode.value) {
                // Draw circle mode
                L.circle(e.latlng, {
                    color: 'red',
                    fillColor: '#f03',
                    fillOpacity: 0.5,
                    radius: 500
                }).addTo(map);
            } else {
                // Draw marker mode
                let marker = L.marker(e.latlng).addTo(map);
                marker.bindPopup('<div>Marker at ' + e.latlng.toString() + '<br><button class="marker-delete-btn">Delete</button></div>');

                marker.on('popupopen', function () {
                    let deleteButton = document.querySelector('.marker-delete-btn');
                    deleteButton.addEventListener('click', function () {
                        map.removeLayer(marker);
                    });
                });
            }
        });

    }
})
</script>
  