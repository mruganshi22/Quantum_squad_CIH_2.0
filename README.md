<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Smart Ambulance Dashboard</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Leaflet CSS & JS -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <!-- Leaflet Routing Machine -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet-routing-machine/dist/leaflet-routing-machine.css" />
  <script src="https://unpkg.com/leaflet-routing-machine/dist/leaflet-routing-machine.min.js"></script>

  <!-- Font Awesome for icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');
    
    :root {
      --primary-green: #10b981;
      --secondary-green: #059669;
      --light-green: #d1fae5;
      --dark-green: #047857;
      --accent-green: #34d399;
      --emerald-50: #ecfdf5;
      --emerald-100: #d1fae5;
      --emerald-500: #10b981;
      --emerald-600: #059669;
      --emerald-700: #047857;
      --emerald-800: #065f46;
    }
    
    body {
      font-family: 'Inter', sans-serif;
      background: linear-gradient(135deg, #ecfdf5 0%, #d1fae5 50%, #a7f3d0 100%);
    }
    
    .emergency-pulse {
      animation: pulse-red 2s infinite;
      box-shadow: 0 0 30px rgba(239, 68, 68, 0.3);
    }
    
    @keyframes pulse-red {
      0%, 100% {
        box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7), 0 0 30px rgba(239, 68, 68, 0.3);
        transform: scale(1);
      }
      50% {
        box-shadow: 0 0 0 20px rgba(239, 68, 68, 0), 0 0 40px rgba(239, 68, 68, 0.5);
        transform: scale(1.02);
      }
    }
    
    .status-online {
      animation: pulse-green 2s infinite;
    }
    
    @keyframes pulse-green {
      0%, 100% {
        opacity: 1;
        transform: scale(1);
      }
      50% {
        opacity: 0.7;
        transform: scale(1.1);
      }
    }
    
    .glass-effect {
      backdrop-filter: blur(20px);
      background: rgba(255, 255, 255, 0.15);
      border: 1px solid rgba(255, 255, 255, 0.3);
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
    }
    
    .sidebar-toggle {
      transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
    }
    
    .sidebar-hidden {
      transform: translateX(-100%);
    }

    .gradient-green {
      background: linear-gradient(135deg, var(--primary-green) 0%, var(--secondary-green) 100%);
    }

    .gradient-green-light {
      background: linear-gradient(135deg, var(--light-green) 0%, var(--emerald-100) 100%);
    }

    .card-hover {
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .card-hover:hover {
      transform: translateY(-4px);
      box-shadow: 0 20px 40px rgba(16, 185, 129, 0.15);
    }

    .btn-green {
      background: linear-gradient(135deg, var(--primary-green) 0%, var(--secondary-green) 100%);
      transition: all 0.3s ease;
      box-shadow: 0 4px 15px rgba(16, 185, 129, 0.3);
    }

    .btn-green:hover {
      transform: translateY(-2px);
      box-shadow: 0 8px 25px rgba(16, 185, 129, 0.4);
    }

    .floating-animation {
      animation: float 3s ease-in-out infinite;
    }

    @keyframes float {
      0%, 100% { transform: translateY(0px); }
      50% { transform: translateY(-10px); }
    }

    .glow-green {
      box-shadow: 0 0 20px rgba(16, 185, 129, 0.3);
    }

    .nav-gradient {
      background: linear-gradient(90deg, rgba(255,255,255,0.95) 0%, rgba(236,253,245,0.95) 100%);
      backdrop-filter: blur(10px);
    }

    .sidebar-gradient {
      background: linear-gradient(180deg, rgba(255,255,255,0.98) 0%, rgba(236,253,245,0.95) 100%);
      backdrop-filter: blur(15px);
    }

    .quick-action-card {
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
      background: linear-gradient(135deg, rgba(255,255,255,0.9) 0%, rgba(236,253,245,0.8) 100%);
      backdrop-filter: blur(10px);
      border: 1px solid rgba(16, 185, 129, 0.1);
    }

    .quick-action-card:hover {
      transform: translateY(-3px) scale(1.02);
      box-shadow: 0 10px 30px rgba(16, 185, 129, 0.2);
      border-color: rgba(16, 185, 129, 0.3);
    }

    .map-controls {
      background: rgba(255, 255, 255, 0.95);
      backdrop-filter: blur(10px);
      border: 1px solid rgba(16, 185, 129, 0.2);
    }

    .route-info-panel {
      background: linear-gradient(135deg, rgba(255,255,255,0.95) 0%, rgba(236,253,245,0.9) 100%);
      backdrop-filter: blur(20px);
      border: 1px solid rgba(16, 185, 129, 0.2);
    }

    .toggle-switch {
      background: linear-gradient(135deg, var(--primary-green) 0%, var(--secondary-green) 100%);
    }

    .contact-card {
      background: linear-gradient(135deg, rgba(255,255,255,0.9) 0%, rgba(236,253,245,0.7) 100%);
      border: 1px solid rgba(16, 185, 129, 0.15);
      transition: all 0.3s ease;
    }

    .contact-card:hover {
      border-color: rgba(16, 185, 129, 0.3);
      transform: translateX(5px);
    }

    .success-modal {
      background: linear-gradient(135deg, rgba(255,255,255,0.98) 0%, rgba(236,253,245,0.95) 100%);
      backdrop-filter: blur(20px);
      border: 1px solid rgba(16, 185, 129, 0.2);
    }

    .loading-spinner {
      animation: spin 1s linear infinite;
    }

    @keyframes spin {
      from { transform: rotate(0deg); }
      to { transform: rotate(360deg); }
    }

    .text-shadow {
      text-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    .icon-glow {
      filter: drop-shadow(0 0 8px rgba(16, 185, 129, 0.3));
    }
  </style>
</head>

<body class="min-h-screen">
  <!-- Navigation Bar -->
  <nav class="nav-gradient shadow-xl border-b border-emerald-200/50 sticky top-0 z-40">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between items-center h-18">
        <!-- Logo and Brand -->
        <div class="flex items-center space-x-4">
          <button id="sidebarToggle" class="lg:hidden text-emerald-600 hover:text-emerald-800 transition-colors">
            <i class="fas fa-bars text-xl"></i>
          </button>
          <div class="flex items-center space-x-3">
            <div class="gradient-green p-3 rounded-xl shadow-lg floating-animation">
              <i class="fas fa-ambulance text-white text-2xl icon-glow"></i>
            </div>
            <div>
              <h1 class="text-2xl font-bold text-emerald-800 text-shadow">Smart Ambulance</h1>
              <p class="text-sm text-emerald-600 font-medium">Emergency Response System</p>
            </div>
          </div>
        </div>

        <!-- Status Indicators -->
        <div class="hidden md:flex items-center space-x-8">
          <div class="flex items-center space-x-3 bg-emerald-50 px-4 py-2 rounded-full">
            <div class="w-3 h-3 bg-emerald-500 rounded-full status-online"></div>
            <span class="text-sm font-medium text-emerald-700">System Online</span>
          </div>
          <div class="flex items-center space-x-3 bg-emerald-50 px-4 py-2 rounded-full">
            <i class="fas fa-signal text-emerald-500 icon-glow"></i>
            <span class="text-sm font-medium text-emerald-700">GPS Active</span>
          </div>
          <div class="flex items-center space-x-3 bg-emerald-50 px-4 py-2 rounded-full">
            <i class="fas fa-wifi text-emerald-500 icon-glow"></i>
            <span class="text-sm font-medium text-emerald-700">Connected</span>
          </div>
        </div>

        <!-- User Menu -->
        <div class="flex items-center space-x-4">
          <button class="relative text-emerald-600 hover:text-emerald-800 transition-colors p-2 rounded-lg hover:bg-emerald-50">
            <i class="fas fa-bell text-xl"></i>
            <span class="absolute -top-1 -right-1 bg-red-500 text-white text-xs rounded-full h-5 w-5 flex items-center justify-center animate-pulse">3</span>
          </button>
          <div class="flex items-center space-x-3 bg-emerald-50 px-4 py-2 rounded-full">
            <img src="https://ui-avatars.com/api/?name=Operator&background=10b981&color=fff" 
                 alt="User" class="w-10 h-10 rounded-full border-2 border-emerald-200">
            <span class="hidden md:block text-sm font-semibold text-emerald-800">Dr. Sarah Johnson</span>
          </div>
        </div>
      </div>
    </div>
  </nav>

  <div class="flex h-screen pt-18">
    <!-- Sidebar -->
    <div id="sidebar" class="sidebar-toggle w-80 sidebar-gradient shadow-2xl border-r border-emerald-200/50 lg:translate-x-0">
      <div class="p-6 h-full overflow-y-auto">
        <!-- User Profile Card -->
        <div class="mb-8 gradient-green rounded-2xl p-6 text-white shadow-xl card-hover">
          <div class="flex items-center space-x-4 mb-4">
            <img src="https://ui-avatars.com/api/?name=Dr+Sarah+Johnson&background=ffffff&color=10b981" 
                 alt="User" class="w-16 h-16 rounded-full border-3 border-white shadow-lg">
            <div>
              <h3 class="text-lg font-bold">Dr. Sarah Johnson</h3>
              <p class="text-emerald-100 text-sm">Emergency Medical Technician</p>
              <p class="text-emerald-200 text-xs">ID: EMP001</p>
            </div>
          </div>
          <div class="grid grid-cols-2 gap-4 text-center">
            <div class="bg-white/20 rounded-lg p-3">
              <div class="text-2xl font-bold">8h 23m</div>
              <div class="text-xs text-emerald-100">On Duty</div>
            </div>
            <div class="bg-white/20 rounded-lg p-3">
              <div class="text-2xl font-bold">12</div>
              <div class="text-xs text-emerald-100">Calls Today</div>
            </div>
          </div>
        </div>

        <!-- Emergency Section -->
        <div class="mb-8">
          <h2 class="text-lg font-bold text-emerald-800 mb-4 flex items-center">
            <i class="fas fa-exclamation-triangle text-red-500 mr-2"></i>
            Emergency Control
          </h2>
          
          <!-- Emergency Button -->
          <button id="emergencyBtn" 
                  class="w-full bg-gradient-to-r from-red-500 to-red-600 hover:from-red-600 hover:to-red-700 text-white font-bold py-8 px-6 rounded-2xl emergency-pulse transition-all duration-300 transform hover:scale-105 mb-4 shadow-2xl">
            <i class="fas fa-exclamation-triangle text-4xl mb-3"></i>
            <div class="text-2xl font-black">EMERGENCY</div>
            <div class="text-sm opacity-90 font-medium">Send Alert Signal</div>
          </button>

          <!-- Emergency Status -->
          <div id="emergencyStatus" class="hidden bg-yellow-50 border-2 border-yellow-200 rounded-xl p-4 mb-4">
            <div class="flex items-center">
              <i class="fas fa-spinner loading-spinner text-yellow-600 mr-3 text-lg"></i>
              <span class="text-yellow-800 font-semibold">Sending emergency signal...</span>
            </div>
          </div>
        </div>

        <!-- Location Input -->
        <div class="mb-8">
          <h3 class="text-lg font-bold text-emerald-800 mb-4 flex items-center">
            <i class="fas fa-route text-emerald-600 mr-2"></i>
            Route Planning
          </h3>
          <div class="space-y-4">
            <input id="userLocation" type="text" placeholder="Enter pickup location"
                   class="w-full px-4 py-4 border-2 border-emerald-200 rounded-xl focus:ring-4 focus:ring-emerald-200 focus:border-emerald-500 transition-all bg-white/80 backdrop-blur-sm">
            <button onclick="findRoute()" 
                    class="w-full btn-green text-white font-bold py-4 px-6 rounded-xl transition-all">
              <i class="fas fa-route mr-2"></i>Find Optimal Route
            </button>
          </div>
        </div>

        <!-- Quick Actions -->
        <div class="mb-8">
          <h3 class="text-lg font-bold text-emerald-800 mb-4 flex items-center">
            <i class="fas fa-bolt text-emerald-600 mr-2"></i>
            Quick Actions
          </h3>
          <div class="grid grid-cols-2 gap-4">
            <button class="quick-action-card p-4 rounded-xl text-emerald-700">
              <i class="fas fa-hospital text-2xl mb-2 icon-glow"></i>
              <div class="text-sm font-semibold">Hospitals</div>
            </button>
            <button class="quick-action-card p-4 rounded-xl text-emerald-700">
              <i class="fas fa-traffic-light text-2xl mb-2 icon-glow"></i>
              <div class="text-sm font-semibold">Traffic</div>
            </button>
            <button class="quick-action-card p-4 rounded-xl text-emerald-700">
              <i class="fas fa-phone text-2xl mb-2 icon-glow"></i>
              <div class="text-sm font-semibold">Contacts</div>
            </button>
            <button class="quick-action-card p-4 rounded-xl text-emerald-700">
              <i class="fas fa-history text-2xl mb-2 icon-glow"></i>
              <div class="text-sm font-semibold">History</div>
            </button>
          </div>
        </div>

        <!-- Settings Toggle -->
        <div class="mb-8">
          <h3 class="text-lg font-bold text-emerald-800 mb-4 flex items-center">
            <i class="fas fa-cog text-emerald-600 mr-2"></i>
            Settings
          </h3>
          <div class="space-y-4">
            <div class="flex items-center justify-between bg-white/60 p-4 rounded-xl backdrop-blur-sm border border-emerald-200">
              <span class="text-sm font-semibold text-emerald-800">Auto-routing</span>
              <label class="relative inline-flex items-center cursor-pointer">
                <input type="checkbox" class="sr-only peer" checked>
                <div class="w-12 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-emerald-300 rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all toggle-switch peer-checked:bg-emerald-500"></div>
              </label>
            </div>
            <div class="flex items-center justify-between bg-white/60 p-4 rounded-xl backdrop-blur-sm border border-emerald-200">
              <span class="text-sm font-semibold text-emerald-800">Sound Alerts</span>
              <label class="relative inline-flex items-center cursor-pointer">
                <input type="checkbox" class="sr-only peer" checked>
                <div class="w-12 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-emerald-300 rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all toggle-switch peer-checked:bg-emerald-500"></div>
              </label>
            </div>
            <div class="flex items-center justify-between bg-white/60 p-4 rounded-xl backdrop-blur-sm border border-emerald-200">
              <span class="text-sm font-semibold text-emerald-800">Dark Mode</span>
              <label class="relative inline-flex items-center cursor-pointer">
                <input id="darkModeToggle" type="checkbox" class="sr-only peer">
                <div class="w-12 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-emerald-300 rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all toggle-switch peer-checked:bg-emerald-500"></div>
              </label>
            </div>
          </div>
        </div>

        <!-- Emergency Contacts -->
        <div>
          <h3 class="text-lg font-bold text-emerald-800 mb-4 flex items-center">
            <i class="fas fa-address-book text-emerald-600 mr-2"></i>
            Emergency Contacts
          </h3>
          <div class="space-y-3">
            <div class="contact-card flex items-center justify-between p-4 rounded-xl">
              <div class="flex items-center space-x-3">
                <div class="bg-red-100 p-2 rounded-lg">
                  <i class="fas fa-hospital text-red-500"></i>
                </div>
                <div>
                  <span class="text-sm font-semibold text-emerald-800">Main Hospital</span>
                  <p class="text-xs text-emerald-600">Emergency Center</p>
                </div>
              </div>
              <a href="tel:+1234567890" class="bg-emerald-100 p-2 rounded-lg text-emerald-600 hover:bg-emerald-200 transition-colors">
                <i class="fas fa-phone"></i>
              </a>
            </div>
            <div class="contact-card flex items-center justify-between p-4 rounded-xl">
              <div class="flex items-center space-x-3">
                <div class="bg-blue-100 p-2 rounded-lg">
                  <i class="fas fa-shield-alt text-blue-500"></i>
                </div>
                <div>
                  <span class="text-sm font-semibold text-emerald-800">Police Department</span>
                  <p class="text-xs text-emerald-600">Emergency Line</p>
                </div>
              </div>
              <a href="tel:911" class="bg-emerald-100 p-2 rounded-lg text-emerald-600 hover:bg-emerald-200 transition-colors">
                <i class="fas fa-phone"></i>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Main Content -->
    <div class="flex-1 flex flex-col">
      <!-- Map Header -->
      <div class="nav-gradient shadow-lg border-b border-emerald-200/50 p-6">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-2xl font-bold text-emerald-800 text-shadow">Live Map & Navigation</h2>
            <p class="text-emerald-600 font-medium">Right-click to mark traffic points • Real-time route optimization</p>
          </div>
          <div class="flex items-center space-x-6">
            <div class="flex items-center space-x-3 bg-emerald-100 px-4 py-2 rounded-full glow-green">
              <div class="w-3 h-3 bg-emerald-500 rounded-full status-online"></div>
              <span class="text-sm font-bold text-emerald-700">GPS Active</span>
            </div>
            <button class="map-controls p-3 rounded-xl hover:bg-emerald-50 transition-colors">
              <i class="fas fa-expand text-emerald-600"></i>
            </button>
          </div>
        </div>
      </div>

      <!-- Map Container -->
      <div class="flex-1 relative">
        <div id="map" class="w-full h-full"></div>
        
        <!-- Floating Controls -->
        <div class="absolute top-6 right-6 space-y-3">
          <button class="map-controls p-4 rounded-xl hover:bg-emerald-50 transition-all transform hover:scale-105 shadow-lg">
            <i class="fas fa-crosshairs text-emerald-600 text-lg"></i>
          </button>
          <button class="map-controls p-4 rounded-xl hover:bg-emerald-50 transition-all transform hover:scale-105 shadow-lg">
            <i class="fas fa-layer-group text-emerald-600 text-lg"></i>
          </button>
          <button class="map-controls p-4 rounded-xl hover:bg-emerald-50 transition-all transform hover:scale-105 shadow-lg">
            <i class="fas fa-satellite text-emerald-600 text-lg"></i>
          </button>
        </div>

        <!-- Route Info Panel -->
        <div id="routeInfo" class="absolute bottom-6 left-6 right-6 route-info-panel shadow-2xl rounded-2xl p-6 hidden">
          <div class="flex items-center justify-between mb-4">
            <h3 class="text-xl font-bold text-emerald-800 flex items-center">
              <i class="fas fa-info-circle text-emerald-600 mr-2"></i>
              Route Information
            </h3>
            <button onclick="closeRouteInfo()" class="text-emerald-500 hover:text-emerald-700 p-2 rounded-lg hover:bg-emerald-50 transition-colors">
              <i class="fas fa-times text-lg"></i>
            </button>
          </div>
          <div id="routeDetails" class="grid grid-cols-3 gap-6 text-center">
            <div class="bg-emerald-50 p-4 rounded-xl">
              <div class="text-3xl font-bold text-emerald-600">--</div>
              <div class="text-sm text-emerald-700 font-semibold">Distance</div>
            </div>
            <div class="bg-emerald-50 p-4 rounded-xl">
              <div class="text-3xl font-bold text-emerald-600">--</div>
              <div class="text-sm text-emerald-700 font-semibold">ETA</div>
            </div>
            <div class="bg-emerald-50 p-4 rounded-xl">
              <div class="text-3xl font-bold text-emerald-600">--</div>
              <div class="text-sm text-emerald-700 font-semibold">Traffic</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Success Modal -->
  <div id="successModal" class="fixed inset-0 bg-black/50 backdrop-blur-sm hidden items-center justify-center z-50">
    <div class="success-modal rounded-2xl p-8 max-w-md mx-4 text-center shadow-2xl">
      <div class="w-20 h-20 bg-emerald-100 rounded-full flex items-center justify-center mx-auto mb-6 floating-animation">
        <i class="fas fa-check text-emerald-500 text-3xl"></i>
      </div>
      <h3 class="text-2xl font-bold text-emerald-800 mb-3">Emergency Signal Sent!</h3>
      <p class="text-emerald-600 mb-8 font-medium">Your emergency alert has been successfully transmitted to the response center.</p>
      <button onclick="closeSuccessModal()" class="btn-green text-white px-8 py-3 rounded-xl font-bold">
        <i class="fas fa-check mr-2"></i>OK
      </button>
    </div>
  </div>

  <script>
    // Global variables
    const hospitalCoords = [21.1458, 79.0882];
    let map = L.map('map').setView(hospitalCoords, 13);
    let routingControl;
    const trafficPoints = [];
    let userMarker;

    // Initialize map
    function initializeMap() {
      // Load base map with custom styling
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap contributors'
      }).addTo(map);

      // Mark hospital with custom green-themed icon
      const hospitalIcon = L.divIcon({
        html: '<div class="bg-emerald-500 p-2 rounded-full shadow-lg"><i class="fas fa-hospital text-white text-xl"></i></div>',
        iconSize: [40, 40],
        className: 'custom-div-icon'
      });
      
      L.marker(hospitalCoords, { icon: hospitalIcon })
        .addTo(map)
        .bindPopup("<div class='text-center p-2'><b class='text-emerald-800'>🏥 Main Hospital</b><br><span class='text-emerald-600'>Emergency Response Center</span></div>");

      // Handle right-click for traffic points
      map.on('contextmenu', function (e) {
        const { lat, lng } = e.latlng;
        trafficPoints.push([lat, lng]);

        L.circleMarker([lat, lng], {
          radius: 10,
          color: '#ef4444',
          fillColor: '#ef4444',
          fillOpacity: 0.8,
          weight: 3
        }).addTo(map)
          .bindPopup("<div class='text-center p-2'><b class='text-red-600'>🚦 Traffic Point</b><br><span class='text-gray-600'>Congestion detected</span></div>")
          .openPopup();
      });
    }

    // Emergency button functionality
    document.getElementById('emergencyBtn').addEventListener('click', async function() {
      const btn = this;
      const status = document.getElementById('emergencyStatus');
      
      // Show loading state
      btn.disabled = true;
      btn.innerHTML = '<i class="fas fa-spinner loading-spinner text-3xl mb-3"></i><div class="text-xl font-black">Sending...</div><div class="text-sm opacity-90">Please wait</div>';
      status.classList.remove('hidden');

      try {
        // Get current location
        const position = await getCurrentLocation();
        
        // Prepare emergency data
        const emergencyData = {
          timestamp: new Date().toISOString(),
          location: {
            latitude: position.coords.latitude,
            longitude: position.coords.longitude
          },
          status: 'EMERGENCY',
          ambulance_id: 'AMB-001',
          operator: 'Dr. Sarah Johnson',
          operator_id: 'EMP001',
          priority: 'HIGH'
        };

        // Send to Google Sheets API
        const response = await fetch('https://sheetdb.io/api/v1/s3l55gro9n30p', {
          method: 'POST',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(emergencyData)
        });

        if (response.ok) {
          // Success
          status.classList.add('hidden');
          showSuccessModal();
          
          // Add emergency marker to map
          const emergencyIcon = L.divIcon({
            html: '<div class="bg-red-500 p-2 rounded-full shadow-lg animate-pulse"><i class="fas fa-exclamation-triangle text-white text-lg"></i></div>',
            iconSize: [35, 35],

            className: 'custom-div-icon'
          });
          
          L.marker([position.coords.latitude, position.coords.longitude], { icon: emergencyIcon })
            .addTo(map)
            .bindPopup("<div class='text-center p-2'><b class='text-red-600'>🚨 Emergency Location</b><br><span class='text-emerald-600'>Signal sent successfully!</span></div>")
            .openPopup();
            
          map.setView([position.coords.latitude, position.coords.longitude], 15);
        } else {
          throw new Error('Failed to send emergency signal');
        }
      } catch (error) {
        console.error('Emergency signal error:', error);
        alert('Failed to send emergency signal. Please try again.');
        status.classList.add('hidden');
      } finally {
        // Reset button
        btn.disabled = false;
        btn.innerHTML = '<i class="fas fa-exclamation-triangle text-4xl mb-3"></i><div class="text-2xl font-black">EMERGENCY</div><div class="text-sm opacity-90 font-medium">Send Alert Signal</div>';
      }
    });

    // Get current location
    function getCurrentLocation() {
      return new Promise((resolve, reject) => {
        if (!navigator.geolocation) {
          reject(new Error('Geolocation is not supported'));
          return;
        }

        navigator.geolocation.getCurrentPosition(resolve, reject, {
          enableHighAccuracy: true,
          timeout: 10000,
          maximumAge: 60000
        });
      });
    }

    // Find route function
    async function findRoute() {
      const address = document.getElementById("userLocation").value.trim();

      if (!address) {
        alert("Please enter a location.");
        return;
      }

      try {
        // Convert location to lat/lng using Nominatim
        const response = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(address)}`);
        const data = await response.json();

        if (!data.length) {
          alert("Location not found.");
          return;
        }

        const userCoords = [parseFloat(data[0].lat), parseFloat(data[0].lon)];

        // Remove existing user marker
        if (userMarker) {
          map.removeLayer(userMarker);
        }

        // Add user marker with green theme
        const userIcon = L.divIcon({
          html: '<div class="bg-emerald-500 p-2 rounded-full shadow-lg"><i class="fas fa-map-marker-alt text-white text-lg"></i></div>',
          iconSize: [35, 35],
          className: 'custom-div-icon'
        });
        
        userMarker = L.marker(userCoords, { icon: userIcon })
          .addTo(map)
          .bindPopup("<div class='text-center p-2'><b class='text-emerald-800'>📍 Pickup Location</b><br><span class='text-emerald-600'>Route starting point</span></div>")
          .openPopup();

        // Remove existing route
        if (routingControl) {
          map.removeControl(routingControl);
        }

        // Create new route with green styling
        routingControl = L.Routing.control({
          waypoints: [
            L.latLng(userCoords),
            L.latLng(hospitalCoords)
          ],
          routeWhileDragging: false,
          showAlternatives: true,
          altLineOptions: {
            styles: [{ color: '#6b7280', opacity: 0.5, weight: 4 }]
          },
          lineOptions: {
            styles: [{ color: '#10b981', weight: 6, opacity: 0.8 }]
          }
        }).addTo(map);

        // Show route info panel
        document.getElementById('routeInfo').classList.remove('hidden');
        
        // Update route details (mock data for demo)
        document.getElementById('routeDetails').innerHTML = `
          <div class="bg-emerald-50 p-4 rounded-xl">
            <div class="text-3xl font-bold text-emerald-600">5.2km</div>
            <div class="text-sm text-emerald-700 font-semibold">Distance</div>
          </div>
          <div class="bg-emerald-50 p-4 rounded-xl">
            <div class="text-3xl font-bold text-emerald-600">12min</div>
            <div class="text-sm text-emerald-700 font-semibold">ETA</div>
          </div>
          <div class="bg-emerald-50 p-4 rounded-xl">
            <div class="text-3xl font-bold text-emerald-600">Light</div>
            <div class="text-sm text-emerald-700 font-semibold">Traffic</div>
          </div>
        `;

      } catch (error) {
        console.error('Route finding error:', error);
        alert('Error finding route. Please try again.');
      }
    }

    // Close route info
    function closeRouteInfo() {
      document.getElementById('routeInfo').classList.add('hidden');
    }

    // Success modal functions
    function showSuccessModal() {
      document.getElementById('successModal').classList.remove('hidden');
      document.getElementById('successModal').classList.add('flex');
    }

    function closeSuccessModal() {
      document.getElementById('successModal').classList.add('hidden');
      document.getElementById('successModal').classList.remove('flex');
    }

    // Sidebar toggle
    document.getElementById('sidebarToggle').addEventListener('click', function() {
      const sidebar = document.getElementById('sidebar');
      sidebar.classList.toggle('sidebar-hidden');
    });

    // Dark mode toggle
    document.getElementById('darkModeToggle').addEventListener('change', function() {
      if (this.checked) {
        document.body.classList.add('dark');
      } else {
        document.body.classList.remove('dark');
      }
    });

    // Initialize everything when page loads
    document.addEventListener('DOMContentLoaded', function() {
      initializeMap();
      
      // Auto-resize map when window resizes
      window.addEventListener('resize', function() {
        setTimeout(() => {
          map.invalidateSize();
        }, 100);
      });
    });

    // Handle Enter key in location input
    document.getElementById('userLocation').addEventListener('keypress', function(e) {
      if (e.key === 'Enter') {
        findRoute();
      }
    });
  </script>
</body>
</html># Quantum_squad_CIH_2.0
