<script setup>
import { ref, onMounted } from 'vue'

// 'ref' para armazenar o código da URL
const authCode = ref('')

// 'ref' para controlar o estado do botão de copiar
const copyButtonText = ref('Copiar')

// 'ref' para o próprio elemento <input>, para podermos selecioná-lo
const codeInputRef = ref(null)

// Função para copiar o código
const copyToClipboard = () => {
  // Se não houver código ou o input não estiver pronto, não faz nada
  if (!authCode.value || !codeInputRef.value) return

  // Usa a API do Clipboard
  navigator.clipboard.writeText(authCode.value).then(() => {
    // Sucesso
    copyButtonText.value = 'Copiado!'

    // Reseta o botão após 2 segundos
    setTimeout(() => {
      copyButtonText.value = 'Copiar'
    }, 2000)
  }).catch(err => {
    // Erro
    console.error('Falha ao copiar: ', err)
    copyButtonText.value = 'Erro!'
  })
}

// 'onMounted' é executado assim que o componente é carregado na página
onMounted(() => {
  // 1. Pega os parâmetros da URL
  const urlParams = new URLSearchParams(window.location.search)
  
  // 2. Procura pelo parâmetro 'code'
  const codeFromUrl = urlParams.get('code')

  // 3. Atualiza o 'ref' com o valor encontrado
  if (codeFromUrl) {
    authCode.value = codeFromUrl
  } else {
    authCode.value = "ERRO: Parâmetro 'code' não encontrado."
  }
})
</script>

<template>
  <div class="flex items-center justify-center min-h-screen bg-gray-100">
    
    <div class="w-full max-w-2xl p-8 bg-white rounded-lg shadow-md">
      
      <h1 class="mb-4 text-3xl font-bold text-center text-gray-800">
        Código de Autorização Capturado
      </h1>
      
      <p class="mb-6 text-center text-gray-600">
        O código da URL foi detectado. Clique no botão para copiá-lo.
      </p>

      <div class="flex">
        
        <input
          ref="codeInputRef"
          type="text"
          :value="authCode"
          readonly
          class="flex-grow p-3 overflow-x-auto font-mono text-sm text-gray-700 bg-gray-200 border border-r-0 border-gray-300 rounded-l-md focus:outline-none"
          placeholder="Aguardando código da URL..."
        />

        <button
          @click="copyToClipboard"
          :class="[
            'px-5 py-3 font-semibold text-white transition-colors duration-200 rounded-r-md focus:outline-none',
            copyButtonText === 'Copiado!' 
              ? 'bg-green-600 hover:bg-green-700' 
              : 'bg-blue-600 hover:bg-blue-700'
          ]"
        >
          {{ copyButtonText }}
        </button>

      </div>
    </div>
  </div>
</template>