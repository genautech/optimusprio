# 🛠️ Scripts e Automações - Lojinha Prio

## 📊 Scripts de Diagnóstico

### 1. Script de Análise de Bundle (Next.js)

```bash
# package.json - adicionar script
{
  "scripts": {
    "analyze": "ANALYZE=true next build",
    "lighthouse": "lighthouse https://lojinhaprio.yoobe.app --output html --output-path ./reports/lighthouse-$(date +%Y%m%d-%H%M%S).html",
    "perf-check": "node scripts/performance-check.js"
  }
}
```

### 2. Script de Monitoramento de Performance

```javascript
// scripts/performance-check.js
const https = require('https');
const fs = require('fs');

const PAGESPEED_API_KEY = 'YOUR_API_KEY'; // Pegar em: https://developers.google.com/speed/docs/insights/v5/get-started
const URL = 'https://lojinhaprio.yoobe.app';

async function checkPerformance() {
  const apiUrl = `https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=${encodeURIComponent(URL)}&strategy=desktop&category=performance`;
  
  return new Promise((resolve, reject) => {
    https.get(apiUrl, (res) => {
      let data = '';
      res.on('data', (chunk) => data += chunk);
      res.on('end', () => {
        const result = JSON.parse(data);
        const metrics = result.lighthouseResult.audits;
        
        const report = {
          timestamp: new Date().toISOString(),
          score: result.lighthouseResult.categories.performance.score * 100,
          metrics: {
            fcp: metrics['first-contentful-paint'].displayValue,
            lcp: metrics['largest-contentful-paint'].displayValue,
            tbt: metrics['total-blocking-time'].displayValue,
            cls: metrics['cumulative-layout-shift'].displayValue,
            tti: metrics['interactive'].displayValue
          }
        };
        
        console.log('📊 Performance Report:');
        console.log(`Score: ${report.score}/100`);
        console.log(`LCP: ${report.metrics.lcp}`);
        console.log(`TBT: ${report.metrics.tbt}`);
        console.log(`CLS: ${report.metrics.cls}`);
        
        // Salvar histórico
        const history = JSON.parse(fs.readFileSync('./perf-history.json', 'utf8') || '[]');
        history.push(report);
        fs.writeFileSync('./perf-history.json', JSON.stringify(history, null, 2));
        
        resolve(report);
      });
    }).on('error', reject);
  });
}

checkPerformance().catch(console.error);
```

### 3. Script de Análise de Imagens

```javascript
// scripts/analyze-images.js
const fs = require('fs');
const path = require('path');
const https = require('https');

function analyzeImages(dir = './public') {
  const images = [];
  
  function scanDir(directory) {
    const files = fs.readdirSync(directory);
    
    files.forEach(file => {
      const filePath = path.join(directory, file);
      const stat = fs.statSync(filePath);
      
      if (stat.isDirectory()) {
        scanDir(filePath);
      } else if (/\.(jpg|jpeg|png|gif|webp|avif)$/i.test(file)) {
        const sizeKB = (stat.size / 1024).toFixed(2);
        images.push({
          path: filePath,
          size: sizeKB + ' KB',
          sizebytes: stat.size,
          needsOptimization: stat.size > 100000 // > 100KB
        });
      }
    });
  }
  
  scanDir(dir);
  
  // Relatório
  console.log('\n📸 Image Analysis Report:\n');
  console.log(`Total images: ${images.length}`);
  
  const needsOpt = images.filter(img => img.needsOptimization);
  console.log(`Images needing optimization: ${needsOpt.length}`);
  
  const totalSize = images.reduce((sum, img) => sum + img.sizebytes, 0);
  console.log(`Total size: ${(totalSize / 1024 / 1024).toFixed(2)} MB`);
  
  if (needsOpt.length > 0) {
    console.log('\n⚠️ Large images (>100KB):');
    needsOpt.forEach(img => {
      console.log(`  ${img.path} - ${img.size}`);
    });
  }
  
  // Salvar relatório
  fs.writeFileSync('./reports/image-analysis.json', JSON.stringify(images, null, 2));
  console.log('\n✅ Report saved to ./reports/image-analysis.json');
}

analyzeImages();
```

---

## 🔧 Scripts de Otimização

### 4. Script de Conversão de Imagens para WebP

```javascript
// scripts/convert-to-webp.js
// Requer: npm install sharp
const sharp = require('sharp');
const fs = require('fs');
const path = require('path');

async function convertToWebP(inputDir = './public/images', quality = 80) {
  const files = fs.readdirSync(inputDir);
  
  for (const file of files) {
    const filePath = path.join(inputDir, file);
    const stat = fs.statSync(filePath);
    
    if (stat.isDirectory()) {
      await convertToWebP(filePath, quality);
      continue;
    }
    
    if (/\.(jpg|jpeg|png)$/i.test(file)) {
      const outputPath = filePath.replace(/\.(jpg|jpeg|png)$/i, '.webp');
      
      try {
        await sharp(filePath)
          .webp({ quality })
          .toFile(outputPath);
        
        const originalSize = stat.size;
        const newSize = fs.statSync(outputPath).size;
        const saved = ((1 - newSize / originalSize) * 100).toFixed(1);
        
        console.log(`✅ ${file} → ${path.basename(outputPath)} (${saved}% smaller)`);
      } catch (err) {
        console.error(`❌ Error converting ${file}:`, err.message);
      }
    }
  }
}

convertToWebP();
```

### 5. Script de Limpeza de Node Modules

```bash
# scripts/clean-deps.sh
#!/bin/bash

echo "🧹 Analyzing dependencies..."

# Instalar depcheck se não existir
npm list -g depcheck || npm install -g depcheck

# Analisar dependencies não utilizadas
depcheck

echo ""
echo "📦 Bundle size analysis:"
npm install -g cost-of-modules
cost-of-modules --less --no-install

echo ""
echo "💡 Consider removing unused dependencies to reduce bundle size"
```

### 6. Script de Cache Warming (Spree)

```ruby
# lib/tasks/cache_warm.rake
namespace :cache do
  desc "Warm up cache for products and categories"
  task warm: :environment do
    puts "🔥 Warming up cache..."
    
    # Cache all products
    Spree::Product.active.find_each do |product|
      Rails.cache.fetch("product_#{product.id}", expires_in: 1.hour) do
        product.as_json(include: [:variants, :images])
      end
      print "."
    end
    
    # Cache categories
    Spree::Taxon.roots.find_each do |taxon|
      Rails.cache.fetch("taxon_#{taxon.id}", expires_in: 24.hours) do
        taxon.as_json(include: :children)
      end
      print "."
    end
    
    puts "\n✅ Cache warmed successfully!"
  end
end
```

---

## 📋 Templates de Configuração

### 7. Cloudflare Page Rules (Configuração Recomendada)

```yaml
# Configurar em: Cloudflare Dashboard > Page Rules

# Rule 1: Cache static assets
URL: lojinhaprio.yoobe.app/static/*
Settings:
  - Cache Level: Cache Everything
  - Edge Cache TTL: 1 month
  - Browser Cache TTL: 1 week

# Rule 2: Cache product images
URL: lojinhaprio.yoobe.app/images/*
Settings:
  - Cache Level: Cache Everything
  - Edge Cache TTL: 1 month
  - Browser Cache TTL: 1 week
  - Polish: Lossless (ou Lossy para mais compressão)

# Rule 3: API Caching
URL: lojinhaprio.yoobe.app/api/products*
Settings:
  - Cache Level: Cache Everything
  - Edge Cache TTL: 10 minutes
  - Bypass Cache on Cookie: session_id

# Rule 4: Dynamic pages (checkout, cart)
URL: lojinhaprio.yoobe.app/checkout*
Settings:
  - Cache Level: Bypass
  - Security Level: High
```

### 8. Redis Configuration (GCP Memorystore)

```yaml
# redis.conf (otimizado para e-commerce)

maxmemory 2gb
maxmemory-policy allkeys-lru

# Performance
tcp-backlog 511
timeout 300
tcp-keepalive 60

# Persistence (desligar se não necessário)
save ""
appendonly no

# Optimizations
activedefrag yes
lazyfree-lazy-eviction yes
lazyfree-lazy-expire yes
```

### 9. PostgreSQL Configuration (GCP Cloud SQL)

```sql
-- postgresql.conf recommendations para e-commerce

-- Connections
max_connections = 200

-- Memory
shared_buffers = 2GB                    -- 25% da RAM
effective_cache_size = 6GB              -- 75% da RAM
maintenance_work_mem = 512MB
work_mem = 10MB

-- Query Planning
random_page_cost = 1.1                  -- For SSD
effective_io_concurrency = 200

-- WAL
wal_buffers = 16MB
min_wal_size = 1GB
max_wal_size = 4GB

-- Query Performance
default_statistics_target = 100
checkpoint_completion_target = 0.9
```

### 10. Next.js Configuration (next.config.js)

```javascript
// next.config.js - Otimizado para performance

const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

module.exports = withBundleAnalyzer({
  // Compressão
  compress: true,
  
  // Images optimization
  images: {
    domains: ['res.cloudinary.com', 'lojinhaprio.yoobe.app'],
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
  
  // Webpack optimization
  webpack: (config, { isServer }) => {
    // Code splitting
    config.optimization = {
      ...config.optimization,
      splitChunks: {
        chunks: 'all',
        cacheGroups: {
          default: false,
          vendors: false,
          commons: {
            name: 'commons',
            chunks: 'all',
            minChunks: 2,
          },
          react: {
            name: 'react',
            chunks: 'all',
            test: /[\\/]node_modules[\\/](react|react-dom|scheduler)[\\/]/,
          },
          lib: {
            test: /[\\/]node_modules[\\/]/,
            name(module) {
              const packageName = module.context.match(/[\\/]node_modules[\\/](.*?)([\\/]|$)/)[1];
              return `npm.${packageName.replace('@', '')}`;
            },
          },
        },
      },
    };
    
    return config;
  },
  
  // Headers para cache
  async headers() {
    return [
      {
        source: '/static/:path*',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on'
          },
          {
            key: 'X-Frame-Options',
            value: 'SAMEORIGIN'
          },
        ],
      },
    ];
  },
  
  // React strict mode
  reactStrictMode: true,
  
  // SWC minification (mais rápido)
  swcMinify: true,
  
  // ISR
  experimental: {
    optimizeCss: true,
    optimizePackageImports: ['lucide-react', '@heroicons/react'],
  },
});
```

---

## 🤖 Automações com GitHub Actions

### 11. Lighthouse CI Workflow

```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI

on:
  push:
    branches: [main, staging]
  pull_request:
    branches: [main]

jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Run Lighthouse
        uses: treosh/lighthouse-ci-action@v9
        with:
          urls: |
            https://lojinhaprio.yoobe.app
            https://lojinhaprio.yoobe.app/products
          uploadArtifacts: true
          temporaryPublicStorage: true
      
      - name: Comment PR
        uses: actions/github-script@v6
        if: github.event_name == 'pull_request'
        with:
          script: |
            const results = ${{ steps.lighthouse.outputs.manifest }}
            // Comentar resultados no PR
```

### 12. Bundle Size Check

```yaml
# .github/workflows/bundle-size.yml
name: Bundle Size Check

on:
  pull_request:
    branches: [main]

jobs:
  check-size:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Check bundle size
        uses: andresz1/size-limit-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          skip_step: install
```

---

## 📊 Dashboard de Métricas (Google Sheets + Apps Script)

### 13. Apps Script para Coleta Automática

```javascript
// Google Sheets > Extensions > Apps Script

function collectPerformanceMetrics() {
  const PAGESPEED_API_KEY = 'YOUR_KEY';
  const URL = 'https://lojinhaprio.yoobe.app';
  const SHEET_NAME = 'Performance Metrics';
  
  // Buscar dados do PageSpeed
  const apiUrl = `https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=${encodeURIComponent(URL)}&strategy=desktop`;
  const response = UrlFetchApp.fetch(apiUrl);
  const data = JSON.parse(response.getContentText());
  
  // Extrair métricas
  const score = data.lighthouseResult.categories.performance.score * 100;
  const audits = data.lighthouseResult.audits;
  
  // Adicionar na planilha
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
  const timestamp = new Date();
  
  sheet.appendRow([
    timestamp,
    score,
    audits['first-contentful-paint'].numericValue,
    audits['largest-contentful-paint'].numericValue,
    audits['total-blocking-time'].numericValue,
    audits['cumulative-layout-shift'].numericValue,
    audits['speed-index'].numericValue
  ]);
  
  Logger.log('Metrics collected successfully');
}

// Configurar trigger para executar diariamente
function createTrigger() {
  ScriptApp.newTrigger('collectPerformanceMetrics')
    .timeBased()
    .everyDays(1)
    .atHour(9)
    .create();
}
```

---

## 🔍 Queries SQL Úteis

### 14. Análise de Queries Lentas

```sql
-- Ativar logging de queries lentas
ALTER SYSTEM SET log_min_duration_statement = 1000; -- 1 segundo
ALTER SYSTEM SET log_line_prefix = '%t [%p]: [%l-1] user=%u,db=%d,app=%a,client=%h ';
SELECT pg_reload_conf();

-- Top 10 queries mais lentas
SELECT 
  query,
  calls,
  total_time,
  mean_time,
  max_time
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;

-- Índices não utilizados (remover para liberar espaço)
SELECT
  schemaname,
  tablename,
  indexname,
  pg_size_pretty(pg_relation_size(indexrelid)) as index_size
FROM pg_stat_user_indexes
WHERE idx_scan = 0
  AND schemaname NOT IN ('pg_catalog', 'information_schema')
ORDER BY pg_relation_size(indexrelid) DESC;

-- Tabelas que precisam de VACUUM
SELECT
  schemaname,
  tablename,
  n_dead_tup,
  n_live_tup,
  round(n_dead_tup * 100.0 / NULLIF(n_live_tup + n_dead_tup, 0), 2) as dead_percentage
FROM pg_stat_user_tables
WHERE n_dead_tup > 1000
ORDER BY dead_percentage DESC;
```

### 15. Criação de Índices Estratégicos (Spree)

```sql
-- Índices para performance em e-commerce

-- Products
CREATE INDEX CONCURRENTLY idx_products_available_on 
  ON spree_products(available_on) WHERE available_on IS NOT NULL;

CREATE INDEX CONCURRENTLY idx_products_name_trgm 
  ON spree_products USING gin(name gin_trgm_ops);

-- Variants
CREATE INDEX CONCURRENTLY idx_variants_product_id_is_master 
  ON spree_variants(product_id, is_master);

CREATE INDEX CONCURRENTLY idx_variants_sku 
  ON spree_variants(sku) WHERE sku IS NOT NULL;

-- Orders
CREATE INDEX CONCURRENTLY idx_orders_completed_at 
  ON spree_orders(completed_at) WHERE completed_at IS NOT NULL;

CREATE INDEX CONCURRENTLY idx_orders_user_state 
  ON spree_orders(user_id, state);

-- Line Items
CREATE INDEX CONCURRENTLY idx_line_items_order_variant 
  ON spree_line_items(order_id, variant_id);

-- Taxons (Categories)
CREATE INDEX CONCURRENTLY idx_taxons_parent_lft_rgt 
  ON spree_taxons(parent_id, lft, rgt);
```

---

## 🎨 Component Templates (Next.js)

### 16. Image Component Otimizado

```jsx
// components/OptimizedImage.jsx
import Image from 'next/image';
import { useState } from 'react';

export default function OptimizedImage({ 
  src, 
  alt, 
  width, 
  height, 
  priority = false,
  className = '' 
}) {
  const [isLoading, setIsLoading] = useState(true);
  
  return (
    <div className={`relative overflow-hidden ${className}`}>
      {isLoading && (
        <div className="absolute inset-0 bg-gray-200 animate-pulse" />
      )}
      <Image
        src={src}
        alt={alt}
        width={width}
        height={height}
        priority={priority}
        quality={85}
        placeholder="blur"
        blurDataURL="data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9IiNlZWUiLz48L3N2Zz4="
        onLoadingComplete={() => setIsLoading(false)}
        className={`transition-opacity duration-300 ${
          isLoading ? 'opacity-0' : 'opacity-100'
        }`}
      />
    </div>
  );
}
```

### 17. Product Card com Performance

```jsx
// components/ProductCard.jsx
import dynamic from 'next/dynamic';
import { memo } from 'react';
import OptimizedImage from './OptimizedImage';

// Lazy load do botão de adicionar ao carrinho
const AddToCart = dynamic(() => import('./AddToCart'), {
  loading: () => <button disabled>Loading...</button>,
  ssr: false
});

const ProductCard = memo(({ product }) => {
  return (
    <article className="product-card">
      <OptimizedImage
        src={product.image_url}
        alt={product.name}
        width={300}
        height={300}
        className="product-image"
      />
      <h3>{product.name}</h3>
      <p className="price">${product.price}</p>
      <AddToCart productId={product.id} />
    </article>
  );
});

ProductCard.displayName = 'ProductCard';

export default ProductCard;
```

---

## 📝 Checklist de Deploy

### 18. Pre-Deploy Checklist

```markdown
# Pre-Deploy Checklist

## Performance
- [ ] Lighthouse score > 90
- [ ] Bundle size analisado e otimizado
- [ ] Images comprimidas (WebP/AVIF)
- [ ] Cache headers configurados
- [ ] Redis funcionando

## Testing
- [ ] Testes em staging passando
- [ ] Cross-browser testing (Chrome, Firefox, Safari)
- [ ] Mobile responsiveness verificado
- [ ] Performance testada em 3G throttling

## Monitoring
- [ ] Sentry configurado
- [ ] Error tracking ativo
- [ ] Performance monitoring ativo
- [ ] Alerts configurados

## Security
- [ ] Headers de segurança configurados
- [ ] HTTPS forçado
- [ ] Rate limiting ativo
- [ ] DDoS protection (Cloudflare)

## Rollback Plan
- [ ] Backup do banco de dados
- [ ] Tag de versão criada
- [ ] Procedimento de rollback documentado
- [ ] On-call disponível por 2h pós-deploy
```

---

## 🎓 AI Prompts Práticos

### 19. Prompts para Cursor/Claude

```
# Análise de Performance
"Analise este código e identifique:
1. Problemas de performance (re-renders, memory leaks)
2. Oportunidades de lazy loading
3. Possibilidade de memoization
4. Code splitting opportunities
Forneça código refatorado com comentários."

# Otimização de Query
"Esta query Rails está lenta:
[código]
Contexto: [explicar relacionamentos]
Sugira:
1. Eager loading apropriado
2. Índices necessários
3. Alternativas de cache
4. Reescrita se necessário"

# Component Optimization
"Otimize este componente React para performance:
[código]
Implemente:
- useMemo/useCallback onde apropriado
- React.memo se benéfico
- Lazy loading de child components
- Error boundaries"
```

---

**Fim dos Scripts e Automações**

Este documento contém scripts prontos para uso. Adapte conforme necessário para o seu ambiente específico.
