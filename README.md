<html><head><base href="">
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Investigação IP</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@400;700&display=swap');

body {
  font-family: 'Roboto Mono', monospace;
  background-color: #0a0f18;
  color: #00ff00;
  margin: 0;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-image: 
    radial-gradient(circle at 10% 20%, rgba(0, 255, 0, 0.05) 0%, transparent 20%),
    radial-gradient(circle at 90% 80%, rgba(0, 255, 0, 0.05) 0%, transparent 20%);
}

.panel {
  background-color: rgba(16, 26, 36, 0.95);
  border: 2px solid #00ff00;
  border-radius: 15px;
  box-shadow: 0 0 30px rgba(0, 255, 0, 0.2);
  padding: 30px;
  width: 95%;
  max-width: 1000px;
  position: relative;
  overflow: hidden;
}

.panel::before {
  content: "";
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 20px,
    rgba(0, 255, 0, 0.03) 20px,
    rgba(0, 255, 0, 0.03) 40px
  );
  animation: scan 10s linear infinite;
  pointer-events: none;
}

@keyframes scan {
  0% { transform: translateY(0); }
  100% { transform: translateY(50%); }
}

.police-badge {
  width: 100px;
  height: 100px;
  margin: 0 auto 20px;
  display: block;
}

h1 {
  text-align: center;
  color: #00ff00;
  text-transform: uppercase;
  letter-spacing: 3px;
  margin-bottom: 30px;
  font-size: 28px;
  text-shadow: 0 0 10px rgba(0, 255, 0, 0.5);
}

.input-container {
  position: relative;
  margin-bottom: 30px;
}

input[type="text"] {
  width: 100%;
  padding: 15px;
  background: rgba(0, 255, 0, 0.1);
  border: 2px solid #00ff00;
  color: #00ff00;
  font-family: 'Roboto Mono', monospace;
  font-size: 16px;
  transition: all 0.3s ease;
}

input[type="text"]:focus {
  outline: none;
  box-shadow: 0 0 15px rgba(0, 255, 0, 0.3);
}

.search-button {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  background: #00ff00;
  border: none;
  padding: 10px 20px;
  color: #0a0f18;
  cursor: pointer;
  transition: all 0.3s ease;
}

.search-button:hover {
  background: #0a0f18;
  color: #00ff00;
  box-shadow: 0 0 10px #00ff00;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 30px;
}

.info-card {
  background: rgba(0, 255, 0, 0.1);
  border: 1px solid #00ff00;
  padding: 20px;
  border-radius: 10px;
  transition: all 0.3s ease;
}

.info-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 255, 0, 0.2);
}

.info-label {
  font-weight: bold;
  color: #00ff00;
  margin-bottom: 10px;
  display: block;
}

.info-value {
  color: #fff;
}

#log {
  margin-top: 30px;
  padding: 20px;
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid #00ff00;
  border-radius: 10px;
  height: 150px;
  overflow-y: auto;
}

.log-entry {
  color: #00ff00;
  margin-bottom: 5px;
  font-size: 14px;
  opacity: 0.8;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

.alert {
  animation: pulse 2s infinite;
}

</style>
</head>
<body>

<div class="panel">
  <svg class="police-badge" viewBox="0 0 100 100">
    <path d="M50 5 L90 30 L90 70 L50 95 L10 70 L10 30 Z" fill="none" stroke="#00ff00" stroke-width="2"/>
    <text x="50" y="55" fill="#00ff00" text-anchor="middle" font-size="14">M</text>
    <text x="50" y="70" fill="#00ff00" text-anchor="middle" font-size="14">PRO</text>
  </svg>
  
  <h1>Sistema de Investigação IP</h1>
  
  <div class="input-container">
    <input type="text" id="ip-input" placeholder="Digite o endereço IP para investigação...">
    <button class="search-button" onclick="investigateIP()">INVESTIGAR</button>
  </div>

  <div class="info-grid">
    <div class="info-card">
      <span class="info-label">IP Investigado:</span>
      <span id="ip-address" class="info-value">-</span>
    </div>
    <div class="info-card">
      <span class="info-label">Localização:</span>
      <span id="location" class="info-value">-</span>
    </div>
    <div class="info-card">
      <span class="info-label">Provedor (ISP):</span>
      <span id="isp" class="info-value">-</span>
    </div>
    <div class="info-card">
      <span class="info-label">País:</span>
      <span id="country" class="info-value">-</span>
    </div>
    <div class="info-card">
      <span class="info-label">Região:</span>
      <span id="region" class="info-value">-</span>
    </div>
    <div class="info-card">
      <span class="info-label">Cidade:</span>
      <span id="city" class="info-value">-</span>
    </div>
  </div>

  <div id="log"></div>
</div>

<script>
async function investigateIP() {
  const ipInput = document.getElementById('ip-input').value.trim();
  
  if (!ipInput) {
    addLogEntry('⚠️ ALERTA: Digite um endereço IP para investigar', true);
    return;
  }

  addLogEntry(`🔍 Iniciando investigação do IP: ${ipInput}`);
  
  try {
    const response = await fetch(`https://ipapi.co/${ipInput}/json/`);
    const data = await response.json();
    
    if (data.error) {
      throw new Error(data.reason || 'Erro na investigação');
    }

    const elements = {
      'ip-address': data.ip,
      'location': `${data.city}, ${data.region}`,
      'isp': data.org,
      'country': data.country_name,
      'region': data.region,
      'city': data.city
    };

    Object.entries(elements).forEach(([id, value]) => {
      const element = document.getElementById(id);
      element.textContent = value;
      element.classList.add('alert');
      setTimeout(() => element.classList.remove('alert'), 2000);
    });

    addLogEntry(`✅ Investigação concluída para IP: ${data.ip}`);
    addLogEntry(`📍 Localização identificada: ${data.city}, ${data.country_name}`);
    addLogEntry(`🌐 Provedor detectado: ${data.org}`);

  } catch (error) {
    addLogEntry(`❌ ERRO NA INVESTIGAÇÃO: ${error.message}`, true);
  }
}

function addLogEntry(message, isAlert = false) {
  const log = document.getElementById('log');
  const entry = document.createElement('div');
  entry.className = `log-entry${isAlert ? ' alert' : ''}`;
  entry.textContent = `[${new Date().toLocaleTimeString()}] ${message}`;
  log.insertBefore(entry, log.firstChild);
}

// Adicionar evento de tecla Enter
document.getElementById('ip-input').addEventListener('keypress', function(e) {
  if (e.key === 'Enter') {
    investigateIP();
  }
});

// Mensagem inicial
addLogEntry('🚨 Sistema de Investigação IP inicializado e pronto para operação');
</script>

</body>
</html>
