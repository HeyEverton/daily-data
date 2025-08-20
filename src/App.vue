<template>
  <div class="app">
    <header class="header">
      <h1 class="title">Acompanhe as principais tendências em tempo real </h1>
    </header>

    <main class="main">
      <div class="trends-grid">
        <button 
          v-for="trend in trends" 
          :key="trend.id"
          @click="fetchTrendData(trend)"
          class="trend-button"
          :style="{ backgroundColor: trend.color }"
        >
          <span class="trend-icon">{{ trend.icon }}</span>
          <span class="trend-name">{{ trend.name }}</span>
        </button>
      </div>
    </main>

    <!-- Modal -->
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <h2>{{ selectedTrend?.name }}</h2>
          <button @click="closeModal" class="close-btn">&times;</button>
        </div>
        
        <div class="modal-content">
          <div v-if="loading" class="loading">
            <div class="spinner"></div>
            <p>Carregando dados...</p>
          </div>
          
          <div v-else-if="trendData" class="data-content">
            <div v-for="(item, index) in trendData" :key="index" class="data-item">
              <h3>{{ item.title }}</h3>
              <p>{{ item.description }}</p>
              <div v-if="item.value" class="data-value">{{ item.value }}</div>
            </div>
          </div>
        </div>

        <div class="modal-actions">
          <button @click="copyData" class="action-btn copy-btn">
            📋 Copiar
          </button>
          <button @click="shareWhatsApp" class="action-btn whatsapp-btn">
            📱 WhatsApp
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

const showModal = ref(false)
const loading = ref(false)
const selectedTrend = ref(null)
const trendData = ref(null)

const trends = reactive([
  {
    id: 1,
    name: 'Economia',
    icon: '💰',
    color: '#10B981',
    apiUrl: 'https://api.hgbrasil.com/finance'
  },
  {
    id: 2,
    name: 'Saúde',
    icon: '🏥',
    color: '#EF4444',
    apiUrl: 'https://covid19-brazil-api.now.sh/api/report/v1'
  },
  {
    id: 3,
    name: 'Esportes',
    icon: '⚽',
    color: '#3B82F6',
    apiUrl: 'https://api.cartola.globo.com/atletas/mercado'
  },
  {
    id: 4,
    name: 'Moda',
    icon: '👗',
    color: '#8B5CF6',
    apiUrl: 'https://fakestoreapi.com/products/category/clothing'
  },
  {
    id: 5,
    name: 'Preços',
    icon: '🛒',
    color: '#F59E0B',
    apiUrl: 'https://api.mercadolibre.com/sites/MLB/search?q=trending'
  }
])

const fetchTrendData = async (trend) => {
  selectedTrend.value = trend
  showModal.value = true
  loading.value = true
  
  try {
    // Simulando dados para demonstração
    await new Promise(resolve => setTimeout(resolve, 1500))
    
    // Mock data baseado na categoria
    const mockData = generateMockData(trend.name)
    trendData.value = mockData
    
  } catch (error) {
    console.error('Erro ao buscar dados:', error)
    trendData.value = [{ title: 'Erro', description: 'Não foi possível carregar os dados' }]
  } finally {
    loading.value = false
  }
}

const generateMockData = (category) => {
  const data = {
    'Economia': [
      { title: 'Dólar', description: 'Cotação atual', value: 'R$ 5,12' },
      { title: 'Euro', description: 'Cotação atual', value: 'R$ 5,45' },
      { title: 'Ibovespa', description: 'Índice da bolsa', value: '118.234 pts' }
    ],
    'Saúde': [
      { title: 'Casos COVID-19', description: 'Últimas 24h', value: '1.234 novos casos' },
      { title: 'Vacinação', description: 'Doses aplicadas hoje', value: '45.678 doses' }
    ],
    'Esportes': [
      { title: 'Brasileirão', description: 'Líder da tabela', value: 'Palmeiras - 67 pts' },
      { title: 'Rebaixamento', description: 'Zona de perigo', value: 'Coritiba, América-MG' }
    ],
    'Moda': [
      { title: 'Tendência', description: 'Cor do ano', value: 'Verde Esmeralda' },
      { title: 'Estilo', description: 'Mais procurado', value: 'Minimalista' }
    ],
    'Preços': [
      { title: 'Gasolina', description: 'Preço médio', value: 'R$ 5,89/L' },
      { title: 'Arroz', description: 'Kg no varejo', value: 'R$ 6,50' }
    ]
  }
  
  return data[category] || []
}

const closeModal = () => {
  showModal.value = false
  selectedTrend.value = null
  trendData.value = null
}

const copyData = () => {
  if (!trendData.value) return
  
  let text = `📊 ${selectedTrend.value.name}\n\n`
  trendData.value.forEach(item => {
    text += `${item.title}: ${item.description}`
    if (item.value) text += ` - ${item.value}`
    text += '\n'
  })
  
  navigator.clipboard.writeText(text).then(() => {
    alert('Dados copiados!')
  })
}

const shareWhatsApp = () => {
  if (!trendData.value) return
  
  let text = `📊 *${selectedTrend.value.name}*\n\n`
  trendData.value.forEach(item => {
    text += `*${item.title}:* ${item.description}`
    if (item.value) text += ` - ${item.value}`
    text += '\n'
  })
  
  const whatsappUrl = `https://wa.me/?text=${encodeURIComponent(text)}`
  window.open(whatsappUrl, '_blank')
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  min-height: 100vh;
  background: linear-gradient(135deg, #ededed 0%, #ffffff 100%);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.header {
  text-align: center;
  padding: 2rem 1rem;
  color: white;
}

.title {
  font-size: 2.5rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
  color: #7c7a7a
}

.subtitle {
  font-size: 1.1rem;
  opacity: 0.9;
}

.main {
  padding: 0 1rem 2rem;
}

.trends-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
  max-width: 1200px;
  margin: 0 auto;
}

.trend-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
  padding: 2rem 1rem;
  border: none;
  border-radius: 16px;
  color: white;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.trend-button:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

.trend-icon {
  font-size: 2.5rem;
}

.trend-name {
  font-size: 1.1rem;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 1rem;
}

.modal {
  background: white;
  border-radius: 16px;
  width: 100%;
  max-width: 500px;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid #e5e7eb;
}

.modal-header h2 {
  font-size: 1.5rem;
  color: #1f2937;
}

.close-btn {
  background: none;
  border: none;
  font-size: 2rem;
  cursor: pointer;
  color: #6b7280;
  padding: 0;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.close-btn:hover {
  color: #374151;
}

.modal-content {
  padding: 1.5rem;
  min-height: 200px;
}

.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  padding: 2rem;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #e5e7eb;
  border-top: 4px solid #3b82f6;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.data-content {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.data-item {
  padding: 1rem;
  background: #f9fafb;
  border-radius: 8px;
  border-left: 4px solid #3b82f6;
}

.data-item h3 {
  font-size: 1.1rem;
  color: #1f2937;
  margin-bottom: 0.5rem;
}

.data-item p {
  color: #6b7280;
  margin-bottom: 0.5rem;
}

.data-value {
  font-size: 1.2rem;
  font-weight: bold;
  color: #059669;
}

.modal-actions {
  display: flex;
  gap: 1rem;
  padding: 1.5rem;
  border-top: 1px solid #e5e7eb;
}

.action-btn {
  flex: 1;
  padding: 0.75rem 1rem;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.copy-btn {
  background: #6b7280;
  color: white;
}

.copy-btn:hover {
  background: #4b5563;
}

.whatsapp-btn {
  background: #25d366;
  color: white;
}

.whatsapp-btn:hover {
  background: #128c7e;
}

@media (max-width: 768px) {
  .title {
    font-size: 2rem;
  }
  
  .trends-grid {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 1rem;
  }
  
  .trend-button {
    padding: 1.5rem 0.75rem;
  }
  
  .modal-actions {
    flex-direction: column;
  }
}
</style>