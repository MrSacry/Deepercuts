# Alternative Energy Sources Map

This interactive map displays power plants across the United States, color-coded by fuel source.

## Map Legend
- 🟡 **Yellow**: Solar
- 🟠 **Orange**: Geothermal
- 🔵 **Blue**: Biomass
- ⚪ **Gray**: Other fuel sources

<div id="map" style="width: 100%; height: 600px; border: 1px solid #ccc; margin: 20px 0;"></div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="power-plants.js"></script>

<script>
// Initialize map
const map = L.map('map').setView([39.8, -98.5], 4);

// Add tile layer
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
  attribution: '© OpenStreetMap contributors',
  maxZoom: 19
}).addTo(map);

// Color function based on fuel source
function getColor(fuelSource) {
  if (fuelSource.Solar) return '#FFD700';      // Yellow
  if (fuelSource.Geothermal) return '#FFA500'; // Orange
  if (fuelSource.Biomass) return '#4169E1';    // Blue
  return '#808080';                             // Gray for others
}

// Add markers from GeoJSON
if (typeof plants !== 'undefined') {
  plants.features.forEach(feature => {
    const props = feature.properties;
    const coords = feature.geometry.coordinates;
    const fuelSource = props.fuel_source;
    
    const color = getColor(fuelSource);
    
    const marker = L.circleMarker([coords[1], coords[0]], {
      radius: 6,
      fillColor: color,
      color: '#000',
      weight: 1,
      opacity: 1,
      fillOpacity: 0.8
    }).addTo(map);
    
    // Create popup content
    let fuelText = Object.entries(fuelSource)
      .map(([fuel, capacity]) => `${fuel}: ${capacity} MW`)
      .join('<br>');
    
    const popupContent = `
      <strong>${props.plant_name}</strong><br>
      Capacity: ${props.capacity_mw} MW<br>
      ${fuelText}
    `;
    
    marker.bindPopup(popupContent);
  });
}

// Add legend
const legend = L.control({position: 'bottomright'});
legend.onAdd = function() {
  const div = L.DomUtil.create('div', 'legend');
  div.innerHTML = `
    <div style="background: white; padding: 10px; border-radius: 5px; box-shadow: 0 0 15px rgba(0,0,0,0.2);">
      <p style="margin: 0 0 8px 0; font-weight: bold;">Fuel Source</p>
      <p style="margin: 4px 0;"><span style="display:inline-block; width:12px; height:12px; background:#FFD700; border: 1px solid #000; margin-right: 5px;"></span>Solar</p>
      <p style="margin: 4px 0;"><span style="display:inline-block; width:12px; height:12px; background:#FFA500; border: 1px solid #000; margin-right: 5px;"></span>Geothermal</p>
      <p style="margin: 4px 0;"><span style="display:inline-block; width:12px; height:12px; background:#4169E1; border: 1px solid #000; margin-right: 5px;"></span>Biomass</p>
      <p style="margin: 4px 0;"><span style="display:inline-block; width:12px; height:12px; background:#808080; border: 1px solid #000; margin-right: 5px;"></span>Other</p>
    </div>
  `;
  return div;
};
legend.addTo(map);
</script>

## Data Source
Power plant data is loaded from `power-plants.js`