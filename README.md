<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Radar Social RD - Plataforma de Seguridad Vial</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        body, html {
            height: 100%;
            background-color: #0f172a;
            color: #f8fafc;
            overflow: hidden;
        }
        #map {
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        /* Panel Flotante de Información */
        .info-panel {
            position: absolute;
            top: 20px;
            left: 20px;
            width: 340px;
            background: rgba(15, 23, 42, 0.9);
            backdrop-filter: blur(10px);
            padding: 20px;
            border-radius: 16px;
            z-index: 1000;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(51, 65, 85, 0.5);
        }
        .brand-title {
            font-size: 22px;
            font-weight: 800;
            color: #38bdf8;
            margin-bottom: 4px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .brand-subtitle {
            font-size: 12px;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 15px;
        }
        .stats-container {
            background: rgba(30, 41, 59, 0.5);
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 15px;
            border-left: 4px solid #ef4444;
        }
        .stats-title {
            font-size: 11px;
            color: #94a3b8;
            text-transform: uppercase;
        }
        .stats-number {
            font-size: 18px;
            font-weight: 700;
            color: #ef4444;
        }
        .report-list {
            max-height: 200px;
            overflow-y: auto;
            margin-top: 10px;
        }
        .report-item {
            padding: 10px 0;
            border-bottom: 1px solid rgba(51, 65, 85, 0.3);
            font-size: 13px;
        }
        .report-item:last-child {
            border-bottom: none;
        }
        .tag {
            display: inline-block;
            padding: 2px 6px;
            border-radius: 4px;
            font-size: 10px;
            font-weight: 700;
            margin-right: 5px;
        }
        .tag-danger { background-color: rgba(239, 68, 68, 0.2); color: #f87171; }
        .tag-warning { background-color: rgba(245, 158, 11, 0.2); color: #fbbf24; }
        
        /* Ajuste para móviles */
        @media (max-width: 480px) {
            .info-panel {
                width: calc(100% - 40px);
                bottom: 20px;
                top: auto;
                max-height: 250px;
                overflow-y: auto;
            }
            .report-list { max-height: 100px; }
        }
    </style>
</head>
<body>

    <div class="info-panel">
        <div class="brand-title">📍 Radar Social RD</div>
        <div class="brand-subtitle">Propuesta - Premio Seguridad Vial 2026</div>
        
        <div class="stats-container">
            <div class="stats-title">Puntos Críticos Detectados en Sto. Dgo.</div>
            <div class="stats-number">3 Reportes Activos de Alto Riesgo</div>
        </div>

        <p style="font-size: 13px; color: #cbd5e1; line-height: 1.4;">
            Plataforma colaborativa e interactiva en tiempo real para la prevención de accidentes viales mediante el mapeo comunitario.
        </p>

        <div class="report-list">
            <div class="report-item">
                <span class="tag tag-danger">ALTO RIESGO</span><strong>Av. 27 de Febrero</strong><br>
                <span style="color:#94a3b8; font-size:12px;">Zona de giros prohibidos y alta velocidad.</span>
            </div>
            <div class="report-item">
                <span class="tag tag-danger">ALTO RIESGO</span><strong>Autopista Duarte (Km 9)</strong><br>
                <span style="color:#94a3b8; font-size:12px;">Cruce peatonal de alto peligro y congestión extrema.</span>
            </div>
            <div class="report-item">
                <span class="tag tag-warning">ADVERTENCIA</span><strong>Av. John F. Kennedy</strong><br>
                <span style="color:#94a3b8; font-size:12px;">Incorporación peligrosa cerca de la estación del metro.</span>
            </div>
        </div>
    </div>

    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    <script>
        // Inicializar el mapa centrado en Santo Domingo, República Dominicana
        var map = L.map('map', {
            center: [18.4861, -69.9312],
            zoom: 13,
            zoomControl: false
        });

        // Añadir control de zoom en una posición más limpia
        L.control.zoom({ position: 'bottomright' }).addTo(map);

        // Capa de mapa estilo oscuro (CartoDB Dark Matter) para un look moderno e impactante
        L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
            subdomains: 'abcd',
            maxZoom: 20
        }).addTo(map);

        // Marcador 1: Av. 27 de Febrero con Máximo Gómez
        var marker1 = L.marker([18.4795, -69.9248]).addTo(map);
        marker1.bindPopup("<b>🚨 Punto Crítico: Av. 27 de Febrero</b><br>Incidencia alta de colisiones por exceso de velocidad y cambios bruscos de carril.").openPopup();

        // Marcador 2: Kilómetro 9 de la Autopista Duarte
        var marker2 = L.marker([18.4984, -69.9622]).addTo(map);
        marker2.bindPopup("<b>⚠️ Peligro Vial: Km 9 Aut. Duarte</b><br>Punto crítico de atropellamientos. Flujo masivo de peatones y transporte público.");

        // Marcador 3: Av. John F. Kennedy con Winston Churchill
        var marker3 = L.marker([18.4818, -69.9419]).addTo(map);
        marker3.bindPopup("<b>🚧 Alerta: Av. J.F. Kennedy / Churchill</b><br>Congestión severa en horas pico e incorporaciones de riesgo elevado.");
    </script>
</body>
</html>
