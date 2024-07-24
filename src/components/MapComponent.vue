<template>
  <div id="map" style="height: 600px;"></div>
</template>

<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import axios from 'axios';
import 'leaflet-measure/dist/leaflet-measure.css';
import 'leaflet-measure';

export default {
  mounted() {
    console.log('Vue component mounted');
    this.map = L.map('map').setView([22.5431, 114.0579], 12);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19
    }).addTo(this.map);

    this.map.addControl(new L.Control.Measure({
      primaryLengthUnit: 'meters',
      secondaryLengthUnit: 'kilometers',
      primaryAreaUnit: 'sqmeters',
      secondaryAreaUnit: 'hectares'
    }));

    console.log('Sending Axios request to fetch AIS data');
    axios.get('/api/ais-data')
      .then(response => {
        console.log('Response received:', response);
        const { trajectories} = response.data;
        this.drawTrajectories(trajectories);
      })
      .catch(error => {
        console.error('Error fetching AIS data:', error);
      });
  },
  methods: {
    drawTrajectories(trajectories) {
      trajectories.forEach(trajectory => {
        const latlngs = trajectory.trajectory.coordinates.map(coord => [coord[1], coord[0]]);
        L.polyline(latlngs, { color: 'blue' }).addTo(this.map);
      });
    }
  }
};
</script>

<style>
#map {
  height: 100%;
}
</style>
