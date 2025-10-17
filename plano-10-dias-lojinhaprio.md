# 🚀 Plano de Otimização 10 Dias - Lojinha Prio

## 📌 Visão Geral do Projeto

**Site:** lojinhaprio.yoobe.app  
**Stack:** Next.js (Frontend) + Spree Commerce (Backend) + PostgreSQL + GCP  
**Prazo:** 10 dias  
**Objetivo:** Melhorar PageSpeed Score de [atual] para 90+  

---

## 📊 ANÁLISE DOS PROBLEMAS (Didática)

### 🔴 Problema 1: IMAGENS PESADAS E NÃO OTIMIZADAS

**O que é:**
Quando imagens de produtos não são comprimidas, o navegador do usuário precisa baixar arquivos gigantes (às vezes 2-5MB por imagem). Em uma página com 20 produtos, isso significa 40-100MB de download!

**Por que isso acontece:**
- Fotografias originais de alta resolução não foram processadas
- Formato antigo (PNG/JPG) ao invés de formatos modernos (WebP/AVIF)
- Falta de diferentes tamanhos para diferentes telas (mobile vs desktop)
- Todas as imagens carregam de uma vez (sem lazy loading)

**Impacto na experiência:**
- ⏱️ Usuário espera 10-15 segundos para ver produtos
- 📱 No celular em 4G, pode levar 30+ segundos
- 💸 Alta taxa de abandono (40-60% dos usuários saem)

**Ganho esperado ao resolver:**
- ⚡ **+20-30 pontos no PageSpeed**
- ⬇️ **80-90% redução no tamanho das imagens**
- 🚀 **Tempo de carregamento: de 12s → 3s**
- 💰 **Aumento de conversão estimado: +15-25%**

**Tempo necessário:** 8-12 horas

---

### 🔴 Problema 2: FALTA DE CACHE (CLOUDFLARE/CDN)

**O que é:**
Toda vez que alguém acessa seu site, o servidor GCP precisa processar tudo do zero. É como se você fizesse uma pizza do zero toda vez ao invés de ter pedaços prontos no freezer.

**Por que isso acontece:**
- Sem CDN configurado (todos os requests vão direto pro GCP)
- Headers de cache mal configurados
- Assets estáticos (JS, CSS, imagens) não são cacheados
- Cada visita consome recursos do servidor

**Impacto na experiência:**
- ⏱️ Servidor demora 2-4 segundos para responder
- 💰 Custos altos no GCP (servidor sobrecarregado)
- 🌍 Usuários longe do servidor têm experiência ruim
- 🔥 Site pode cair em picos de tráfego

**Ganho esperado ao resolver:**
- ⚡ **+15-25 pontos no PageSpeed**
- 🚀 **Tempo de resposta: de 2-4s → 0.2-0.5s**
- 💰 **Redução de custos GCP: 30-50%**
- 🌍 **Site rápido em qualquer lugar do mundo**

**Tempo necessário:** 4-6 horas

---

### 🔴 Problema 3: JAVASCRIPT GIGANTE (BUNDLE SIZE)

**O que é:**
Seu site carrega muito código JavaScript de uma vez. É como obrigar o usuário a ler um livro de 500 páginas antes de ver a primeira imagem do produto.

**Por que isso acontece:**
- Next.js carrega bibliotecas inteiras mesmo se usa só 10%
- Código de todas as páginas é carregado na primeira visita
- Bibliotecas antigas e pesadas (duplicadas às vezes)
- Falta de code splitting (separação de código)

**Impacto na experiência:**
- ⏱️ 3-6 segundos só para baixar e executar JavaScript
- 📱 Em celulares fracos, tela fica congelada
- 🖱️ Usuário clica mas nada acontece (site parece quebrado)
- 🔋 Gasta bateria do celular do usuário

**Ganho esperado ao resolver:**
- ⚡ **+10-15 pontos no PageSpeed**
- ⬇️ **Bundle size: de 800KB → 300KB (-60%)**
- 🚀 **Time to Interactive: de 6s → 2s**
- 📱 **Site funciona bem em celulares fracos**

**Tempo necessário:** 8-10 horas

---

### 🔴 Problema 4: QUERIES LENTAS NO BANCO (N+1)

**O que é:**
O banco de dados é consultado de forma ineficiente. É como ir ao supermercado 50 vezes para comprar 50 itens ao invés de comprar tudo de uma vez.

**Por que isso acontece (N+1 query):**
```ruby
# Exemplo do problema:
products = Product.all  # 1 query
products.each do |product|
  product.variants      # +50 queries (1 para cada produto!)
  product.images        # +50 queries
end
# Total: 101 queries ao invés de 1!
```

**Impacto na experiência:**
- ⏱️ Listagem de produtos demora 3-5 segundos
- 🔥 Banco de dados fica sobrecarregado
- 💰 Servidor GCP precisa ser maior (mais caro)
- 📉 Em horário de pico, site fica lento para todos

**Ganho esperado ao resolver:**
- ⚡ **+8-12 pontos no PageSpeed**
- 🚀 **Tempo de API: de 3-5s → 0.3-0.8s**
- 💰 **Redução de carga no banco: 80-90%**
- 📊 **Servidor pode atender 5-10x mais usuários**

**Tempo necessário:** 10-14 horas

---

### 🔴 Problema 5: CACHE DE API/REDIS AUSENTE

**O que é:**
Toda requisição busca dados novamente no banco, mesmo que sejam os mesmos produtos visualizados 1000x por dia.

**Por que isso acontece:**
- Sem camada de cache (Redis) configurada
- Spree consulta banco de dados em toda requisição
- Dados que mudam pouco (produtos) são recalculados sempre

**Impacto na experiência:**
- ⏱️ Cada página demora 1-2s a mais
- 🔥 Banco PostgreSQL fica 100% de CPU
- 💰 Precisaria escalar servidor (2x mais caro)
- 📉 Limit de conexões do banco pode ser atingido

**Ganho esperado ao resolver:**
- ⚡ **+10-15 pontos no PageSpeed**
- 🚀 **API response: de 1.5s → 0.1s (15x mais rápido!)**
- 💰 **Redução de carga no banco: 70-80%**
- 📊 **Pode atender 10x mais usuários simultâneos**

**Tempo necessário:** 6-8 horas

---

### 🟡 Problema 6: FONTS NÃO OTIMIZADAS

**O que é:**
Fontes customizadas (Google Fonts) atrasam renderização da página.

**Por que isso acontece:**
- Download de fontes do Google demora 300-500ms
- Navegador espera fonte carregar para mostrar texto (FOIT)
- Múltiplas fontes e pesos desnecessários

**Impacto na experiência:**
- ⏱️ Texto invisível por 0.5-1 segundo
- 👁️ Usuário vê página "em branco"
- 📱 Em conexões lentas, demora ainda mais

**Ganho esperado ao resolver:**
- ⚡ **+3-5 pontos no PageSpeed**
- 🚀 **First Contentful Paint: -0.5s**
- 👁️ **Texto aparece instantaneamente**

**Tempo necessário:** 2-3 horas

---

### 🟡 Problema 7: CSS NÃO UTILIZADO

**O que é:**
O site carrega 100% do CSS mas usa apenas 20-30% na primeira página.

**Por que isso acontece:**
- CSS de todas as páginas é carregado de uma vez
- Frameworks CSS (Bootstrap, Tailwind) com classes não usadas
- CSS antigo acumulado ao longo do tempo

**Impacto na experiência:**
- ⏱️ 300-500ms extras para baixar CSS
- 🖥️ Navegador precisa processar tudo

**Ganho esperado ao resolver:**
- ⚡ **+3-5 pontos no PageSpeed**
- ⬇️ **CSS size: de 200KB → 50KB (-75%)**
- 🚀 **First Contentful Paint: -0.3s**

**Tempo necessário:** 3-4 horas

---

### 🟡 Problema 8: ÍNDICES FALTANDO NO POSTGRESQL

**O que é:**
Banco de dados faz "varredura completa" em tabelas grandes ao invés de usar índices (atalhos).

**Analogia:**
Imagine procurar um nome em uma lista telefônica sem ordem alfabética. Você teria que ler TODAS as páginas!

**Por que isso acontece:**
- Tabelas antigas sem índices em campos filtrados
- Queries que usam WHERE, ORDER BY sem índice
- Índices compostos não criados para queries complexas

**Impacto na experiência:**
- ⏱️ Queries lentas (2-5 segundos)
- 🔥 CPU do banco a 100%
- 💰 Servidor mais caro necessário

**Ganho esperado ao resolver:**
- ⚡ **+5-8 pontos no PageSpeed**
- 🚀 **Queries: de 2-5s → 0.05-0.2s (10-100x mais rápido!)**
- 💰 **Pode reduzir tamanho da instância do banco**

**Tempo necessário:** 3-4 horas

---

## 🛠️ FERRAMENTAS - PARA QUE SERVEM

### Categoria 1: OTIMIZAÇÃO DE IMAGENS

#### **1.1 Cloudinary** 💰 $89/mês
**Para que serve:**
- Comprime imagens automaticamente (80-90% menor)
- Converte para WebP/AVIF automaticamente
- Gera múltiplos tamanhos para diferentes telas
- CDN global (entrega rápida no mundo todo)

**Problema que resolve:** Problema #1 (Imagens pesadas)

**Como funciona:**
```html
<!-- Antes (imagem gigante) -->
<img src="/products/tenis.jpg" /> <!-- 2.5MB -->

<!-- Depois (Cloudinary otimiza automaticamente) -->
<img src="https://res.cloudinary.com/lojinha/tenis.jpg?w=400&f_auto&q_auto" />
<!-- 45KB apenas! -->
```

**Por que vale o investimento:**
- Economiza 80-90% de banda (reduz custos GCP)
- Conversão aumenta 15-25% (mais vendas)
- ROI: Paga a mensalidade em 1-2 dias

---

#### **1.2 Next.js Image Component** 🆓 Grátis
**Para que serve:**
- Lazy loading automático (só carrega quando vai aparecer)
- Gera tamanhos otimizados
- Placeholder blur enquanto carrega

**Problema que resolve:** Problema #1 (Imagens pesadas)

**Como usar:**
```jsx
// Antes
<img src={product.image} alt={product.name} />

// Depois
import Image from 'next/image';
<Image 
  src={product.image} 
  alt={product.name}
  width={400}
  height={400}
  loading="lazy"
  quality={85}
/>
```

---

### Categoria 2: CACHE E CDN

#### **2.1 Cloudflare** 💰 $0-20/mês
**Para que serve:**
- CDN global (151 datacenters no mundo)
- Cache automático de assets (imagens, JS, CSS)
- DDoS protection incluído
- Compressão Brotli (arquivos 20% menores)

**Problema que resolve:** Problema #2 (Falta de cache)

**Como funciona:**
```
Sem Cloudflare:
Usuário (Brasil) → GCP US (200ms latência) → 2s processamento = 2.2s

Com Cloudflare:
Usuário (Brasil) → Cloudflare São Paulo (cache hit) = 0.05s
```

**Configuração:** 1 hora
**Ganho:** +15-25 pontos PageSpeed

---

#### **2.2 Redis (GCP Memorystore)** 💰 ~$50/mês (2GB)
**Para que serve:**
- Cache em memória (super rápido)
- Armazena resultados de queries
- Cache de sessões de usuários
- Cache de listagens de produtos

**Problema que resolve:** Problema #5 (Cache de API ausente)

**Como funciona:**
```ruby
# Antes: busca no banco sempre (1.5s)
def get_products
  Product.includes(:variants, :images).all
end

# Depois: cache por 30 minutos (0.05s)
def get_products
  Rails.cache.fetch('products_list', expires_in: 30.minutes) do
    Product.includes(:variants, :images).all
  end
end
```

**Configuração:** 4 horas
**Ganho:** +10-15 pontos PageSpeed

---

### Categoria 3: ANÁLISE E MONITORAMENTO

#### **3.1 Lighthouse CI** 🆓 Grátis
**Para que serve:**
- Testa performance automaticamente a cada commit
- Previne regressões (código novo piorar performance)
- Gera relatórios comparativos

**Problema que resolve:** Monitoramento contínuo

**Como usar:**
```bash
npm install -g @lhci/cli
lhci autorun --collect.url=https://lojinhaprio.yoobe.app
```

---

#### **3.2 Sentry Performance** 💰 $29/mês
**Para que serve:**
- Detecta queries lentas em produção
- Rastreia erros de JavaScript
- Monitora experiência real dos usuários
- Alerts automáticos quando algo piora

**Problema que resolve:** Identificação de gargalos em produção

**Como funciona:**
- Instala SDK no Next.js e Rails
- Envia telemetria automaticamente
- Dashboard mostra: queries lentas, páginas lentas, erros

---

#### **3.3 pgAnalyze** 💰 $49/mês
**Para que serve:**
- Analisa queries PostgreSQL automaticamente
- Sugere índices que você deveria criar
- Alerta sobre queries lentas
- Mostra uso de CPU/RAM do banco

**Problema que resolve:** Problema #4 e #8 (Queries lentas)

**Como funciona:**
1. Conecta no seu banco PostgreSQL (GCP)
2. Coleta estatísticas automaticamente
3. IA analisa e sugere melhorias
4. Mostra exatamente qual índice criar

---

### Categoria 4: DESENVOLVIMENTO COM IA

#### **4.1 Cursor IDE** 💰 $20/mês
**Para que serve:**
- Editor de código com IA integrada
- Refatoração assistida
- Autocomplete inteligente
- Explica código complexo

**Problema que resolve:** Acelera desenvolvimento 3-5x

**Exemplos de uso:**
```
Você: "Otimize este componente React para performance"
Cursor: [gera código com useMemo, lazy loading, etc]

Você: "Adicione índices nesta tabela do Spree"
Cursor: [gera migration com índices apropriados]
```

---

#### **4.2 GitHub Copilot** 💰 $10/mês
**Para que serve:**
- Autocomplete de código com IA
- Sugere funções completas
- Gera testes automaticamente

**Problema que resolve:** Acelera desenvolvimento 2-3x

---

#### **4.3 Claude/ChatGPT** 💰 $20/mês
**Para que serve:**
- Análise de código completo
- Sugestões de arquitetura
- Debugging assistido
- Explicações didáticas

**Exemplo de uso:**
```
Prompt: "Analise esta query Rails e explique por que está lenta:
[código]

Sugira otimizações com eager loading e índices necessários."
```

---

### Categoria 5: BUNDLE ANALYSIS

#### **5.1 Webpack Bundle Analyzer** 🆓 Grátis
**Para que serve:**
- Visualiza tamanho do JavaScript
- Mostra quais bibliotecas ocupam mais espaço
- Identifica duplicações

**Problema que resolve:** Problema #3 (JavaScript gigante)

**Como usar:**
```bash
npm install @next/bundle-analyzer
ANALYZE=true npm run build
# Abre visualização interativa
```

---

### Categoria 6: DATABASE OPTIMIZATION

#### **6.1 Bullet Gem** 🆓 Grátis
**Para que serve:**
- Detecta N+1 queries automaticamente
- Alerta no log de desenvolvimento
- Mostra exatamente onde adicionar includes()

**Problema que resolve:** Problema #4 (N+1 queries)

**Como usar:**
```ruby
# Gemfile
gem 'bullet', group: 'development'

# Bullet detecta e mostra:
# USE: Product.includes(:variants, :images)
# ao invés de: Product.all
```

---

#### **6.2 PgHero** 🆓 Grátis (gem)
**Para que serve:**
- Dashboard de saúde do PostgreSQL
- Mostra queries lentas
- Sugere índices
- Monitora conexões

**Como usar:**
```bash
gem install pghero
# Acessa dashboard em localhost:3000/pghero
```

---

### Categoria 7: TESTING

#### **7.1 WebPageTest** 🆓 Grátis
**Para que serve:**
- Testa performance de diferentes locais do mundo
- Simula conexões lentas (3G, 4G)
- Waterfall detalhado (vê ordem de carregamento)

**Como usar:**
webpagetest.org → inserir URL → testar

---

## 📅 CRONOGRAMA 10 DIAS (DETALHADO)

### 📋 **Equipe Necessária:**
- **2 Developers Backend/Fullstack** (idealmente)
- **OU 1 Developer Fullstack + 1 PM técnico**

**Carga de trabalho:** 8-10 horas/dia (intensivo)

---

## 🗓️ DIA 1 - SEGUNDA-FEIRA (Setup + Diagnóstico)

### Manhã (4h): Setup de Ferramentas
- [ ] **0:00-1:00** - Contratar e configurar contas
  - Cloudflare (Free ou Pro $20/mês)
  - Cloudinary ($89/mês - usar trial se disponível)
  - Cursor IDE ($20/mês)
  - GitHub Copilot ($10/mês)
  - Sentry ($29/mês - usar trial)
  
- [ ] **1:00-2:00** - Configurar ambientes
  - Clonar repositório
  - Configurar ambiente de staging (GCP)
  - Instalar Cursor + Copilot
  
- [ ] **2:00-3:00** - Setup de monitoramento
  - Integrar Sentry no Next.js
  - Integrar Sentry no Rails (Spree)
  - Configurar alerts básicos
  
- [ ] **3:00-4:00** - Ferramentas de análise
  - Instalar Lighthouse CI
  - Configurar Webpack Bundle Analyzer
  - Instalar Bullet gem
  - Instalar PgHero

### Tarde (4h): Diagnóstico Profundo
- [ ] **4:00-5:00** - Medir baseline
  - Rodar PageSpeed Insights (desktop + mobile)
  - Rodar WebPageTest de 3 locais diferentes
  - Documentar métricas atuais
  
- [ ] **5:00-6:00** - Análise de imagens
  - Rodar script de análise de imagens
  - Listar top 20 imagens maiores
  - Calcular economia potencial
  
- [ ] **6:00-7:00** - Análise de JavaScript
  - Rodar Bundle Analyzer
  - Identificar bibliotecas maiores
  - Marcar código não utilizado
  
- [ ] **7:00-8:00** - Análise de banco de dados
  - Conectar PgHero
  - Identificar queries lentas (top 20)
  - Verificar índices ausentes
  - Documentar problemas

**Entregável do Dia 1:**
- ✅ Todas as ferramentas configuradas
- ✅ Baseline documentado (métricas atuais)
- ✅ Lista priorizada de problemas
- ✅ Dashboard de monitoramento funcionando

---

## 🗓️ DIA 2 - TERÇA-FEIRA (Cloudflare + Cache)

### Manhã (4h): Configurar Cloudflare
- [ ] **0:00-1:00** - Setup básico
  - Apontar DNS para Cloudflare
  - Aguardar propagação
  - Configurar SSL/TLS
  
- [ ] **1:00-2:00** - Page Rules
  - Cache para /static/* (1 mês)
  - Cache para /images/* (1 mês)
  - Cache para /api/products* (10 min)
  - Bypass cache para /checkout*, /cart*
  
- [ ] **2:00-3:00** - Otimizações Cloudflare
  - Ativar Auto Minify (JS, CSS, HTML)
  - Ativar Brotli compression
  - Ativar HTTP/2, HTTP/3
  - Configurar Rocket Loader (testar)
  
- [ ] **3:00-4:00** - Testing
  - Testar cache headers com curl
  - Verificar HIT/MISS nos headers
  - Medir melhoria com PageSpeed
  - Ajustar configs se necessário

### Tarde (4h): Configurar Redis
- [ ] **4:00-5:00** - Provisionar Redis
  - Criar instância Memorystore (GCP)
  - Configurar VPC networking
  - Testar conectividade
  
- [ ] **5:00-6:00** - Integrar no Rails
  - Configurar Rails cache_store
  - Testar conexão Redis
  - Cache de sessões
  
- [ ] **6:00-7:00** - Cache de queries
  - Implementar cache em ProductsController
  - Implementar cache em TaxonsController
  - Cache de menus/categorias
  - TTL apropriado (15-30 min)
  
- [ ] **7:00-8:00** - Testing + Invalidação
  - Testar cache funcionando
  - Implementar cache invalidation
  - Medir ganho de performance
  - Documentar estratégia de cache

**Ganho esperado Dia 2:** +20-30 pontos PageSpeed

**Checkpoint:**
- ✅ Cloudflare ativo e cacheando
- ✅ Redis funcionando
- ✅ Cache de API implementado
- ✅ Medir antes/depois

---

## 🗓️ DIA 3 - QUARTA-FEIRA (Otimização de Imagens - Parte 1)

### Manhã (4h): Setup Cloudinary
- [ ] **0:00-1:00** - Configurar conta
  - Criar conta Cloudinary
  - Configurar upload presets
  - Gerar API keys
  - Testar upload manual
  
- [ ] **1:00-2:00** - Migração de imagens
  - Script para upload em massa
  - Começar upload de imagens produtos (background)
  - Verificar qualidade após conversão
  
- [ ] **2:00-3:00** - Integração Next.js
  - Instalar next-cloudinary
  - Configurar next.config.js
  - Criar wrapper component
  
- [ ] **3:00-4:00** - Atualizar código
  - Substituir <img> por <Image> do Next.js
  - Apontar URLs para Cloudinary
  - Implementar lazy loading

### Tarde (4h): Otimização Avançada
- [ ] **4:00-5:00** - Responsive images
  - Configurar breakpoints
  - Gerar múltiplos tamanhos
  - Testar em mobile/desktop
  
- [ ] **5:00-6:00** - Placeholders blur
  - Gerar blur placeholders
  - Implementar em componentes
  - Testar UX
  
- [ ] **6:00-7:00** - Lazy loading agressivo
  - Apenas hero image prioritária
  - Resto lazy load
  - Intersection Observer para trigger
  
- [ ] **7:00-8:00** - Testing + Ajustes
  - Medir ganho de performance
  - Ajustar qualidade se necessário
  - Verificar todas as páginas
  - Documentar configurações

**Ganho esperado Dia 3:** +15-20 pontos PageSpeed

**Checkpoint:**
- ✅ Cloudinary configurado
- ✅ Imagens migrando (pode continuar em background)
- ✅ Next.js Image implementado
- ✅ Lazy loading funcionando

---

## 🗓️ DIA 4 - QUINTA-FEIRA (Otimização de Imagens - Parte 2 + Fonts)

### Manhã (3h): Finalizar Imagens
- [ ] **0:00-1:00** - Completar migração
  - Verificar todas as imagens migradas
  - Resolver problemas de upload
  - Backup de imagens originais
  
- [ ] **1:00-2:00** - Product images
  - Verificar imagens de produtos OK
  - Testar em páginas de categoria
  - Testar em página de produto
  - Ajustar tamanhos se necessário
  
- [ ] **2:00-3:00** - Hero/Banner images
  - Otimizar imagens de hero
  - Implementar priority loading
  - Testar Above the Fold
  - Medir LCP (deve melhorar muito)

### Tarde (5h): Otimização de Fonts
- [ ] **3:00-4:00** - Análise de fontes
  - Identificar fontes usadas
  - Identificar pesos não utilizados
  - Calcular tamanho total
  
- [ ] **4:00-5:00** - Next.js Font Optimization
  - Migrar para next/font
  - Self-host Google Fonts
  - Remover fontes não usadas
  - Configurar font-display: swap
  
- [ ] **5:00-6:00** - Preload de fontes críticas
  - Preload de fonte principal
  - Otimizar ordem de carregamento
  - Testar FOIT/FOUT
  
- [ ] **6:00-7:00** - Variable fonts (se aplicável)
  - Considerar variable fonts
  - Implementar se reduz bundle
  
- [ ] **7:00-8:00** - Testing final
  - Medir ganho FCP
  - Verificar texto sempre visível
  - Cross-browser testing
  - Documentar

**Ganho esperado Dia 4:** +8-12 pontos PageSpeed

**Checkpoint:**
- ✅ 100% imagens otimizadas
- ✅ Fonts otimizadas
- ✅ LCP melhorado significativamente
- ✅ PageSpeed Score ~70-80 agora

---

## 🗓️ DIA 5 - SEXTA-FEIRA (JavaScript Bundle)

### Manhã (4h): Análise e Code Splitting
- [ ] **0:00-1:00** - Bundle Analysis profundo
  - Analisar visualização do Bundle Analyzer
  - Listar top 10 bibliotecas maiores
  - Identificar duplicações
  - Marcar código não usado
  
- [ ] **1:00-2:00** - Remover dependencies
  - Remover bibliotecas não usadas
  - Substituir bibliotecas pesadas por leves
  - Exemplo: lodash → lodash-es (tree-shakeable)
  - Exemplo: moment → date-fns (menor)
  
- [ ] **2:00-3:00** - Code Splitting básico
  - Dynamic imports para rotas
  - Lazy load de modais
  - Lazy load de componentes pesados
  - Split vendor chunks
  
- [ ] **3:00-4:00** - Optimizar Next.js config
  - Configurar SWC minifier
  - Configurar splitChunks otimizado
  - Configurar optimizePackageImports
  - Build e analisar resultado

### Tarde (4h): React Optimization
- [ ] **4:00-5:00** - Component optimization
  - Identificar componentes com re-renders
  - Adicionar React.memo onde apropriado
  - Adicionar useMemo/useCallback
  - Usar Cursor IDE para sugestões
  
- [ ] **5:00-6:00** - Lazy loading avançado
  - Dynamic imports agressivos
  - Suspense boundaries
  - Error boundaries
  - Loading skeletons
  
- [ ] **6:00-7:00** - Tree shaking
  - Verificar sideEffects no package.json
  - Imports named ao invés de default
  - Remover dead code
  
- [ ] **7:00-8:00** - Testing + Medição
  - Build production
  - Analisar bundle final
  - Medir TBT, TTI
  - Documentar redução

**Ganho esperado Dia 5:** +10-15 pontos PageSpeed

**Meta bundle size:** <300KB (gzipped)

**Checkpoint:**
- ✅ Bundle reduzido 50-60%
- ✅ Code splitting implementado
- ✅ Components otimizados
- ✅ TBT e TTI melhorados

---

## 🗓️ DIA 6 - SÁBADO (Database - N+1 Queries)

### Manhã (4h): Detectar e Corrigir N+1
- [ ] **0:00-1:00** - Ativar Bullet
  - Configurar Bullet gem
  - Rodar app em development
  - Navegar por todas as páginas
  - Coletar alertas N+1
  
- [ ] **1:00-2:00** - Products Controller
  - Corrigir N+1 em listagem de produtos
  - Adicionar includes apropriados
  ```ruby
  # Antes
  @products = Product.all
  
  # Depois
  @products = Product.includes(:variants, :images, :default_variant)
  ```
  - Testar consulta no console
  - Medir antes/depois
  
- [ ] **2:00-3:00** - Taxons/Categories
  - Corrigir N+1 em categorias
  - Eager load de produtos
  - Testar navegação de categorias
  
- [ ] **3:00-4:00** - Orders/Checkout
  - Corrigir N+1 em checkout
  - Otimizar line_items loading
  - Testar fluxo de compra

### Tarde (4h): Queries Customizadas
- [ ] **4:00-5:00** - Identificar queries lentas
  - Analisar PgHero
  - Copiar top 5 queries lentas
  - Usar EXPLAIN ANALYZE
  
- [ ] **5:00-6:00** - Reescrever queries
  - Usar Claude/ChatGPT para otimizar
  - Testar queries otimizadas
  - Medir ganho (deve ser 5-10x)
  - Implementar no código
  
- [ ] **6:00-7:00** - Pagination
  - Implementar paginação eficiente
  - Usar kaminari ou pagy
  - Limitar resultados (25-50 por página)
  - Testar com muitos produtos
  
- [ ] **7:00-8:00** - Testing + Cache
  - Adicionar cache em queries pesadas
  - Testar com Redis
  - Medir tempo de resposta API
  - Documentar melhorias

**Ganho esperado Dia 6:** +8-12 pontos PageSpeed

**Meta:** Todas as APIs <500ms

**Checkpoint:**
- ✅ Zero N+1 queries
- ✅ Queries lentas otimizadas
- ✅ API response time reduzido 80%
- ✅ Paginação implementada

---

## 🗓️ DIA 7 - DOMINGO (Database - Índices)

### Manhã (4h): Criar Índices
- [ ] **0:00-1:00** - Analisar índices atuais
  - Query para listar índices existentes
  - Identificar índices não usados
  - Planejar índices novos
  
- [ ] **1:00-2:00** - Índices em Products
  ```sql
  CREATE INDEX CONCURRENTLY idx_products_available_on 
    ON spree_products(available_on) 
    WHERE available_on IS NOT NULL;
  
  CREATE INDEX CONCURRENTLY idx_products_name_trgm 
    ON spree_products USING gin(name gin_trgm_ops);
  
  CREATE INDEX CONCURRENTLY idx_products_slug 
    ON spree_products(slug);
  ```
  - Testar impacto em queries
  - Medir antes/depois
  
- [ ] **2:00-3:00** - Índices em Variants
  ```sql
  CREATE INDEX CONCURRENTLY idx_variants_product_id 
    ON spree_variants(product_id);
  
  CREATE INDEX CONCURRENTLY idx_variants_sku 
    ON spree_variants(sku) 
    WHERE sku IS NOT NULL;
  ```
  
- [ ] **3:00-4:00** - Índices em Orders/Line Items
  ```sql
  CREATE INDEX CONCURRENTLY idx_orders_completed_at 
    ON spree_orders(completed_at) 
    WHERE completed_at IS NOT NULL;
  
  CREATE INDEX CONCURRENTLY idx_line_items_order_variant 
    ON spree_line_items(order_id, variant_id);
  ```

### Tarde (4h): Database Tuning
- [ ] **4:00-5:00** - PostgreSQL Configuration
  - Usar pgtune.leopard.in.ua
  - Ajustar shared_buffers
  - Ajustar work_mem
  - Ajustar effective_cache_size
  - Aplicar configs (requer restart)
  
- [ ] **5:00-6:00** - Connection Pooling
  - Avaliar pool atual
  - Ajustar pool size no Rails
  - Considerar PgBouncer se necessário
  - Monitorar conexões
  
- [ ] **6:00-7:00** - VACUUM e ANALYZE
  - Rodar VACUUM ANALYZE em tabelas grandes
  - Configurar autovacuum
  - Verificar dead tuples
  
- [ ] **7:00-8:00** - Testing + Monitoring
  - Stress test do banco
  - Medir query times após índices
  - Configurar monitoring contínuo
  - Documentar todas as mudanças

**Ganho esperado Dia 7:** +5-8 pontos PageSpeed

**Meta:** 90% queries <100ms

**Checkpoint:**
- ✅ Índices estratégicos criados
- ✅ PostgreSQL tuned
- ✅ Queries 10-100x mais rápidas
- ✅ Banco preparado para escala

---

## 🗓️ DIA 8 - SEGUNDA-FEIRA (Critical CSS + Rendering)

### Manhã (4h): CSS Optimization
- [ ] **0:00-1:00** - Análise de CSS
  - Identificar CSS não usado
  - Medir tamanho atual
  - Usar Coverage tool (Chrome DevTools)
  
- [ ] **1:00-2:00** - Critical CSS
  - Extrair CSS crítico (Above the Fold)
  - Inlinizar CSS crítico no <head>
  - Defer CSS não crítico
  ```html
  <style>/* CSS crítico inline */</style>
  <link rel="preload" href="/style.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
  ```
  
- [ ] **2:00-3:00** - Remover CSS não usado
  - Usar PurgeCSS se Tailwind
  - Remover classes antigas
  - Minificação agressiva
  
- [ ] **3:00-4:00** - Testing
  - Medir FCP antes/depois
  - Verificar nada quebrou
  - Cross-browser testing

### Tarde (4h): Rendering Strategy
- [ ] **4:00-5:00** - Análise de páginas
  - Classificar páginas:
    - Estáticas (sobre, contato) → SSG
    - Semi-estáticas (produtos) → ISR
    - Dinâmicas (carrinho) → SSR/CSR
  
- [ ] **5:00-6:00** - Implementar ISR
  ```jsx
  // pages/products/[slug].js
  export async function getStaticProps({ params }) {
    return {
      props: { product },
      revalidate: 3600 // 1 hora
    }
  }
  ```
  - Implementar em páginas de produto
  - Implementar em categorias
  
- [ ] **6:00-7:00** - Static Generation
  - Gerar páginas estáticas no build
  - Páginas de conteúdo (sobre, FAQ)
  - Testar performance
  
- [ ] **7:00-8:00** - Preload/Prefetch
  - Preload de recursos críticos
  - Prefetch de páginas prováveis
  ```html
  <link rel="preload" href="/api/products" as="fetch" crossorigin>
  <link rel="prefetch" href="/checkout">
  ```
  - Testar navegação

**Ganho esperado Dia 8:** +5-8 pontos PageSpeed

**Checkpoint:**
- ✅ CSS otimizado e inline
- ✅ ISR implementado
- ✅ Rendering strategy otimizada
- ✅ FCP melhorado

---

## 🗓️ DIA 9 - TERÇA-FEIRA (Fine-tuning + GCP)

### Manhã (4h): GCP Optimization
- [ ] **0:00-1:00** - Cloud CDN
  - Ativar Cloud CDN para static assets
  - Configurar bucket do Cloud Storage
  - Migrar assets estáticos
  - Configurar cache headers
  
- [ ] **1:00-2:00** - Load Balancer
  - Revisar configuração do LB
  - Health checks otimizados
  - Connection draining
  - Logging e monitoring
  
- [ ] **2:00-3:00** - Instâncias otimizadas
  - Analisar uso de CPU/RAM
  - Right-sizing das instâncias
  - Considerar preemptible VMs (economia)
  - Testar sob carga
  
- [ ] **3:00-4:00** - Cloud SQL optimization
  - Revisar configurações do PostgreSQL
  - High availability se necessário
  - Backups automáticos configurados
  - Read replicas se aplicável

### Tarde (4h): Performance Fine-tuning
- [ ] **4:00-5:00** - Compression
  - Verificar Brotli ativo
  - Compression level otimizado
  - Testar todos os tipos de arquivo
  
- [ ] **5:00-6:00** - HTTP/2 e HTTP/3
  - Verificar HTTP/2 ativo
  - Ativar HTTP/3 (QUIC)
  - Server Push para recursos críticos
  - Testar melhorias
  
- [ ] **6:00-7:00** - Service Worker (opcional)
  - Avaliar benefit de SW
  - Implementar se fizer sentido
  - Cache de assets offline
  - Testar em produção
  
- [ ] **7:00-8:00** - Preconnect/DNS Prefetch
  ```html
  <link rel="preconnect" href="https://res.cloudinary.com">
  <link rel="dns-prefetch" href="https://analytics.google.com">
  ```
  - Identificar domínios externos
  - Adicionar preconnect
  - Medir ganho

**Ganho esperado Dia 9:** +3-5 pontos PageSpeed

**Checkpoint:**
- ✅ GCP otimizado
- ✅ Infraestrutura scalable
- ✅ Fine-tuning completo
- ✅ Performance muito próxima do ideal

---

## 🗓️ DIA 10 - QUARTA-FEIRA (Testing + Deploy + Documentação)

### Manhã (4h): Testing Completo
- [ ] **0:00-1:00** - Lighthouse CI completo
  - Rodar Lighthouse em TODAS as páginas
  - Desktop + Mobile
  - Documentar scores
  - Verificar se meta (90+) foi atingida
  
- [ ] **1:00-2:00** - Load Testing
  - Usar k6 ou Artillery
  - Simular 100-500 usuários simultâneos
  - Verificar se mantém performance
  - Identificar gargalos restantes
  
- [ ] **2:00-3:00** - Real Device Testing
  - Testar em celulares reais
  - iOS + Android
  - Diferentes navegadores
  - Conexões lentas (3G)
  - Coletar feedback
  
- [ ] **3:00-4:00** - Cross-browser Testing
  - Chrome, Firefox, Safari, Edge
  - Versões antigas se relevante
  - Verificar funcionalidades
  - Screenshot testing

### Tarde (4h): Deploy + Documentação
- [ ] **4:00-5:00** - Staging Deploy
  - Deploy para staging
  - Smoke tests completos
  - Testar fluxo de compra E2E
  - Verificar analytics funcionando
  
- [ ] **5:00-6:00** - Production Deploy
  - Backup completo do banco
  - Deploy gradual (canary se possível)
  - Monitorar erros (Sentry)
  - Rollback plan pronto
  
- [ ] **6:00-7:00** - Monitoring pós-deploy
  - Monitorar primeiras horas
  - Verificar métricas em produção
  - Ajustar cache se necessário
  - Resolver issues urgentes
  
- [ ] **7:00-8:00** - Documentação final
  - Documentar todas as mudanças
  - Criar runbook de manutenção
  - Métricas antes/depois
  - Próximos passos recomendados

**Checkpoint Final:**
- ✅ PageSpeed Score 90+ (desktop)
- ✅ PageSpeed Score 85+ (mobile)
- ✅ Em produção e estável
- ✅ Documentação completa

---

## 📊 MÉTRICAS - ANTES vs DEPOIS

### Baseline (Medir no Dia 1):
```
Performance Score (Desktop): _____ / 100
Performance Score (Mobile): _____ / 100
LCP (Largest Contentful Paint): _____ s
FID (First Input Delay): _____ ms
CLS (Cumulative Layout Shift): _____
TBT (Total Blocking Time): _____ ms
TTI (Time to Interactive): _____ s
Speed Index: _____ s

Bundle Size JS: _____ KB
Bundle Size CSS: _____ KB
Total Page Size: _____ MB
Number of Requests: _____

API Response Time (products): _____ ms
API Response Time (categories): _____ ms
Database Query Time (avg): _____ ms
```

### Meta Final (Dia 10):
```
Performance Score (Desktop): 90+ / 100 ✅
Performance Score (Mobile): 85+ / 100 ✅
LCP: < 2.5s ✅
FID: < 100ms ✅
CLS: < 0.1 ✅
TBT: < 200ms ✅
TTI: < 3.5s ✅
Speed Index: < 3.0s ✅

Bundle Size JS: < 300KB ✅
Bundle Size CSS: < 50KB ✅
Total Page Size: < 1MB ✅
Number of Requests: < 50 ✅

API Response Time (products): < 300ms ✅
API Response Time (categories): < 200ms ✅
Database Query Time (avg): < 50ms ✅
```

---

## 💰 INVESTIMENTO TOTAL (10 DIAS)

### Ferramentas Pagas (Essenciais):
| Ferramenta | Custo Mensal | Uso | Pode Cancelar Depois? |
|------------|--------------|-----|----------------------|
| Cursor IDE | $20 | Dev | ❌ (recomendar manter) |
| GitHub Copilot | $10 | Dev | ❌ (recomendar manter) |
| Cloudflare Pro | $20 | CDN | ❌ (essencial) |
| Cloudinary | $89 | Imagens | ❌ (essencial) |
| Sentry | $29 | Monitoring | ⚠️ (pode downgrade) |
| pgAnalyze | $49 | Database | ✅ (pode cancelar) |
| GCP Memorystore (Redis) | ~$50 | Cache | ❌ (essencial) |
| **TOTAL** | **~$267/mês** | | |

### Ferramentas Gratuitas:
- ✅ Lighthouse CI
- ✅ Webpack Bundle Analyzer
- ✅ Bullet Gem
- ✅ PgHero
- ✅ WebPageTest
- ✅ Chrome DevTools
- ✅ PageSpeed Insights

### ROI Esperado:
**Investimento:** $267/mês  
**Ganhos:**
- 🎯 Conversão aumenta 15-25% → +$XXX em vendas/mês
- 💰 Custos GCP reduzem 30-40% → -$XXX/mês
- 📊 SEO melhora → +tráfego orgânico
- ⭐ UX melhora → +retenção de clientes

**Payback:** 1-3 meses (dependendo do volume de vendas)

---

## 🚨 RISCOS E PLANOS B

### Risco 1: Migração de Imagens Falhar
**Plano B:**
- Usar Cloudinary apenas para novas imagens
- Implementar Next.js Image nas imagens atuais
- Migração gradual ao longo de 30 dias

### Risco 2: Redis Não Trazer Ganho Esperado
**Plano B:**
- Usar cache em memória do Rails (menor ganho)
- Focar em otimização de queries
- Investir mais em Cloudflare cache

### Risco 3: Bundle Size Não Reduzir o Suficiente
**Plano B:**
- Code splitting mais agressivo
- Considerar migração para bibliotecas menores
- Lazy load de todo código não crítico

### Risco 4: Queries Ainda Lentas Após Índices
**Plano B:**
- Materialized views para queries complexas
- Denormalização seletiva
- Cache mais agressivo

### Risco 5: Não Atingir 90+ no PageSpeed
**Plano B:**
- 85+ já é muito bom e traz resultados
- Priorizar mobile performance
- Continuar otimizações incrementais

---

## ✅ CHECKLIST DIÁRIO

### Todo Dia:
- [ ] Morning standup (15 min)
- [ ] Medir métricas (PageSpeed)
- [ ] Commit com descrição de ganhos
- [ ] Atualizar dashboard de progresso
- [ ] Documentar bloqueios
- [ ] Evening review (15 min)

### A Cada Deploy:
- [ ] Testar em staging primeiro
- [ ] Lighthouse CI passou
- [ ] Nenhum erro no Sentry
- [ ] Métricas melhoraram ou mantiveram
- [ ] Rollback plan pronto

---

## 📚 RECURSOS DE APRENDIZADO

### Documentação Oficial:
- **Next.js Performance**: https://nextjs.org/docs/advanced-features/measuring-performance
- **Web.dev**: https://web.dev/fast/
- **Spree Guides**: https://guides.spreecommerce.org/
- **PostgreSQL Tuning**: https://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server

### Use IA para Acelerar:
```
# Exemplo de prompts úteis:

Para Cursor/Claude:
"Explique este código Spree e sugira otimizações de performance"

Para ChatGPT:
"Analise esta query PostgreSQL e sugira índices:
[colar SQL]"

Para Copilot:
// Comentário: "Otimizar este componente React para evitar re-renders"
// [Copilot sugere código]
```

---

## 🎯 DEFINIÇÃO DE SUCESSO

### Sucesso MÍNIMO (Aceitável):
- ✅ PageSpeed Desktop: 85+
- ✅ PageSpeed Mobile: 75+
- ✅ LCP < 3s
- ✅ Site estável em produção

### Sucesso ESPERADO (Meta):
- ✅ PageSpeed Desktop: 90+
- ✅ PageSpeed Mobile: 85+
- ✅ LCP < 2.5s
- ✅ Todas as Core Web Vitals no verde

### Sucesso EXCEPCIONAL (Esticar):
- ✅ PageSpeed Desktop: 95+
- ✅ PageSpeed Mobile: 90+
- ✅ LCP < 2s
- ✅ FID < 50ms
- ✅ Bundle size < 250KB

---

## 📞 SUPORTE DURANTE OS 10 DIAS

### Se travar em algum problema:
1. **Consultar Claude/ChatGPT** - Cola o erro e pede ajuda
2. **Documentação oficial** - Sempre a fonte mais confiável
3. **Stack Overflow** - Buscar erro específico
4. **Comunidades**:
   - Next.js Discord
   - Spree Slack
   - PostgreSQL Mailing List

### Escalação:
- **Bloqueio < 1h**: Tentar resolver sozinho + IA
- **Bloqueio 1-2h**: Buscar ajuda em comunidades
- **Bloqueio > 2h**: Considerar consultor freelance (Upwork)

---

## 🎉 PÓS-PROJETO (Dia 11+)

### Manutenção Contínua:
- [ ] Monitoring diário (Sentry dashboard)
- [ ] PageSpeed semanal
- [ ] Review de queries lentas (mensal)
- [ ] Atualizar dependencies (mensal)
- [ ] Otimizações incrementais

### Próximos Passos (Backlog):
1. Upgrade do Spree (se versão muito antiga)
2. Migração para Next.js 14+ (se em versão antiga)
3. Implementar PWA (offline support)
4. A/B testing de performance improvements
5. Advanced caching strategies

---

## 📋 TEMPLATE DE RELATÓRIO FINAL

```markdown
# Relatório de Otimização - Lojinha Prio

## Período: [Data Início] - [Data Fim]

### Métricas Antes/Depois:

| Métrica | Antes | Depois | Melhoria |
|---------|-------|--------|----------|
| PageSpeed Desktop | XX | XX | +XX pts |
| PageSpeed Mobile | XX | XX | +XX pts |
| LCP | X.Xs | X.Xs | -XX% |
| TBT | XXXms | XXXms | -XX% |
| Bundle Size | XXkb | XXkb | -XX% |
| API Response | XXXms | XXms | -XX% |

### Mudanças Implementadas:
1. ✅ Cloudflare + CDN configurado
2. ✅ Redis cache implementado
3. ✅ Imagens otimizadas (Cloudinary)
4. ✅ JavaScript bundle reduzido
5. ✅ N+1 queries eliminadas
6. ✅ Índices de banco criados
7. ✅ CSS otimizado
8. ✅ Fonts otimizadas
9. ✅ ISR implementado
10. ✅ GCP otimizado

### Ganhos de Negócio (Estimado):
- 📈 Conversão: +XX%
- 💰 Receita adicional: $XXX/mês
- 💸 Economia GCP: $XXX/mês
- ⏱️ Tempo de carregamento: -XX%

### Lições Aprendidas:
[Documentar aprendizados]

### Próximos Passos Recomendados:
[Backlog futuro]
```

---

**BOA SORTE! 🚀**

*Com dedicação e uso inteligente de IA, é possível atingir 90+ no PageSpeed em 10 dias!*

---

*Documento criado em: 17/10/2025*  
*Versão: 1.0 - Plano 10 Dias*
