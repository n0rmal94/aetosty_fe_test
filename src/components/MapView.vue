<template>
  <div class="map-container">
    <!-- Filter Panel -->
    <div class="filter-panel">
      <div class="filter-group">
        <label for="severity">Severity:</label>
        <select id="severity" v-model="selectedSeverity" @change="fetchEvents">
          <option value="">All</option>
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
        </select>
      </div>

      <div class="filter-group">
        <label for="keyword">Search:</label>
        <input id="keyword" v-model="searchKeyword" @input="fetchEvents" placeholder="Search description..." />
      </div>

      <button @click="resetFilters">Reset Filters</button>
    </div>

    <!-- Map -->
    <div ref="mapContainer" class="map"></div>

    <!-- Event Detail Panel -->
    <div v-if="selectedEvent" class="event-panel">
      <h3>Event Details</h3>
      <p><strong>Timestamp:</strong> {{ selectedEvent.timestamp }}</p>
      <p><strong>Severity:</strong> {{ selectedEvent.severity }}</p>
      <p><strong>Description:</strong> {{ selectedEvent.description }}</p>
    </div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl';

export default {
  name: 'MapView',
  data() {
    return {
      map: null,
      events: [],
      selectedEvent: null,
      selectedSeverity: '',
      searchKeyword: '',
      markers: [],
    };
  },
  mounted() {
    mapboxgl.accessToken = process.env.VUE_APP_MAPBOX_TOKEN;
    this.map = new mapboxgl.Map({
      container: this.$refs.mapContainer,
      style: 'mapbox://styles/mapbox/streets-v11',
      center: [105, 20],
      zoom: 2,
    });

    this.map.on('load', () => {
      this.fetchEvents();
    });
  },
  methods: {
    async fetchEvents() {
      let apiBackend = process.env.VUE_APP_API_URL;

      try {
        let url = new URL(`${apiBackend}/events`);

        if (this.selectedSeverity) {
          url.searchParams.append('severity', this.selectedSeverity);
        }

        const res = await fetch(url);
        let data = await res.json();

        // Filter on client by description
        if (this.searchKeyword.trim()) {
          const keyword = this.searchKeyword.toLowerCase();
          data = data.filter(event =>
            event.description.toLowerCase().includes(keyword)
          );
        }

        this.events = data;
        this.clearMarkers();
        this.addMarkers();
      } catch (err) {
        console.error('Failed to fetch events:', err);
      }
    },
    clearMarkers() {
      this.markers.forEach(marker => marker.remove());
      this.markers = [];
    },
    addMarkers() {
      this.events.forEach((event) => {
        const el = document.createElement('div');
        el.className = 'marker';
        el.style.backgroundColor = this.getMarkerColor(event.severity);
        el.style.width = '14px';
        el.style.height = '14px';
        el.style.borderRadius = '50%';
        el.style.border = '2px solid white';
        el.style.cursor = 'pointer';

        const marker = new mapboxgl.Marker(el)
          .setLngLat([event.location.longitude, event.location.latitude])
          .addTo(this.map);

        marker.getElement().addEventListener('click', () => {
          this.selectedEvent = event;
        });

        this.markers.push(marker);
      });
    },
    getMarkerColor(severity) {
      switch (severity) {
        case 'low': return 'green';
        case 'medium': return 'orange';
        case 'high': return 'red';
        default: return 'gray';
      }
    },
    resetFilters() {
      this.selectedSeverity = '';
      this.searchKeyword = '';
      this.fetchEvents();
    },
  },
};
</script>

<style scoped>
.map-container {
  position: relative;
  height: 100vh;
  width: 100vw;
}

.map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}

.marker {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  cursor: pointer;
}

.event-panel {
  position: absolute;
  top: 100px;
  right: 10px;
  background: white;
  padding: 12px;
  border-radius: 6px;
  box-shadow: 0 0 10px rgba(0,0,0,0.2);
  width: 250px;
  z-index: 9;
}

.filter-panel {
  position: absolute;
  top: 10px;
  left: 10px;
  background: white;
  padding: 12px;
  border-radius: 6px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
  display: flex;
  gap: 12px;
  align-items: center;
  z-index: 10;
}

.filter-group {
  display: flex;
  flex-direction: column;
}
</style>
