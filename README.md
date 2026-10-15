# toilet-finder
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="utf-8">
  <title>ToiletFinder</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="default">
  <meta name="apple-mobile-web-app-title" content="ToiletFinder">
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <link rel="stylesheet" href="https://unpkg.com/leaflet-routing-machine@latest/dist/leaflet-routing-machine.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://unpkg.com/leaflet-routing-machine@latest/dist/leaflet-routing-machine.js"></script>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      overflow: hidden;
      background: #f5f5f5;
    }
    #map {
      height: 100vh;
      height: 100dvh;
      width: 100vw;
      z-index: 1;
    }
    .top-container {
      position: absolute;
      top: 12px;
      left: 50%;
      transform: translateX(-50%);
      width: 92%;
      max-width: 450px;
      z-index: 1000;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }
    .header-card {
      background: rgba(255, 255, 255, 0.95);
      padding: 10px 16px;
      border-radius: 20px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.12);
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .app-title {
      font-weight: 800;
      font-size: 16px;
      color: #222;
      display: flex;
      align-items: center;
      gap: 6px;
    }
    .status-badge {
      font-size: 11px;
      background: #e3f2fd;
      color: #1976d2;
      padding: 4px 10px;
      border-radius: 12px;
      font-weight: 600;
    }
    .filter-bar {
      display: flex;
      gap: 6px;
      justify-content: center;
    }
    .filter-btn {
      background: rgba(255, 255, 255, 0.95);
      border: 2px solid transparent;
      padding: 7px 14px;
      border-radius: 18px;
      font-size: 12px;
      font-weight: 700;
      color: #555;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      cursor: pointer;
      display: flex;
      align-items: center;
      gap: 5px;
    }
    .filter-btn.active {
      border-color: #2196F3;
      color: #2196F3;
      background: #fff;
    }
    .dot {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      display: inline-block;
    }
    .bottom-bar {
      position: absolute;
      bottom: 24px;
      left: 50%;
      transform: translateX(-50%);
      width: 92%;
      max-width: 450px;
      z-index: 1000;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 10px;
    }
    .action-btn {
      flex: 1;
      background: white;
      border: none;
      padding: 13px 10px;
      border-radius: 16px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.15);
      font-weight: 700;
      font-size: 13px;
      color: #333;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 6px;
    }
    .sos-btn {
      background: #e53935;
      color: white;
      flex: 1.3;
    }
    .icon-btn {
      width: 46px;
      height: 46px;
      border-radius: 50%;
      background: white;
      border: none;
      box-shadow: 0 4px 15px rgba(0,0,0,0.15);
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      font-size: 20px;
    }
    .clear-route-btn {
      position: absolute;
      bottom: 82px;
      left: 50%;
      transform: translateX(-50%);
      background: #333;
      color: white;
      border: none;
      padding: 8px 16px;
      border-radius: 20px;
      font-size: 12px;
      font-weight: 600;
      z-index: 1000;
      display: none;
      cursor: pointer;
      box-shadow: 0 3px 10px rgba(0,0,0,0.2);
    }
    .custom-pin {
      background: none;
      border: none;
    }
    .leaflet-popup-content {
      margin: 12px;
      text-align: center;
      min-width: 160px;
    }
    .popup-tag {
      display: inline-block;
      font-size: 10px;
      font-weight: 700;
      padding: 2px 8px;
      border-radius: 10px;
      margin-bottom: 4px;
    }
    .tag-toilet { background: #fff8e1; color: #f57f17; }
    .tag-shop { background: #ffebee; color: #c62828; }
    .popup-title { font-size: 15px; font-weight: 800; color: #222; }
    .popup-sub { font-size: 12px; color: #666; margin-bottom: 8px; }
    .popup-meta {
      background: #f5f5f5;
      padding: 6px;
      border-radius: 8px;
      font-size: 12px;
      font-weight: 700;
      color: #1976d2;
      margin-bottom: 8px;
    }
    .route-go-btn {
      background: #2196F3;
      color: white;
      border: none;
      width: 100%;
      padding: 8px;
      border-radius: 8px;
      font-weight: 700;
      font-size: 12px;
      cursor: pointer;
    }
    .modal-overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background: rgba(0,0,0,0.5);
      z-index: 2000;
      display: none;
      justify-content: center;
      align-items: flex-end;
    }
    .modal-sheet {
      background: white;
      width: 100%;
      max-width: 500px;
      max-height: 82vh;
      border-radius: 24px 24px 0 0;
      padding: 20px;
      box-sizing: border-box;
      overflow-y: auto;
    }
    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 14px;
    }
    .modal-title { font-size: 18px; font-weight: 800; }
    .close-btn {
      background: #eee;
      border: none;
      width: 32px;
      height: 32px;
      border-radius: 50%;
      font-weight: bold;
      cursor: pointer;
    }
    .phrase-card {
      background: #fff8e1;
      border: 2px solid #ffc107;
      border-radius: 14px;
      padding: 14px;
      text-align: center;
      margin-bottom: 16px;
    }
    .phrase-jp { font-size: 18px; font-weight: 800; color: #222; margin: 6px 0; }
    .phrase-romaji { font-size: 12px; color: #555; }
    .guide-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }
    .guide-item {
      border: 1px solid #e0e0e0;
      border-radius: 12px;
      padding: 10px;
      display: flex;
      flex-direction: column;
      align-items: center;
      text-align: center;
    }
    .kanji-box {
      background: #f0f4f8;
      font-size: 20px;
      font-weight: 800;
      color: #1976d2;
      padding: 6px 14px;
      border-radius: 8px;
      margin-bottom: 6px;
    }
    .guide-en { font-size: 13px; font-weight: 700; color: #333; }
    .guide-desc { font-size: 11px; color: #777; }
  </style>
</head>
<body>

  <div class="top-container">
    <div class="header-card">
      <div class="app-title">🚻 Japan Toilet Finder</div>
      <div class="status-badge" id="statusText">Locating...</div>
    </div>
    <div class="filter-bar">
      <button class="filter-btn active" id="btnAll">All</button>
      <button class="filter-btn" id="btnToilet">
        <span class="dot" style="background:#FFC107;"></span>Public Toilet
      </button>
      <button class="filter-btn" id="btnShop">
        <span class="dot" style="background:#F44336;"></span>Convenience
      </button>
    </div>
  </div>

  <div id="map"></div>

  <button class="clear-route-btn" id="clearRouteBtn">✕ ルート案内を終了 (Clear Route)</button>

  <div class="bottom-bar">
    <button class="action-btn sos-btn" id="nearestBtn">🚨 Nearest (一番近い所)</button>
    <button class="action-btn" id="guideBtn">📖 Toilet Guide</button>
    <button class="icon-btn" id="locateBtn">🎯</button>
  </div>

  <div class="modal-overlay" id="guideModal">
    <div class="modal-sheet">
      <div class="modal-header">
        <div class="modal-title">🇯🇵 Japan Toilet Survival Guide</div>
        <button class="close-btn" id="closeModalBtn">✕</button>
      </div>

      <div class="phrase-card">
        <div style="font-size:11px; font-weight:700; color:#f57f17;">SHOW THIS TO CONVENIENCE STORE STAFF</div>
        <div class="phrase-jp">すみません、トイレを借りてもいいですか？</div>
        <div class="phrase-romaji">"Sumimasen, toire wo karitemo ii desu ka?"<br>(Excuse me, may I use the restroom?)</div>
      </div>

      <div style="font-size:14px; font-weight:800; margin-bottom:10px; color:#333;">Washlet Button Meanings (ボタンの意味)</div>
      <div class="guide-grid">
        <div class="guide-item">
          <div class="kanji-box">流す / 大・小</div>
          <div class="guide-en">FLUSH</div>
          <div class="guide-desc">Big (大) / Small (小) flush</div>
        </div>
        <div class="guide-item">
          <div class="kanji-box" style="color:#e53935;">止</div>
          <div class="guide-en">STOP</div>
          <div class="guide-desc">Stop water spray</div>
        </div>
        <div class="guide-item">
          <div class="kanji-box">おしり</div>
          <div class="guide-en">REAR WASH</div>
          <div class="guide-desc">Warm water wash</div>
        </div>
        <div class="guide-item">
          <div class="kanji-box">ビデ</div>
          <div class="guide-en">BIDET</div>
          <div class="guide-desc">Feminine wash</div>
        </div>
        <div class="guide-item">
          <div class="kanji-box">音姫 / 音</div>
          <div class="guide-en">PRIVACY SOUND</div>
          <div class="guide-desc">Plays flushing sound</div>
        </div>
        <div class="guide-item">
          <div class="kanji-box" style="color:#d32f2f;">呼出 / 非常</div>
          <div class="guide-en">EMERGENCY</div>
          <div class="guide-desc">Do NOT press unless urgent!</div>
        </div>
      </div>
    </div>
  </div>

  <script>
    (function setupAppIcon() {
      var canvas = document.createElement('canvas');
      canvas.width = 180;
      canvas.height = 180;
      var ctx = canvas.getContext('2d');
      ctx.fillStyle = '#2196F3';
      ctx.fillRect(0, 0, 180, 180);
      ctx.font = '90px sans-serif';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText('🚻', 90, 95);
      var link = document.createElement('link');
      link.rel = 'apple-touch-icon';
      link.href = canvas.toDataURL('image/png');
      document.head.appendChild(link);
    })();

    function createIcon(color) {
      var svg = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="${color}" width="38px" height="38px">
        <path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z" stroke="white" stroke-width="1.5"/>
      </svg>`;
      return L.divIcon({
        className: 'custom-pin',
        html: svg,
        iconSize: [38, 38],
        iconAnchor: [19, 38],
        popupAnchor: [0, -36]
      });
    }

    var blueIcon = createIcon('#2196F3');
    var redIcon = createIcon('#F44336');
    var yellowIcon = createIcon('#FFC107');

    var map = L.map('map', { zoomControl: false }).setView([35.681236, 139.767125], 14);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: 'OpenStreetMap'
    }).addTo(map);

    var userLat = 35.681236;
    var userLng = 139.767125;
    var userMarker = null;
    var routingControl = null;
    var allPlaces = [];
    var currentFilter = 'all';

    function updateStatus(text) {
      document.getElementById('statusText').innerText = text;
    }

    function drawRoute(destLat, destLng) {
      if (routingControl) {
        map.removeControl(routingControl);
      }
      routingControl = L.Routing.control({
        waypoints: [
          L.latLng(userLat, userLng),
          L.latLng(destLat, destLng)
        ],
        routeWhileDragging: false,
        addWaypoints: false,
        show: false
      }).addTo(map);
      document.getElementById('clearRouteBtn').style.display = 'block';
      map.closePopup();
    }

    window.startRouteTo = function(lat, lng) {
      drawRoute(lat, lng);
    };

    function renderMarkers() {
      allPlaces.forEach(function(item) {
        map.removeLayer(item.marker);
      });

      var visibleCount = 0;
      allPlaces.forEach(function(item) {
        if (currentFilter === 'all' || currentFilter === item.type) {
          item.marker.addTo(map);
          visibleCount++;
        }
      });
      updateStatus(visibleCount + " spots found");
    }

    function fetchPlaces(lat, lng) {
      updateStatus("Searching...");
      var overpassUrl = `https://overpass-api.de/api/interpreter?data=[out:json];(nwr(around:5000,${lat},${lng})["amenity"="toilets"];nwr(around:5000,${lat},${lng})["shop"="convenience"];);out center;`;

      fetch(overpassUrl)
        .then(function(res) { return res.json(); })
        .then(function(data) {
          allPlaces.forEach(function(item) { map.removeLayer(item.marker); });
          allPlaces = [];

          if (!data.elements || data.elements.length === 0) {
            updateStatus("No spots nearby");
            return;
          }

          data.elements.forEach(function(el) {
            var pLat = el.lat || (el.center && el.center.lat);
            var pLon = el.lon || (el.center && el.center.lon);
            if (!pLat || !pLon) return;

            var isShop = el.tags && el.tags.shop === "convenience";
            var type = isShop ? 'shop' : 'toilet';
            var icon = isShop ? redIcon : yellowIcon;

            var brandName = (el.tags && (el.tags.name || el.tags.brand)) ? (el.tags.name || el.tags.brand) : "";
            var titleJa = isShop ? (brandName || "コンビニ") : (brandName || "公衆トイレ");
            var titleEn = isShop ? "Convenience Store" : "Public Restroom";
            var tagClass = isShop ? "tag-shop" : "tag-toilet";
            var tagLabel = isShop ? "SHOP / 24H" : "PUBLIC FREE";

            var wheelchairInfo = "";
            if (el.tags && el.tags.wheelchair === "yes") {
              wheelchairInfo = " ♿ Accessible";
            }

            var distMeters = Math.round(map.distance([userLat, userLng], [pLat, pLon]));
            var walkMins = Math.max(1, Math.round(distMeters / 80));

            var popupHtml = `
              <div class="popup-tag ${tagClass}">${tagLabel}${wheelchairInfo}</div>
              <div class="popup-title">${titleJa}</div>
              <div class="popup-sub">${titleEn}</div>
              <div class="popup-meta">🚶 約 ${walkMins} 分 (${distMeters}m)</div>
              <button class="route-go-btn" onclick="startRouteTo(${pLat}, ${pLon})">ここへ行く (Go Here)</button>
            `;

            var marker = L.marker([pLat, pLon], { icon: icon });
            marker.bindPopup(popupHtml);

            allPlaces.push({
              lat: pLat,
              lng: pLon,
              type: type,
              dist: distMeters,
              marker: marker
            });
          });

          renderMarkers();
        })
        .catch(function() {
          updateStatus("Server busy");
        });
    }

    function initLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(function(pos) {
          userLat = pos.coords.latitude;
          userLng = pos.coords.longitude;
          map.setView([userLat, userLng], 14);

          if (userMarker) map.removeLayer(userMarker);
          userMarker = L.marker([userLat, userLng], { icon: blueIcon }).addTo(map);
          userMarker.bindPopup("<div class='popup-title'>現在地</div><div class='popup-sub'>You are here</div>");

          fetchPlaces(userLat, userLng);
        }, function() {
          updateStatus("GPS Off");
          userMarker = L.marker([userLat, userLng], { icon: blueIcon }).addTo(map);
          fetchPlaces(userLat, userLng);
        });
      }
    }

    initLocation();

    function setFilter(filterType, activeBtnId) {
      currentFilter = filterType;
      document.getElementById('btnAll').classList.remove('active');
      document.getElementById('btnToilet').classList.remove('active');
      document.getElementById('btnShop').classList.remove('active');
      document.getElementById(activeBtnId).classList.add('active');
      renderMarkers();
    }

    document.getElementById('btnAll').addEventListener('click', function() { setFilter('all', 'btnAll'); });
    document.getElementById('btnToilet').addEventListener('click', function() { setFilter('toilet', 'btnToilet'); });
    document.getElementById('btnShop').addEventListener('click', function() { setFilter('shop', 'btnShop'); });

    document.getElementById('nearestBtn').addEventListener('click', function() {
      var candidates = allPlaces.filter(function(item) {
        return currentFilter === 'all' || currentFilter === item.type;
      });
      if (candidates.length === 0) {
        alert("周辺にスポットが見つからないよ！");
        return;
      }
      candidates.sort(function(a, b) { return a.dist - b.dist; });
      var nearest = candidates[0];
      map.setView([nearest.lat, nearest.lng], 16);
      nearest.marker.openPopup();
      drawRoute(nearest.lat, nearest.lng);
    });

    document.getElementById('clearRouteBtn').addEventListener('click', function() {
      if (routingControl) {
        map.removeControl(routingControl);
        routingControl = null;
      }
      this.style.display = 'none';
    });

    document.getElementById('locateBtn').addEventListener('click', function() {
      map.setView([userLat, userLng], 15);
      if (userMarker) userMarker.openPopup();
    });

    document.getElementById('guideBtn').addEventListener('click', function() {
      document.getElementById('guideModal').style.display = 'flex';
    });

    document.getElementById('closeModalBtn').addEventListener('click', function() {
      document.getElementById('guideModal').style.display = 'none';
    });
  </script>
</body>
</html>
