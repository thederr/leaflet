<template>
    <div>
        <div id="map" style="height: 500px;"></div>
    </div>
</template>
  
<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import 'leaflet-draw';
import 'leaflet-draw/dist/leaflet.draw.css';

export default {
    name: 'LeafletMap',

    mounted() {
        // Initialize the map
        const map = L.map('map').setView([51.505, -0.09], 13);

        // Add OpenStreetMap tile layer to the map
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors',
        }).addTo(map);

        // Initialize the FeatureGroup to store editable layers
        const editableLayers = new L.FeatureGroup();
        map.addLayer(editableLayers);

        // Initialize the draw control and pass it the FeatureGroup of editable layers
        const drawControl = new L.Control.Draw({
            edit: {
                featureGroup: editableLayers,
                poly: {
                    allowIntersection: false,
                },
            },
            draw: {
                polyline: false,
                rectangle: false,
                polygon: false,
                marker: true,
                circlemarker: false,
                // Enable drawing circles
                circle: true,
            },
        });
        map.addControl(drawControl);

        // Handle the creation of shapes
        map.on(L.Draw.Event.CREATED, function (event) {
            const layer = event.layer;
            editableLayers.addLayer(layer);
            // Do something with the layer, e.g., save the coordinates
        });
    },
};
</script>
  
<style scoped>
#map {
    width: 100%;
    height: 100%;
}
</style>
  