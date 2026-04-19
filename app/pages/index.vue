<template>
  <div class="cyber-theme min-h-screen flex flex-col font-sans selection:bg-cyber-green selection:text-black">
    
    <header class="fixed top-0 w-full z-50 glass-panel border-b border-white/10">
        <div class="max-w-7xl mx-auto px-6 h-20 flex items-center justify-between">
            <a href="#" class="text-2xl font-mono font-bold tracking-tighter text-white glitch-hover flex items-center gap-2">
                <i class="ph-fill ph-terminal-window text-cyber-green"></i>
                yous.ee
            </a>
            
            <div class="relative group">
                <select v-model="currentLang" class="appearance-none bg-transparent font-mono text-sm border border-white/20 rounded px-4 py-2 pr-8 hover:border-cyber-green focus:outline-none focus:border-cyber-green transition-colors cursor-pointer text-white">
                    <option value="en" class="bg-cyber-dark text-white">EN - English</option>
                    <option value="zh-CN" class="bg-cyber-dark text-white">简 - 简体中文</option>
                    <option value="zh-TW" class="bg-cyber-dark text-white">繁 - 繁體中文</option>
                    <option value="ja" class="bg-cyber-dark text-white">日 - 日本語</option>
                    <option value="es" class="bg-cyber-dark text-white">ES - Español</option>
                    <option value="fr" class="bg-cyber-dark text-white">FR - Français</option>
                    <option value="de" class="bg-cyber-dark text-white">DE - Deutsch</option>
                </select>
                <i class="ph ph-caret-down absolute right-3 top-1/2 -translate-y-1/2 pointer-events-none text-white/50"></i>
            </div>
        </div>
    </header>

    <main class="flex-grow pt-32 pb-20 px-6 relative flex flex-col justify-center">
        <div class="absolute top-1/4 left-1/4 w-96 h-96 bg-cyber-green/5 rounded-full blur-[100px] pointer-events-none"></div>
        <div class="absolute bottom-1/4 right-1/4 w-96 h-96 bg-blue-500/5 rounded-full blur-[100px] pointer-events-none"></div>

        <div class="max-w-3xl mx-auto w-full relative z-10 text-center">
            
            <h1 class="text-5xl md:text-7xl font-bold tracking-tight mb-6" v-html="t['hero-title']"></h1>
            <p class="text-lg md:text-xl text-gray-400 font-mono mb-12 max-w-2xl mx-auto">{{ t['hero-subtitle'] }}</p>

            <div class="glass-panel p-2 md:p-4 rounded-xl flex flex-col md:flex-row gap-4 mb-8 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 left-0 w-full h-[1px] bg-gradient-to-r from-transparent via-cyber-green/50 to-transparent"></div>
                
                <div class="relative flex-grow flex items-center">
                    <i class="ph ph-link absolute left-4 text-gray-500 text-xl"></i>
                    <input 
                        v-model="inputUrl" 
                        @keypress.enter="handleShorten"
                        type="url" 
                        :class="['w-full bg-cyber-black text-white font-mono text-base md:text-lg rounded-lg pl-12 pr-4 py-4 border input-glow transition-all placeholder-gray-600', inputError ? 'border-red-500' : 'border-white/10']" 
                        :placeholder="t['input-placeholder']" 
                        required 
                        autocomplete="off"
                    >
                </div>
                
                <button 
                    @click="handleShorten" 
                    :disabled="isProcessing"
                    :class="['bg-white text-black font-mono font-bold px-8 py-4 rounded-lg transition-all flex items-center justify-center gap-2 group', isProcessing ? 'opacity-50 cursor-not-allowed' : 'hover:bg-cyber-green hover:text-black']"
                >
                    <span>{{ t['btn-shorten'] }}</span>
                    <i class="ph-bold ph-arrow-right group-hover:translate-x-1 transition-transform"></i>
                </button>
            </div>

            <div v-show="showResult" class="text-left bg-[#0a0a0a] border border-cyber-green/30 rounded-lg p-6 relative">
                <div class="absolute top-0 left-0 w-full h-8 bg-white/5 border-b border-cyber-green/20 rounded-t-lg flex items-center px-4 gap-2">
                    <div class="w-3 h-3 rounded-full bg-red-500/50"></div>
                    <div class="w-3 h-3 rounded-full bg-yellow-500/50"></div>
                    <div class="w-3 h-3 rounded-full bg-cyber-green"></div>
                    <span class="text-xs font-mono text-gray-500 ml-2">yous.ee_terminal_v1.0</span>
                </div>
                
                <div class="mt-8 font-mono">
                    <p class="text-gray-400 text-sm mb-2">{{ t['result-status'] }}</p>
                    <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 bg-black/50 p-4 rounded border border-white/5">
                        <a :href="shortenedUrl" class="text-cyber-green text-xl sm:text-2xl break-all hover:underline" target="_blank">{{ shortenedUrl }}</a>
                        <button 
                            @click="handleCopy" 
                            :class="['shrink-0 border px-4 py-2 rounded text-sm transition-colors flex items-center gap-2', isCopied ? 'bg-cyber-green text-black border-cyber-green' : 'border-cyber-green text-cyber-green hover:bg-cyber-green hover:text-black']"
                        >
                            <i :class="isCopied ? 'ph-bold ph-check' : 'ph ph-copy'"></i>
                            <span>{{ isCopied ? t['btn-copied'] : t['btn-copy'] }}</span>
                        </button>
                    </div>
                </div>
            </div>

            <div v-show="isProcessing" class="mt-8 font-mono text-cyber-green flex items-center justify-center gap-2">
                <i class="ph ph-circle-notch animate-spin text-2xl"></i>
                <span>{{ t['processing'] }}</span>
            </div>
        </div>

        <div class="max-w-5xl mx-auto w-full mt-32 grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="glass-panel p-8 rounded-xl border border-white/5 hover:border-white/20 transition-colors group">
                <i class="ph ph-shield-check text-4xl text-gray-500 group-hover:text-cyber-green transition-colors mb-4"></i>
                <h3 class="text-xl font-bold mb-2">{{ t['feat-1-title'] }}</h3>
                <p class="text-gray-400 text-sm">{{ t['feat-1-desc'] }}</p>
            </div>
            <div class="glass-panel p-8 rounded-xl border border-white/5 hover:border-white/20 transition-colors group">
                <i class="ph ph-lightning text-4xl text-gray-500 group-hover:text-cyber-green transition-colors mb-4"></i>
                <h3 class="text-xl font-bold mb-2">{{ t['feat-2-title'] }}</h3>
                <p class="text-gray-400 text-sm">{{ t['feat-2-desc'] }}</p>
            </div>
            <div class="glass-panel p-8 rounded-xl border border-white/5 hover:border-white/20 transition-colors group">
                <i class="ph ph-ghost text-4xl text-gray-500 group-hover:text-cyber-green transition-colors mb-4"></i>
                <h3 class="text-xl font-bold mb-2">{{ t['feat-3-title'] }}</h3>
                <p class="text-gray-400 text-sm">{{ t['feat-3-desc'] }}</p>
            </div>
        </div>
    </main>

    <footer class="border-t border-white/10 glass-panel mt-auto">
        <div class="max-w-7xl mx-auto px-6 py-8 flex flex-col md:flex-row items-center justify-between gap-4 font-mono text-sm text-gray-500">
            <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-cyber-green animate-pulse"></span>
                <span>{{ t['footer-status'] }}</span>
            </div>
            <div>
                &copy; {{ currentYear }} yous.ee. {{ t['footer-rights'] }}
            </div>
            <div class="flex gap-4">
                <a href="#" class="hover:text-cyber-green transition-colors">API</a>
                <a href="#" class="hover:text-cyber-green transition-colors">{{ t['footer-terms'] }}</a>
                <a href="#" class="hover:text-cyber-green transition-colors">{{ t['footer-privacy'] }}</a>
            </div>
        </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

// 动态注入外部资源 (代替原本在 <head> 中的内容)
useHead({
  title: 'yous.ee | Advanced URL Shortener',
  script: [
    { src: 'https://cdn.tailwindcss.com' },
    { src: 'https://cdn.jsdelivr.net/npm/@phosphor-icons/web' },
    { 
      innerHTML: `
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['Fira Code', 'monospace'],
                    },
                    colors: {
                        cyber: { black: '#050505', dark: '#111111', green: '#00ff41', greenDark: '#00cc33' }
                    }
                }
            }
        }
      ` 
    }
  ],
  link: [
    { rel: 'preconnect', href: 'https://fonts.googleapis.com' },
    { rel: 'preconnect', href: 'https://fonts.gstatic.com', crossorigin: '' },
    { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;600&family=Inter:wght@300;400;600&display=swap' }
  ]
})

// === 状态管理 ===
const currentYear = new Date().getFullYear()
const currentLang = ref('en')
const inputUrl = ref('')
const inputError = ref(false)
const isProcessing = ref(false)
const showResult = ref(false)
const shortenedUrl = ref('')
const isCopied = ref(false)

// 翻译字典 (缩减版，保留了你原本的内容)
const translations = {
    'en': {
        'hero-title': 'Share what <span class="text-cyber-green">yous.ee</span>',
        'hero-subtitle': '> yous.ee = "you see". Secure, edge-routed URL compression. Let the world see your vision instantly.',
        'btn-shorten': 'EXECUTE',
        'processing': 'Processing algorithms...',
        'result-status': '> System response: Link generated successfully.',
        'btn-copy': 'COPY',
        'btn-copied': 'COPIED!',
        'feat-1-title': 'Encrypted Logic',
        'feat-1-desc': 'Bank-grade security. URLs are sanitized and protected against malicious intent.',
        'feat-2-title': 'Zero Latency',
        'feat-2-desc': 'Edge-network routing ensures redirects happen in milliseconds globally.',
        'feat-3-title': 'Anonymous Mode',
        'feat-3-desc': 'No invasive tracking pixels. We respect your data and digital privacy.',
        'footer-status': 'System Online. All protocols nominal.',
        'footer-rights': 'All rights reserved.',
        'footer-terms': 'Terms',
        'footer-privacy': 'Privacy',
        'input-placeholder': 'https://your-long-url.com/goes/here'
    },
    'zh-CN': {
        'hero-title': '分享你所见 <span class="text-cyber-green font-mono">yous.ee</span>',
        'hero-subtitle': '> yous.ee 寓意 "you see"。安全、极速的边缘路由网址压缩，让世界瞬间看见你的“视界”。',
        'btn-shorten': '执行压缩',
        'processing': '算法处理中...',
        'result-status': '> 系统响应: 链接生成成功。',
        'btn-copy': '复制',
        'btn-copied': '已复制!',
        'feat-1-title': '加密逻辑',
        'feat-1-desc': '银行级安全标准。所有网址均经过清洗，有效防御恶意威胁。',
        'feat-2-title': '零延迟',
        'feat-2-desc': '全球边缘网络节点路由，确保毫秒级重定向响应。',
        'feat-3-title': '匿名模式',
        'feat-3-desc': '无侵入式追踪代码。我们绝对尊重您的数据与数字隐私。',
        'footer-status': '系统在线。各项协议运行正常。',
        'footer-rights': '保留所有权利。',
        'footer-terms': '服务条款',
        'footer-privacy': '隐私政策',
        'input-placeholder': '在此输入您的长网址...'
    }
    // 注意：为了控制篇幅，这里我只放了中英双语，你可以把你原本字典里剩下的语言直接粘贴补充到这里面。
}

const t = computed(() => translations[currentLang.value] || translations['en'])

// 初始化语言设置
onMounted(() => {
  if (process.client) {
    const savedLang = localStorage.getItem('yous_ee_lang')
    if (savedLang && translations[savedLang]) {
      currentLang.value = savedLang
    }
  }
})

// 监听语言切换
watch(currentLang, (newLang) => {
  if (process.client) {
    localStorage.setItem('yous_ee_lang', newLang)
    isCopied.value = false // 切换语言时重置复制按钮状态
  }
})

// === 业务逻辑 ===
const generateRandomString = (length) => {
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
    return Array.from({ length }, () => chars.charAt(Math.floor(Math.random() * chars.length))).join('')
}

const handleShorten = () => {
    if (!inputUrl.value.trim()) {
        inputError.value = true
        setTimeout(() => inputError.value = false, 1000)
        return
    }

    showResult.value = false
    isProcessing.value = true
    isCopied.value = false

    // 模拟网络请求延迟
    setTimeout(() => {
        const hash = generateRandomString(6)
        shortenedUrl.value = `https://yous.ee/${hash}`
        isProcessing.value = false
        showResult.value = true
        inputUrl.value = ''
    }, 1200)
}

const handleCopy = async () => {
    try {
        if (navigator.clipboard && window.isSecureContext) {
            await navigator.clipboard.writeText(shortenedUrl.value)
        } else {
            const textArea = document.createElement("textarea")
            textArea.value = shortenedUrl.value
            document.body.appendChild(textArea)
            textArea.focus()
            textArea.select()
            document.execCommand('copy')
            textArea.remove()
        }
        isCopied.value = true
    } catch (err) {
        console.error('Failed to copy text: ', err)
    }
}
</script>

<style scoped>
/* 限定在当前组件生效的样式 */
.cyber-theme {
    background-color: #050505;
    color: #ffffff;
}

.glass-panel {
    background: rgba(17, 17, 17, 0.6);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    border: 1px solid rgba(255, 255, 255, 0.05);
}

.input-glow:focus {
    box-shadow: 0 0 15px rgba(0, 255, 65, 0.2);
    border-color: rgba(0, 255, 65, 0.5);
    outline: none;
}

.glitch-hover:hover {
    text-shadow: 2px 0 #00ff41, -2px 0 #ff003c;
    transition: text-shadow 0.2s ease-in-out;
}

/* Minimalist Scrollbar */
::-webkit-scrollbar {
    width: 8px;
}
::-webkit-scrollbar-track {
    background: #050505;
}
::-webkit-scrollbar-thumb {
    background: #222;
    border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover {
    background: #00ff41;
}
</style>
