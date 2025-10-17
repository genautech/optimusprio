# 🚀 Plano de Otimização - Lojinha Prio (lojinhaprio.yoobe.app)

## 📊 Análise Inicial do Problema

Baseado no PageSpeed Insights, o site apresenta problemas críticos de performance que impactam:
- **Experiência do usuário** (taxa de conversão)
- **SEO e ranqueamento** no Google
- **Taxa de abandono** (usuários saem antes de carregar)
- **Custos de infraestrutura** no GCP

**Stack Atual:**
- Frontend: Next.js + Commerce
- Backend: Spree Commerce (versão antiga)
- Database: PostgreSQL
- Infraestrutura: Google Cloud Platform

---

## 🎯 Estratégia Geral

### Princípios do Plano
✅ **Sem refatoração completa** - Melhorias incrementais  
✅ **Priorização baseada em impacto** - Quick wins primeiro  
✅ **Equipe enxuta** - 1 PM + 1 Developer  
✅ **Uso intensivo de IA** - Automação e análise inteligente  
✅ **Resultados mensuráveis** - Métricas claras de sucesso  

---

## 📋 FASES DO PROJETO

### **FASE 1: DIAGNÓSTICO PROFUNDO (Semana 1)**
*Objetivo: Identificar todos os gargalos com precisão*

#### Tarefas do Gerente de Projetos
- [ ] Configurar ferramentas de monitoramento
- [ ] Estabelecer baseline de métricas atuais
- [ ] Criar dashboard de acompanhamento
- [ ] Documentar problemas prioritários

#### Tarefas do Developer
- [ ] Auditar código frontend (Next.js)
- [ ] Auditar consultas de banco de dados
- [ ] Mapear assets pesados (imagens, JS, CSS)
- [ ] Identificar queries N+1 no Spree

#### 🤖 Ferramentas de IA Recomendadas

**1. Análise de Código e Performance**
- **GitHub Copilot** ($10/mês) - Sugestões de código otimizado em tempo real
- **Cursor IDE** ($20/mês) - Editor com IA para refatoração guiada
- **Claude Code + Cline** (Gratuito/Pago) - Análise de codebase completo

**2. Monitoramento e Diagnóstico**
- **Raygun** - Detecta problemas de performance em produção
- **Sentry Performance** - Identifica gargalos automaticamente
- **New Relic (Free Tier)** - APM com insights de IA

**3. Análise de Frontend**
- **DebugBear** - Monitora PageSpeed continuously
- **Lighthouse CI** - Automação de auditorias
- **WebPageTest** - Análise detalhada de carregamento

**4. Análise de Queries SQL**
- **pgAnalyze** - Otimização de queries PostgreSQL com IA
- **EverSQL** - Reescreve queries lentas automaticamente
- **pgMustard** - Explica e otimiza EXPLAIN plans

---

### **FASE 2: QUICK WINS (Semanas 2-3)**
*Objetivo: Ganhos rápidos com 20% do esforço gerando 80% do resultado*

#### 🎯 Prioridade CRÍTICA (Impacto Alto + Esforço Baixo)

**1. Otimização de Imagens (Impacto: ⭐⭐⭐⭐⭐)**
```bash
# Problemas comuns em e-commerce
- Imagens não comprimidas
- Formatos antigos (JPG/PNG ao invés de WebP/AVIF)
- Imagens maiores que o necessário
- Falta de lazy loading
```

**Ações:**
- [ ] Implementar Next.js Image Component em todas as imagens
- [ ] Converter imagens para WebP/AVIF automaticamente
- [ ] Implementar lazy loading em imagens de produtos
- [ ] Adicionar placeholders blur enquanto carrega

**🤖 Ferramentas de IA:**
- **Cloudinary AI** - Otimização automática de imagens
- **ImageKit.io** - CDN + otimização em tempo real
- **TinyPNG API** - Compressão automática via script

**Script de Automação:**
```javascript
// Usar IA para converter todas as imagens do projeto
// GitHub Copilot pode gerar scripts de conversão em massa
```

**2. Cache e CDN (Impacto: ⭐⭐⭐⭐⭐)**
```bash
# Configurações essenciais
- Cache de assets estáticos (JS, CSS, imagens)
- Cache de API responses do Spree
- Cache de páginas de produtos estáticas
```

**Ações:**
- [ ] Implementar Cloudflare (Free Tier) na frente do GCP
- [ ] Configurar cache headers corretos
- [ ] Implementar Redis para cache de sessões/queries
- [ ] Cache de páginas estáticas no Next.js (ISR)

**3. Redução de JavaScript (Impacto: ⭐⭐⭐⭐)**
```bash
# Next.js + Commerce geralmente tem muito JS
- Bundle size grande
- Bibliotecas não utilizadas
- Código duplicado
```

**Ações:**
- [ ] Analisar bundle com Next.js Bundle Analyzer
- [ ] Remover bibliotecas não utilizadas
- [ ] Implementar code splitting agressivo
- [ ] Lazy load de componentes pesados

**🤖 Ferramentas de IA:**
- **BundlePhobia** - Analisa tamanho de dependencies
- **Webpack Bundle Analyzer** - Visualiza bundle size
- **Claude/ChatGPT** - Análise de dependencies não utilizadas

**Script para análise:**
```bash
# Instalar analyzer
npm install @next/bundle-analyzer

# Analisar bundle
ANALYZE=true npm run build
```

**4. Otimização de Fonts (Impacto: ⭐⭐⭐)**
- [ ] Usar next/font para otimização automática
- [ ] Fazer self-host de Google Fonts
- [ ] Remover fonts não utilizadas
- [ ] Implementar font-display: swap

---

### **FASE 3: OTIMIZAÇÕES DE BACKEND (Semanas 4-5)**
*Objetivo: Acelerar APIs e queries do banco*

#### **1. Otimização de Queries PostgreSQL (Impacto: ⭐⭐⭐⭐⭐)**

**Problemas comuns no Spree:**
- Queries N+1 em listagem de produtos
- Falta de índices em campos filtrados
- Eager loading mal configurado
- Subconsultas ineficientes

**Ações:**
- [ ] Instalar Bullet gem para detectar N+1 queries
- [ ] Adicionar índices em campos de filtro/busca
- [ ] Implementar includes/joins apropriados
- [ ] Usar explain analyze em queries lentas

**🤖 Ferramentas de IA:**
```bash
# pgAnalyze - Conecta no PostgreSQL e sugere otimizações
# EverSQL - Cola o SQL e recebe versão otimizada
# GitHub Copilot - Sugestões de eager loading no código Ruby
```

**Script de diagnóstico:**
```ruby
# Adicionar ao Gemfile
gem 'bullet'
gem 'rack-mini-profiler'

# Detecta N+1 automaticamente em desenvolvimento
```

**2. Implementar Cache de API (Impacto: ⭐⭐⭐⭐)**
- [ ] Configurar Redis no GCP (Memorystore)
- [ ] Cache de listagem de produtos (15-30 min)
- [ ] Cache de produto individual (30-60 min)
- [ ] Cache de categorias e menus (24h)
- [ ] Invalidação inteligente ao atualizar produtos

**3. API Response Optimization (Impacto: ⭐⭐⭐)**
- [ ] Implementar compressão gzip/brotli
- [ ] Reduzir payload JSON (remover campos não usados)
- [ ] Implementar paginação eficiente
- [ ] Usar HTTP/2 push para recursos críticos

---

### **FASE 4: OTIMIZAÇÕES AVANÇADAS (Semanas 6-7)**
*Objetivo: Melhorias de infraestrutura e arquitetura*

#### **1. Database Optimization (Impacto: ⭐⭐⭐⭐)**
- [ ] Otimizar configuração do PostgreSQL para e-commerce
- [ ] Implementar connection pooling (PgBouncer)
- [ ] Particionar tabelas grandes (orders, products)
- [ ] Configurar vacuum automático
- [ ] Adicionar índices compostos estratégicos

**🤖 Usar AI:**
```bash
# pgtune.leopard.in.ua - Gera configuração otimizada
# pgMustard - Analisa explain plans e sugere índices
```

**2. GCP Infrastructure (Impacto: ⭐⭐⭐)**
- [ ] Implementar Cloud CDN para assets estáticos
- [ ] Configurar Load Balancer com health checks
- [ ] Otimizar tamanho das instâncias (right-sizing)
- [ ] Implementar auto-scaling básico
- [ ] Usar Cloud Storage para imagens estáticas

**3. Monitoramento Contínuo (Impacto: ⭐⭐⭐⭐)**
- [ ] Configurar Google Cloud Monitoring
- [ ] Alertas de performance degradada
- [ ] Dashboard de métricas vitais
- [ ] Synthetic monitoring (teste de carga simulado)

---

### **FASE 5: FRONTEND CRITICAL PATH (Semana 8)**
*Objetivo: Otimizar o carregamento inicial*

#### **1. Critical CSS e Above the Fold (Impacto: ⭐⭐⭐⭐)**
- [ ] Extrair CSS crítico e inlinizar
- [ ] Defer de CSS não crítico
- [ ] Remover CSS não utilizado
- [ ] Minificação agressiva

**🤖 Ferramentas:**
- **PurgeCSS** - Remove CSS não usado
- **Critical** (npm package) - Extrai CSS crítico
- **Critters** (já integrado no Next.js) - Inline critical CSS

**2. Preload e Prefetch Estratégico (Impacto: ⭐⭐⭐)**
```html
<!-- Preload recursos críticos -->
<link rel="preload" href="/fonts/main.woff2" as="font" crossorigin>
<link rel="preload" href="/api/products" as="fetch" crossorigin>

<!-- Prefetch páginas prováveis -->
<link rel="prefetch" href="/checkout">
```

**3. Rendering Strategy (Impacto: ⭐⭐⭐⭐)**
- [ ] ISR (Incremental Static Regeneration) para produtos
- [ ] SSG para páginas de categoria
- [ ] SSR apenas para páginas dinâmicas (carrinho, checkout)
- [ ] Implementar React Server Components (Next.js 13+)

---

## 🤖 PLANO DE USO DE IA - WORKFLOWS PRÁTICOS

### **Workflow 1: Diagnóstico Diário Automatizado**
```bash
# 1. GitHub Actions + Lighthouse CI
# Executa teste de performance a cada commit
# IA analisa diferenças e alerta regressões

# 2. Sentry Performance
# Monitora real user experience
# IA detecta padrões de lentidão

# 3. Raygun Real User Monitoring
# Mapeia jornada do usuário
# Identifica onde usuários abandonam por lentidão
```

### **Workflow 2: Code Review Assistido por IA**
```bash
# 1. Cursor IDE + Claude
# Developer escreve código
# IA sugere otimizações em tempo real

# 2. GitHub Copilot
# Autocomplete de código otimizado
# Sugestões de patterns performáticos

# 3. CodeRabbit
# Code review automático em PRs
# Detecta anti-patterns de performance
```

### **Workflow 3: Database Query Optimization**
```bash
# 1. pgAnalyze (conectar no GCP PostgreSQL)
# Monitora queries lentas continuamente
# IA sugere índices automaticamente

# 2. Developer copia query lenta
# 3. Cola no EverSQL.com
# 4. IA reescreve query otimizada
# 5. Testa e implementa
```

### **Workflow 4: Image Optimization Pipeline**
```bash
# 1. Script de upload -> Cloudinary AI
# 2. Cloudinary otimiza automaticamente:
#    - Formato adequado (WebP/AVIF)
#    - Compressão inteligente
#    - Resize para diferentes viewports
# 3. CDN global entrega versão otimizada
# 4. Lazy loading no frontend
```

---

## 📊 ESTRUTURA DE TRABALHO - PM + DEV

### **Gerente de Projetos - Responsabilidades**

#### **Diariamente:**
- [ ] Revisar dashboard de performance (PageSpeed, Core Web Vitals)
- [ ] Priorizar tasks baseado em impacto vs esforço
- [ ] Remover blockers do developer
- [ ] Documentar progresso e decisões

#### **Semanalmente:**
- [ ] Sprint planning com foco em métricas
- [ ] Review de resultados da semana anterior
- [ ] Ajustar prioridades baseado em dados
- [ ] Reportar stakeholders (se houver)

#### **Ferramentas do PM:**
- **Linear.app** ou **ClickUp** - Gestão de tasks com IA
- **Notion AI** - Documentação e summaries automáticos
- **Google Sheets + GPT** - Análise de dados de performance
- **Slack + Claude** - Suporte para decisões rápidas

---

### **Developer - Responsabilidades**

#### **Diariamente:**
- [ ] Implementar tasks priorizadas pelo PM
- [ ] Usar Cursor/Copilot para acelerar desenvolvimento
- [ ] Testar impacto das mudanças localmente
- [ ] Commitar com métricas antes/depois

#### **Semanalmente:**
- [ ] Code review assistido por IA
- [ ] Deploy de otimizações em staging
- [ ] Validar melhorias no PageSpeed
- [ ] Documentar learnings e best practices

#### **Ferramentas do Developer:**
- **Cursor IDE** - Editor com IA integrada
- **GitHub Copilot** - Autocomplete inteligente
- **Claude Code (Cline)** - Refatoração assistida
- **Lighthouse CI** - Validação automática
- **pgAnalyze** - Otimização de queries

---

## 🎯 MÉTRICAS DE SUCESSO

### **Baseline Atual (Medir antes de começar)**
```
Performance Score: _____ / 100
Largest Contentful Paint: _____ s
First Input Delay: _____ ms
Cumulative Layout Shift: _____
Total Blocking Time: _____ ms
Time to Interactive: _____ s
```

### **Metas por Fase**

| Fase | Performance Score | LCP | FID | CLS | TBT |
|------|------------------|-----|-----|-----|-----|
| **Baseline** | ? | ? | ? | ? | ? |
| **Fase 2 (Quick Wins)** | +20-30 pts | -30% | -30% | -50% | -40% |
| **Fase 3 (Backend)** | +10-15 pts | -20% | -20% | - | -30% |
| **Fase 4 (Advanced)** | +10 pts | -15% | -10% | - | -20% |
| **FINAL** | **90+** | **<2.5s** | **<100ms** | **<0.1** | **<200ms** |

### **Métricas de Negócio**
- [ ] Taxa de conversão (baseline → +X%)
- [ ] Taxa de abandono (baseline → -X%)
- [ ] Tempo médio na página (baseline → +X%)
- [ ] Bounce rate (baseline → -X%)
- [ ] Custo de infraestrutura GCP (baseline → -X%)

---

## 💰 INVESTIMENTO EM FERRAMENTAS

### **Ferramentas Essenciais (Total: ~$150-200/mês)**

| Ferramenta | Custo Mensal | Uso | ROI |
|------------|--------------|-----|-----|
| **Cursor IDE** | $20 | Developer | ⭐⭐⭐⭐⭐ |
| **GitHub Copilot** | $10 | Developer | ⭐⭐⭐⭐⭐ |
| **Cloudflare Pro** | $20 | CDN + Cache | ⭐⭐⭐⭐⭐ |
| **Sentry Performance** | $29 | Monitoring | ⭐⭐⭐⭐ |
| **pgAnalyze** | $49 | Database | ⭐⭐⭐⭐⭐ |
| **Cloudinary** | $89 | Imagens | ⭐⭐⭐⭐⭐ |
| **DebugBear** | $30 | PageSpeed CI | ⭐⭐⭐ |
| **Linear.app** | $8 | PM | ⭐⭐⭐⭐ |

### **Ferramentas Gratuitas**
- Claude Code (Cline) - Gratuito com API key
- New Relic Free Tier - APM básico
- Lighthouse CI - Open source
- Google Cloud Monitoring - Incluído no GCP
- PageSpeed Insights - Google gratuito

---

## 📅 CRONOGRAMA DETALHADO (8 SEMANAS)

### **Semana 1: Diagnóstico**
```
Segunda: Setup de ferramentas (Cursor, Copilot, Sentry)
Terça: Análise de código frontend com Claude
Quarta: Análise de queries com pgAnalyze
Quinta: Mapeamento de assets pesados
Sexta: Documentação de problemas + priorização
```

### **Semana 2-3: Quick Wins**
```
Semana 2:
- Segunda-Quarta: Otimização de imagens (Cloudinary)
- Quinta-Sexta: Implementar Cloudflare + cache

Semana 3:
- Segunda-Quarta: Redução de JavaScript
- Quinta-Sexta: Otimização de fonts + testing
```

### **Semana 4-5: Backend**
```
Semana 4:
- Segunda-Quarta: Otimização de queries + índices
- Quinta-Sexta: Setup Redis + cache API

Semana 5:
- Segunda-Quarta: API response optimization
- Quinta-Sexta: Testing + tuning
```

### **Semana 6-7: Advanced**
```
Semana 6:
- Segunda-Quarta: Database tuning avançado
- Quinta-Sexta: GCP infrastructure optimization

Semana 7:
- Segunda-Quarta: Monitoramento contínuo
- Quinta-Sexta: Load testing + ajustes
```

### **Semana 8: Frontend Critical**
```
Segunda-Quarta: Critical CSS + rendering strategy
Quinta-Sexta: Testes finais + documentação
```

---

## 🚨 RISCOS E MITIGAÇÕES

### **Risco 1: Spree Commerce Versão Antiga**
**Problema:** Gem desatualizada pode ter limitações  
**Mitigação:**
- Focar em otimizações de infraestrutura e frontend
- Cache agressivo para compensar backend lento
- Considerar upgrade incremental do Spree em paralelo

### **Risco 2: Mudanças Causarem Bugs**
**Problema:** Otimizações podem quebrar funcionalidades  
**Mitigação:**
- Ambiente de staging para testes
- Deploy incremental (feature flags)
- Rollback plan para cada mudança
- Monitoring de erros com Sentry

### **Risco 3: Equipe Pequena (1 PM + 1 Dev)**
**Problema:** Velocidade limitada  
**Mitigação:**
- IA para multiplicar produtividade
- Foco em high-impact tasks
- Automação máxima de testes
- Scripts para tarefas repetitivas

### **Risco 4: Budget Limitado**
**Problema:** Ferramentas custam ~$200/mês  
**Mitigação:**
- ROI de ferramentas pagas é comprovado
- Economia em custos GCP (instâncias menores)
- Aumento de conversão paga investimento
- Usar free tiers quando possível

---

## 🎓 LEARNING RESOURCES - AI-POWERED

### **Para o Developer**
1. **Cursor IDE Docs** - cursor.sh/docs
2. **web.dev/fast** - Guia oficial Google (usar com ChatGPT)
3. **Next.js Performance Docs** - nextjs.org/docs/advanced-features/measuring-performance
4. **Spree Guides** - guides.spreecommerce.org (usar Claude para análise)

### **Para o PM**
1. **Core Web Vitals** - web.dev/vitals
2. **Impact vs Effort Matrix** - usar Notion AI para priorização
3. **Performance Budget Calculator** - usar ChatGPT

### **Uso de IA para Learning:**
```bash
# Exemplo de prompt para Claude/ChatGPT:
"Analise este código Next.js e sugira 5 otimizações 
de performance específicas com impacto alto e esforço baixo.
Forneça código refatorado."
```

---

## 📈 DASHBOARD DE ACOMPANHAMENTO

### **Métricas Semanais (Google Sheets + ChatGPT)**

```
| Semana | PageSpeed | LCP | FID | CLS | TBT | Tasks Concluídas | Bloqueios |
|--------|-----------|-----|-----|-----|-----|------------------|-----------|
| 1      |           |     |     |     |     |                  |           |
| 2      |           |     |     |     |     |                  |           |
| 3      |           |     |     |     |     |                  |           |
...
```

**Usar ChatGPT para:**
- Gerar gráficos de evolução
- Analisar tendências
- Sugerir ajustes de prioridade
- Calcular projeção de metas

---

## ✅ CHECKLIST DE IMPLEMENTAÇÃO

### **Setup Inicial**
- [ ] Contratar ferramentas essenciais (Cursor, Copilot, Cloudflare)
- [ ] Configurar Sentry + pgAnalyze
- [ ] Setup ambiente de staging no GCP
- [ ] Medir baseline de métricas
- [ ] Criar repositório de documentação (Notion)

### **Governance**
- [ ] Daily standup de 15min (PM + Dev)
- [ ] Review semanal de métricas
- [ ] Documentação de todas as mudanças
- [ ] Commits com benchmarks antes/depois
- [ ] Testing em staging antes de produção

### **Entrega Contínua**
- [ ] Deploy incremental (1-2x por semana)
- [ ] Feature flags para rollback rápido
- [ ] Monitoring de erros pós-deploy
- [ ] A/B testing de mudanças críticas

---

## 🎯 QUICK WINS - LISTA PRIORIZADA

### **TOP 10 Ações de Maior Impacto**

1. **Cloudflare Free + Cache** (1-2h) → ⚡ +15-20 pts PageSpeed
2. **Next.js Image Component** (4-6h) → ⚡ +10-15 pts
3. **Cloudinary para Imagens** (2-3h setup) → ⚡ +10 pts
4. **Redis Cache API** (4-6h) → ⚡ +8-12 pts
5. **Bundle Analyzer + Code Splitting** (4-6h) → ⚡ +8-10 pts
6. **Índices no PostgreSQL** (2-4h) → ⚡ +5-10 pts
7. **Critical CSS Inline** (3-4h) → ⚡ +5-8 pts
8. **Font Optimization** (2-3h) → ⚡ +3-5 pts
9. **ISR para Produtos** (6-8h) → ⚡ +5-8 pts
10. **Compressão Brotli** (1-2h) → ⚡ +3-5 pts

**Estimativa de ganho total:** +70-110 pontos no PageSpeed Score

---

## 🤖 PROMPTS ÚTEIS PARA IA

### **Para Cursor/Claude Code:**
```
"Analise este componente React e otimize para performance:
- Implementar lazy loading
- Memoization onde necessário
- Reduzir re-renders
- Code splitting
Mostre antes e depois com métricas estimadas."
```

### **Para pgAnalyze/ChatGPT:**
```
"Esta query está lenta:
[colar SQL]

Contexto: Tabela products tem 50k registros, tabela variants tem 200k.
Sugira otimizações: índices, reescrita, caching."
```

### **Para GitHub Copilot:**
```javascript
// Prompt no código:
// Otimizar este fetch para cache com React Query e revalidação
// Implementar loading state e error boundary
// Adicionar retry logic
```

---

## 💡 DICAS FINAIS

### **Para o PM:**
1. **Celebre pequenas vitórias** - +10 pts no PageSpeed é vitória
2. **Documente tudo** - Use Notion AI para organizar
3. **Comunique com dados** - Gráficos são mais efetivos
4. **Seja pragmático** - Nem tudo precisa ser perfeito
5. **Use IA para decisões** - Cole dados no ChatGPT e peça análise

### **Para o Developer:**
1. **Meça antes e depois** - Sempre tenha baseline
2. **Use IA sem medo** - Cursor/Copilot aceleram muito
3. **Teste em produção-like** - Staging deve ser similar a prod
4. **Automatize tudo** - Scripts para tarefas repetitivas
5. **Aprenda com IA** - Use Claude para explicar conceitos

### **Para Ambos:**
1. **Foco em impacto** - 20% do esforço = 80% resultado
2. **Iteração rápida** - Deploy pequeno e frequente
3. **Monitoramento contínuo** - Não otimize no escuro
4. **Feedback loop curto** - Daily checks de métricas
5. **Mantenha simples** - Soluções complexas são difíceis de manter

---

## 📞 SUPORTE E RECURSOS

### **Comunidades para Ajuda:**
- **Next.js Discord** - Discord de Next.js (usar com IA para buscar soluções)
- **Spree Slack** - Comunidade Spree Commerce
- **Stack Overflow** - Perguntas específicas (usar Claude para formular)

### **Consultoria AI-Powered:**
- **ChatGPT Plus** - $20/mês para consultas ilimitadas
- **Claude Pro** - $20/mês para análises profundas
- **Perplexity Pro** - $20/mês para research rápido

---

## 🎉 CONCLUSÃO

Este plano foi desenhado para ser:
- ✅ **Pragmático** - Sem refatoração completa
- ✅ **Ágil** - Resultados rápidos em 8 semanas
- ✅ **AI-First** - Uso intensivo de IA para acelerar
- ✅ **Mensurável** - Métricas claras de sucesso
- ✅ **Escalável** - Com equipe de 1 PM + 1 Dev

**Expectativa realista:**
- **Semana 2-3:** Primeiros ganhos visíveis (+20-30 pts)
- **Semana 5:** Melhorias significativas (+40-50 pts total)
- **Semana 8:** Meta de 90+ no PageSpeed Score

**Lembre-se:** Performance é jornada contínua, não destino. Após as 8 semanas, mantenha monitoramento e otimizações incrementais.

---

## 📋 PRÓXIMOS PASSOS IMEDIATOS

1. **Hoje:** Contratar Cursor IDE + GitHub Copilot
2. **Amanhã:** Medir baseline completo de métricas
3. **Esta semana:** Implementar Cloudflare + Cloudinary (quick wins)
4. **Próxima semana:** Começar Fase 2 do plano

**Boa sorte! 🚀**

---

*Documento criado em: 17/10/2025*  
*Última atualização: 17/10/2025*  
*Versão: 1.0*
