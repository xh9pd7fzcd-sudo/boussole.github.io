# boussole.github.io
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Boussole GPS JS</title>

<style>
  body {
    background: #020617;
    color: white;
    text-align: center;
    font-family: Arial, sans-serif;
  }

  #compass {
    width: 260px;
    height: 260px;
    border: 6px solid cyan;
    border-radius: 50%;
    margin: 40px auto;
    position: relative;
    box-shadow: 0 0 25px cyan;
  }

  /* FLÈCHE ROUGE */
  #needle {
    position: absolute;
    width: 0;
    height: 0;
    border-left: 10px solid transparent;
    border-right: 10px solid transparent;
    border-bottom: 120px solid red;
    top: 20px;
    left: 50%;
    transform-origin: center 100%;
    transform: translateX(-50%) rotate(0deg);
  }
</style>
</head>

<body>

<h2>🧭 Boussole GPS – Flèche</h2>

<div id="compass">
  <div id="needle"></div>
</div>

<p id="info">Initialisation GPS…</p>

<script>
// ===== DMS → DECIMAL =====
function dmsToDecimal(deg, min, sec, dir) {
    let val = deg + min / 60 + sec / 3600;
    if (dir === "S" || dir === "W") val *= -1;
    return val;
}

// 🎯 Coordonnées cibles (DMS)
const targetLat = dmsToDecimal(45, 42, 59.44, "N");
const targetLon = dmsToDecimal(4, 53, 41.16, "E");

// ===== CALCUL DU CAP =====
function toRad(deg) {
    return deg * Math.PI / 180;
}

function getBearing(lat1, lon1, lat2, lon2) {
    const dLon = toRad(lon2 - lon1);
    lat1 = toRad(lat1);
    lat2 = toRad(lat2);

    const y = Math.sin(dLon) * Math.cos(lat2);
    const x = Math.cos(lat1) * Math.sin(lat2) -
              Math.sin(lat1) * Math.cos(lat2) * Math.cos(dLon);

    return (Math.atan2(y, x) * 180 / Math.PI + 360) % 360;
}

// ===== GPS UTILISATEUR =====
navigator.geolocation.watchPosition(
    pos => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;

        const bearing = getBearing(lat, lon, targetLat, targetLon);

        document.getElementById("needle").style.transform =
            `translateX(-50%) rotate(${bearing}deg)`;

        document.getElementById("info").innerHTML =
            `📍 Position actuelle<br>
             Lat ${lat.toFixed(5)} | Lon ${lon.toFixed(5)}<br>
             🎯 Cap à suivre : ${bearing.toFixed(1)}°`;
    },
    () => alert("GPS indisponible"),
    { enableHighAccuracy: true }
);
</script>

</body>
</html>
