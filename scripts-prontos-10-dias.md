# 🛠️ Scripts Prontos para Uso - 10 Dias

## 📋 ÍNDICE RÁPIDO
- [Dia 1: Setup e Diagnóstico](#dia-1-scripts)
- [Dia 2: Cloudflare e Redis](#dia-2-scripts)
- [Dia 3-4: Imagens](#dia-3-4-scripts)
- [Dia 5: JavaScript](#dia-5-scripts)
- [Dia 6-7: Database](#dia-6-7-scripts)
- [Dia 8: CSS e Rendering](#dia-8-scripts)
- [Scripts de Monitoramento](#scripts-monitoramento)

---

## 🗓️ DIA 1 SCRIPTS

### 1.1 Script de Medição de Baseline

```bash
#!/bin/bash
# baseline-check.sh

echo "🔍 Medindo baseline de performance..."
echo "=================================="

# Criar pasta de reports
mkdir -p reports

# Data atual
DATE=$(date +%Y%m%d-%H%M%S)

# PageSpeed Insights (Desktop)
echo "📊 Testando Desktop..."
curl -s "https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=https://lojinhaprio.yoobe.app&strategy=desktop" > reports/baseline-desktop-$DATE.json

# PageSpeed Insights (Mobile)
echo "📱 Testando Mobile..."
curl -s "https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=https://lojinhaprio.yoobe.app&strategy=mobile" > reports/baseline-mobile-$DATE.json

# Extrair scores
echo ""
echo "✅ Resultados:"
echo "Desktop Score:" $(cat reports/baseline-desktop-$DATE.json | jq '.lighthouseResult.categories.performance.score * 100')
echo "Mobile Score:" $(cat reports/baseline-mobile-$DATE.json | jq '.lighthouseResult.categories.performance.score * 100')

echo ""
echo "📁 Relatórios salvos em: reports/"
```

**Como usar:**
```bash
chmod +x baseline-check.sh
./baseline-check.sh
```

---

### 1.2 Script de Análise de Imagens

```javascript
// analyze-images.js
const fs = require('fs');
const path = require('path');

function analyzeImages(directory = './public') {
  console.log('🔍 Analisando imagens...\n');
  
  const results = {
    total: 0,
    totalSize: 0,
    needsOptimization: [],
    byType: {}
  };

  function scanDirectory(dir) {
    const files = fs.readdirSync(dir);
    
    files.forEach(file => {
      const filePath = path.join(dir, file);
      const stat = fs.statSync(filePath);
      
      if (stat.isDirectory()) {
        scanDirectory(filePath);
      } else if (/\.(jpg|jpeg|png|gif|webp|avif|svg)$/i.test(file)) {
        results.total++;
        results.totalSize += stat.size;
        
        const ext = path.extname(file).toLowerCase();
        results.byType[ext] = (results.byType[ext] || 0) + 1;
        
        // Imagens > 100KB precisam otimização
        if (stat.size > 100000) {
          results.needsOptimization.push({
            path: filePath,
            size: stat.size,
            sizeKB: (stat.size / 1024).toFixed(2)
          });
        }
      }
    });
  }

  scanDirectory(directory);

  // Relatório
  console.log('📊 RELATÓRIO DE IMAGENS\n');
  console.log(`Total de imagens: ${results.total}`);
  console.log(`Tamanho total: ${(results.totalSize / 1024 / 1024).toFixed(2)} MB`);
  console.log(`\nPor tipo:`);
  Object.entries(results.byType).forEach(([ext, count]) => {
    console.log(`  ${ext}: ${count}`);
  });
  
  console.log(`\n⚠️  Imagens que precisam otimização (> 100KB): ${results.needsOptimization.length}`);
  
  if (results.needsOptimization.length > 0) {
    console.log('\n🔴 Top 10 maiores:');
    results.needsOptimization
      .sort((a, b) => b.size - a.size)
      .slice(0, 10)
      .forEach((img, i) => {
        console.log(`  ${i + 1}. ${img.path} - ${img.sizeKB} KB`);
      });
  }

  // Economia potencial
  const potentialSavings = results.needsOptimization.reduce((sum, img) => {
    return sum + (img.size * 0.7); // 70% de economia esperada
  }, 0);
  
  console.log(`\n💰 Economia potencial: ${(potentialSavings / 1024 / 1024).toFixed(2)} MB (70%)`);
  
  // Salvar JSON
  fs.writeFileSync(
    'reports/image-analysis.json', 
    JSON.stringify(results, null, 2)
  );
  console.log('\n✅ Relatório salvo em: reports/image-analysis.json');
}

analyzeImages();
```

**Como usar:**
```bash
node analyze-images.js
```

---

### 1.3 Setup de Ferramentas (package.json)

```json
{
  "name": "lojinhaprio-optimization",
  "version": "1.0.0",
  "scripts": {
    "analyze": "ANALYZE=true next build",
    "lighthouse": "lighthouse https://lojinhaprio.yoobe.app --output html --output-path ./reports/lighthouse.html",
    "lighthouse:mobile": "lighthouse https://lojinhaprio.yoobe.app --output html --output-path ./reports/lighthouse-mobile.html --preset=mobile",
    "bundle-analyze": "npm run analyze",
    "baseline": "node scripts/baseline-check.js",
    "check-images": "node scripts/analyze-images.js",
    "perf-check": "npm run lighthouse && npm run lighthouse:mobile"
  },
  "devDependencies": {
    "@next/bundle-analyzer": "^14.0.0",
    "lighthouse": "^11.0.0"
  }
}
```

---

## 🗓️ DIA 2 SCRIPTS

### 2.1 Configuração Cloudflare (via API)

```javascript
// setup-cloudflare.js
const axios = require('axios');

const CLOUDFLARE_API_TOKEN = 'YOUR_TOKEN';
const ZONE_ID = 'YOUR_ZONE_ID';

const pageRules = [
  {
    targets: [{
      target: 'url',
      constraint: {
        operator: 'matches',
        value: 'lojinhaprio.yoobe.app/static/*'
      }
    }],
    actions: [
      { id: 'cache_level', value: 'cache_everything' },
      { id: 'edge_cache_ttl', value: 2592000 }, // 30 dias
      { id: 'browser_cache_ttl', value: 604800 }  // 7 dias
    ],
    priority: 1,
    status: 'active'
  },
  {
    targets: [{
      target: 'url',
      constraint: {
        operator: 'matches',
        value: 'lojinhaprio.yoobe.app/images/*'
      }
    }],
    actions: [
      { id: 'cache_level', value: 'cache_everything' },
      { id: 'edge_cache_ttl', value: 2592000 },
      { id: 'polish', value: 'lossless' }
    ],
    priority: 2,
    status: 'active'
  }
];

async function setupCloudflare() {
  console.log('⚙️  Configurando Cloudflare Page Rules...\n');

  for (const rule of pageRules) {
    try {
      const response = await axios.post(
        `https://api.cloudflare.com/client/v4/zones/${ZONE_ID}/pagerules`,
        rule,
        {
          headers: {
            'Authorization': `Bearer ${CLOUDFLARE_API_TOKEN}`,
            'Content-Type': 'application/json'
          }
        }
      );
      console.log(`✅ Regra criada: ${rule.targets[0].constraint.value}`);
    } catch (error) {
      console.error(`❌ Erro: ${error.message}`);
    }
  }
  
  console.log('\n✅ Configuração do Cloudflare concluída!');
}

setupCloudflare();
```

---

### 2.2 Configuração Redis no Rails

```ruby
# config/environments/production.rb

Rails.application.configure do
  # Redis Configuration
  config.cache_store = :redis_cache_store, {
    url: ENV['REDIS_URL'] || 'redis://localhost:6379/0',
    expires_in: 90.minutes,
    namespace: 'lojinhaprio',
    
    # Connection pool
    pool_size: ENV.fetch('RAILS_MAX_THREADS', 5).to_i,
    pool_timeout: 5,
    
    # Reconnect automaticamente
    reconnect_attempts: 3,
    
    # Compressão para economizar memória
    compress: true,
    compress_threshold: 1.kilobyte
  }
  
  # Session store no Redis
  config.session_store :redis_store,
    servers: ENV['REDIS_URL'] || 'redis://localhost:6379/0/session',
    expire_after: 90.minutes,
    key: '_lojinhaprio_session',
    threadsafe: true
end
```

---

### 2.3 Cache Helper para Spree

```ruby
# app/helpers/cache_helper.rb

module CacheHelper
  # Cache de produtos com invalidação automática
  def cached_products(options = {})
    cache_key = ['products', options.to_s, Product.maximum(:updated_at)].join('/')
    
    Rails.cache.fetch(cache_key, expires_in: 30.minutes) do
      Product.available
             .includes(:master, :images)
             .order(options[:sort] || 'created_at DESC')
             .limit(options[:limit] || 50)
    end
  end
  
  # Cache de categorias
  def cached_taxons
    Rails.cache.fetch('taxons/tree', expires_in: 24.hours) do
      Spree::Taxon.roots.includes(:children)
    end
  end
  
  # Invalidar cache ao atualizar produto
  def invalidate_product_cache(product)
    Rails.cache.delete_matched("products/*")
    Rails.cache.delete("product/#{product.id}")
  end
end
```

---

## 🗓️ DIA 3-4 SCRIPTS

### 3.1 Upload em Massa para Cloudinary

```javascript
// upload-to-cloudinary.js
const cloudinary = require('cloudinary').v2;
const fs = require('fs');
const path = require('path');

cloudinary.config({
  cloud_name: 'YOUR_CLOUD_NAME',
  api_key: 'YOUR_API_KEY',
  api_secret: 'YOUR_API_SECRET'
});

async function uploadImages(directory = './public/images') {
  console.log('☁️  Fazendo upload para Cloudinary...\n');
  
  const files = getAllImages(directory);
  console.log(`Encontradas ${files.length} imagens\n`);
  
  let success = 0;
  let failed = 0;
  
  for (const file of files) {
    try {
      const result = await cloudinary.uploader.upload(file, {
        folder: 'lojinhaprio',
        use_filename: true,
        unique_filename: false,
        quality: 'auto',
        fetch_format: 'auto'
      });
      
      console.log(`✅ ${path.basename(file)} - ${result.bytes} bytes`);
      success++;
      
      // Salvar mapeamento de URLs
      saveMapping(file, result.secure_url);
      
    } catch (error) {
      console.error(`❌ ${path.basename(file)} - ${error.message}`);
      failed++;
    }
  }
  
  console.log(`\n📊 Resultado: ${success} sucesso, ${failed} falhas`);
}

function getAllImages(dir, files = []) {
  const items = fs.readdirSync(dir);
  
  items.forEach(item => {
    const fullPath = path.join(dir, item);
    if (fs.statSync(fullPath).isDirectory()) {
      getAllImages(fullPath, files);
    } else if (/\.(jpg|jpeg|png|gif)$/i.test(item)) {
      files.push(fullPath);
    }
  });
  
  return files;
}

function saveMapping(localPath, cloudinaryUrl) {
  const mapping = JSON.parse(
    fs.readFileSync('cloudinary-mapping.json', 'utf8') || '{}'
  );
  mapping[localPath] = cloudinaryUrl;
  fs.writeFileSync('cloudinary-mapping.json', JSON.stringify(mapping, null, 2));
}

uploadImages();
```

**Como usar:**
```bash
npm install cloudinary
node upload-to-cloudinary.js
```

---

### 3.2 Next.js Image Component Wrapper

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
  quality = 85,
  className = '',
  ...props
}) {
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(false);

  // Se for URL local, converter para Cloudinary
  const cloudinarySrc = src.startsWith('/')
    ? `https://res.cloudinary.com/YOUR_CLOUD/image/upload/f_auto,q_auto${src}`
    : src;

  return (
    <div className={`relative ${className}`}>
      {isLoading && (
        <div className="absolute inset-0 bg-gray-200 animate-pulse rounded" />
      )}
      
      {!error ? (
        <Image
          src={cloudinarySrc}
          alt={alt}
          width={width}
          height={height}
          priority={priority}
          quality={quality}
          placeholder="blur"
          blurDataURL="data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNDAwIiBoZWlnaHQ9IjQwMCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48cmVjdCB3aWR0aD0iNDAwIiBoZWlnaHQ9IjQwMCIgZmlsbD0iI2VlZSIvPjwvc3ZnPg=="
          onLoadingComplete={() => setIsLoading(false)}
          onError={() => {
            setError(true);
            setIsLoading(false);
          }}
          className={`transition-opacity duration-300 ${
            isLoading ? 'opacity-0' : 'opacity-100'
          }`}
          {...props}
        />
      ) : (
        <div className="flex items-center justify-center bg-gray-200 rounded">
          <span className="text-gray-500">Imagem indisponível</span>
        </div>
      )}
    </div>
  );
}
```

---

### 3.3 Converter Imagens para WebP (Local)

```bash
#!/bin/bash
# convert-to-webp.sh

echo "🖼️  Convertendo imagens para WebP..."

# Instalar cwebp se necessário
# sudo apt-get install webp (Linux)
# brew install webp (Mac)

find ./public/images -type f \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" \) | while read file; do
  output="${file%.*}.webp"
  
  if [ ! -f "$output" ]; then
    echo "Convertendo: $file"
    cwebp -q 85 "$file" -o "$output"
    
    # Comparar tamanhos
    original_size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file")
    new_size=$(stat -f%z "$output" 2>/dev/null || stat -c%s "$output")
    saved=$((original_size - new_size))
    percent=$((saved * 100 / original_size))
    
    echo "  ✅ Economizou: $percent% ($saved bytes)"
  fi
done

echo "✅ Conversão concluída!"
```

---

## 🗓️ DIA 5 SCRIPTS

### 5.1 Análise de Bundle

```bash
#!/bin/bash
# analyze-bundle.sh

echo "📦 Analisando bundle..."

# Build com análise
ANALYZE=true npm run build

# Análise de dependências
echo ""
echo "📊 Análise de dependências pesadas:"
npx cost-of-modules --no-install --less

# Encontrar duplicações
echo ""
echo "🔍 Buscando duplicações:"
npx find-duplicate-dependencies

# Dependências não usadas
echo ""
echo "🗑️  Dependências não utilizadas:"
npx depcheck
```

---

### 5.2 Next.js Config Otimizado

```javascript
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // Compressão
  compress: true,
  
  // SWC Minifier (mais rápido)
  swcMinify: true,
  
  // Images
  images: {
    domains: ['res.cloudinary.com', 'lojinhaprio.yoobe.app'],
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256],
    minimumCacheTTL: 60 * 60 * 24 * 30, // 30 dias
  },
  
  // Webpack Optimization
  webpack: (config, { dev, isServer }) => {
    // Production optimizations
    if (!dev && !isServer) {
      config.optimization = {
        ...config.optimization,
        moduleIds: 'deterministic',
        runtimeChunk: 'single',
        splitChunks: {
          chunks: 'all',
          cacheGroups: {
            default: false,
            vendors: false,
            
            // Framework bundle (React, React-DOM)
            framework: {
              name: 'framework',
              chunks: 'all',
              test: /[\\/]node_modules[\\/](react|react-dom|scheduler)[\\/]/,
              priority: 40,
              enforce: true,
            },
            
            // Libs bundle
            lib: {
              test: /[\\/]node_modules[\\/]/,
              name(module) {
                const packageName = module.context.match(
                  /[\\/]node_modules[\\/](.*?)([\\/]|$)/
                )[1];
                return `npm.${packageName.replace('@', '')}`;
              },
              priority: 30,
              minChunks: 1,
              reuseExistingChunk: true,
            },
            
            // Commons
            commons: {
              name: 'commons',
              minChunks: 2,
              priority: 20,
            },
          },
        },
      };
    }
    
    return config;
  },
  
  // Headers
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
  
  // Experimental
  experimental: {
    optimizeCss: true,
    optimizePackageImports: [
      'lucide-react',
      '@heroicons/react',
      'date-fns'
    ],
  },
  
  // React Strict Mode
  reactStrictMode: true,
  
  // Remove console.log em produção
  compiler: {
    removeConsole: process.env.NODE_ENV === 'production',
  },
});
```

---

### 5.3 React Component com Performance

```jsx
// components/ProductCard.jsx
import { memo, useMemo, useCallback } from 'react';
import dynamic from 'next/dynamic';
import OptimizedImage from './OptimizedImage';

// Lazy load do AddToCart
const AddToCart = dynamic(() => import('./AddToCart'), {
  loading: () => <button disabled className="opacity-50">Carregando...</button>,
  ssr: false
});

const ProductCard = memo(function ProductCard({ product, onAddToCart }) {
  // Memoizar cálculos
  const formattedPrice = useMemo(() => {
    return new Intl.NumberFormat('pt-BR', {
      style: 'currency',
      currency: 'BRL'
    }).format(product.price);
  }, [product.price]);
  
  // Callback memoizado
  const handleAddToCart = useCallback(() => {
    onAddToCart?.(product.id);
  }, [product.id, onAddToCart]);
  
  return (
    <article className="product-card">
      <OptimizedImage
        src={product.image_url}
        alt={product.name}
        width={300}
        height={300}
        className="rounded-lg"
      />
      
      <h3 className="mt-2 text-lg font-semibold">
        {product.name}
      </h3>
      
      <p className="text-xl font-bold text-green-600">
        {formattedPrice}
      </p>
      
      <AddToCart 
        productId={product.id}
        onClick={handleAddToCart}
      />
    </article>
  );
});

export default ProductCard;
```

---

## 🗓️ DIA 6-7 SCRIPTS

### 6.1 Detectar N+1 com Bullet

```ruby
# config/environments/development.rb

Rails.application.configure do
  # Bullet configuration
  config.after_initialize do
    Bullet.enable = true
    Bullet.alert = false
    Bullet.bullet_logger = true
    Bullet.console = true
    Bullet.rails_logger = true
    
    # Adicionar no HTML (development)
    Bullet.add_footer = true
    
    # Slack notification (opcional)
    # Bullet.slack = { webhook_url: 'YOUR_WEBHOOK', channel: '#dev' }
  end
end
```

---

### 6.2 Corrigir N+1 em ProductsController

```ruby
# app/controllers/spree/products_controller.rb

module Spree
  class ProductsController < StoreController
    def index
      # ❌ ANTES (N+1 query)
      # @products = Product.available
      
      # ✅ DEPOIS (eager loading)
      @products = Product.available
                        .includes(
                          :master,
                          :default_variant,
                          variants: [:prices, :images, :option_values],
                          images: [:attachment_blob]
                        )
                        .page(params[:page])
                        .per(25)
      
      # Cache da query
      cache_key = [
        'products',
        params[:page],
        @products.maximum(:updated_at)
      ].join('/')
      
      @products = Rails.cache.fetch(cache_key, expires_in: 30.minutes) do
        @products.to_a
      end
    end
    
    def show
      # ✅ Eager loading para página de produto
      @product = Product.includes(
        variants: [:prices, :images, :option_values, :stock_items],
        product_properties: :property,
        images: [:attachment_blob]
      ).friendly.find(params[:id])
      
      # Cache do produto
      @cached_product = Rails.cache.fetch(
        "product/#{@product.id}/#{@product.updated_at}",
        expires_in: 1.hour
      ) do
        @product
      end
    end
  end
end
```

---

### 6.3 Queries SQL para Criar Índices

```sql
-- indices-lojinhaprio.sql

-- ==================================
-- ÍNDICES PARA SPREE COMMERCE
-- ==================================

-- Products
CREATE INDEX CONCURRENTLY idx_products_available_on 
  ON spree_products(available_on) 
  WHERE available_on IS NOT NULL AND deleted_at IS NULL;

CREATE INDEX CONCURRENTLY idx_products_slug 
  ON spree_products(slug) 
  WHERE deleted_at IS NULL;

-- Para busca full-text
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE INDEX CONCURRENTLY idx_products_name_trgm 
  ON spree_products USING gin(name gin_trgm_ops);

CREATE INDEX CONCURRENTLY idx_products_description_trgm 
  ON spree_products USING gin(description gin_trgm_ops);

-- Variants
CREATE INDEX CONCURRENTLY idx_variants_product_id 
  ON spree_variants(product_id) 
  WHERE deleted_at IS NULL;

CREATE INDEX CONCURRENTLY idx_variants_sku 
  ON spree_variants(sku) 
  WHERE sku IS NOT NULL AND deleted_at IS NULL;

CREATE INDEX CONCURRENTLY idx_variants_is_master 
  ON spree_variants(product_id, is_master) 
  WHERE is_master = true AND deleted_at IS NULL;

-- Stock Items
CREATE INDEX CONCURRENTLY idx_stock_items_variant_id 
  ON spree_stock_items(variant_id);

CREATE INDEX CONCURRENTLY idx_stock_items_stock_location 
  ON spree_stock_items(stock_location_id, variant_id);

-- Orders
CREATE INDEX CONCURRENTLY idx_orders_completed_at 
  ON spree_orders(completed_at) 
  WHERE completed_at IS NOT NULL;

CREATE INDEX CONCURRENTLY idx_orders_user_id 
  ON spree_orders(user_id) 
  WHERE user_id IS NOT NULL;

CREATE INDEX CONCURRENTLY idx_orders_number 
  ON spree_orders(number);

CREATE INDEX CONCURRENTLY idx_orders_state 
  ON spree_orders(state);

-- Line Items
CREATE INDEX CONCURRENTLY idx_line_items_order_id 
  ON spree_line_items(order_id);

CREATE INDEX CONCURRENTLY idx_line_items_variant_id 
  ON spree_line_items(variant_id);

CREATE INDEX CONCURRENTLY idx_line_items_order_variant 
  ON spree_line_items(order_id, variant_id);

-- Taxons (Categories)
CREATE INDEX CONCURRENTLY idx_taxons_parent_id 
  ON spree_taxons(parent_id) 
  WHERE parent_id IS NOT NULL;

CREATE INDEX CONCURRENTLY idx_taxons_lft_rgt 
  ON spree_taxons(lft, rgt);

CREATE INDEX CONCURRENTLY idx_taxons_permalink 
  ON spree_taxons(permalink);

-- Images
CREATE INDEX CONCURRENTLY idx_images_viewable 
  ON spree_assets(viewable_type, viewable_id) 
  WHERE type = 'Spree::Image';

-- Prices
CREATE INDEX CONCURRENTLY idx_prices_variant_id 
  ON spree_prices(variant_id);

CREATE INDEX CONCURRENTLY idx_prices_currency 
  ON spree_prices(variant_id, currency);

-- ==================================
-- VERIFICAR ÍNDICES CRIADOS
-- ==================================

SELECT
  tablename,
  indexname,
  indexdef
FROM pg_indexes
WHERE schemaname = 'public'
  AND tablename LIKE 'spree_%'
ORDER BY tablename, indexname;
```

**Como executar:**
```bash
# Conectar no PostgreSQL
psql -h YOUR_HOST -U YOUR_USER -d YOUR_DATABASE -f indices-lojinhaprio.sql
```

---

### 6.4 PostgreSQL Tuning

```sql
-- postgresql-tuning.sql

-- ==================================
-- OTIMIZAÇÃO POSTGRESQL
-- ==================================

-- Memória (ajustar conforme sua instância)
ALTER SYSTEM SET shared_buffers = '2GB';
ALTER SYSTEM SET effective_cache_size = '6GB';
ALTER SYSTEM SET maintenance_work_mem = '512MB';
ALTER SYSTEM SET work_mem = '10MB';

-- Query Planning
ALTER SYSTEM SET random_page_cost = 1.1;  -- Para SSD
ALTER SYSTEM SET effective_io_concurrency = 200;

-- WAL
ALTER SYSTEM SET wal_buffers = '16MB';
ALTER SYSTEM SET min_wal_size = '1GB';
ALTER SYSTEM SET max_wal_size = '4GB';
ALTER SYSTEM SET checkpoint_completion_target = 0.9;

-- Autovacuum
ALTER SYSTEM SET autovacuum = on;
ALTER SYSTEM SET autovacuum_max_workers = 3;
ALTER SYSTEM SET autovacuum_naptime = '10s';

-- Statistics
ALTER SYSTEM SET default_statistics_target = 100;

-- Reload config (não requer restart para alguns parâmetros)
SELECT pg_reload_conf();

-- Para aplicar todas as mudanças:
-- sudo systemctl restart postgresql
-- (ou restart da instância GCP Cloud SQL)
```

---

## 🗓️ DIA 8 SCRIPTS

### 8.1 Extrair Critical CSS

```javascript
// extract-critical-css.js
const critical = require('critical');

async function extractCritical() {
  try {
    const { css, html } = await critical.generate({
      base: './out/', // ou .next se SSR
      src: 'index.html',
      target: {
        css: 'critical.css',
        html: 'index-critical.html'
      },
      width: 1300,
      height: 900,
      inline: true,
      extract: true,
      dimensions: [
        {
          width: 375,
          height: 667,
        },
        {
          width: 1920,
          height: 1080,
        },
      ],
    });

    console.log('✅ Critical CSS extraído com sucesso!');
  } catch (err) {
    console.error('❌ Erro:', err);
  }
}

extractCritical();
```

**Como usar:**
```bash
npm install critical
node extract-critical-css.js
```

---

### 8.2 ISR Implementation (Next.js)

```jsx
// pages/products/[slug].js

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <OptimizedImage 
        src={product.image}
        alt={product.name}
        width={600}
        height={600}
        priority
      />
      <p>{product.description}</p>
      <p className="price">${product.price}</p>
    </div>
  );
}

// ISR: Incremental Static Regeneration
export async function getStaticProps({ params }) {
  const product = await fetch(
    `https://lojinhaprio.yoobe.app/api/products/${params.slug}`
  ).then(res => res.json());

  return {
    props: {
      product,
    },
    // Revalidar a cada 1 hora
    revalidate: 3600,
  };
}

// Gerar páginas no build
export async function getStaticPaths() {
  const products = await fetch(
    'https://lojinhaprio.yoobe.app/api/products?limit=100'
  ).then(res => res.json());

  const paths = products.data.map(product => ({
    params: { slug: product.slug },
  }));

  return {
    paths,
    // Gerar outras páginas on-demand
    fallback: 'blocking',
  };
}
```

---

## 📊 SCRIPTS DE MONITORAMENTO

### Monitor Contínuo de Performance

```javascript
// monitor-performance.js
const lighthouse = require('lighthouse');
const chromeLauncher = require('chrome-launcher');
const fs = require('fs');

async function runLighthouse(url) {
  const chrome = await chromeLauncher.launch({ chromeFlags: ['--headless'] });
  
  const options = {
    logLevel: 'info',
    output: 'json',
    onlyCategories: ['performance'],
    port: chrome.port,
  };
  
  const runnerResult = await lighthouse(url, options);
  
  await chrome.kill();
  
  return runnerResult.lhr;
}

async function monitor() {
  console.log('🔍 Monitorando performance...\n');
  
  const urls = [
    'https://lojinhaprio.yoobe.app',
    'https://lojinhaprio.yoobe.app/products',
  ];
  
  const results = [];
  
  for (const url of urls) {
    console.log(`Testando: ${url}`);
    const result = await runLighthouse(url);
    
    const metrics = {
      url,
      timestamp: new Date().toISOString(),
      score: result.categories.performance.score * 100,
      fcp: result.audits['first-contentful-paint'].numericValue,
      lcp: result.audits['largest-contentful-paint'].numericValue,
      tbt: result.audits['total-blocking-time'].numericValue,
      cls: result.audits['cumulative-layout-shift'].numericValue,
    };
    
    console.log(`  Score: ${metrics.score}`);
    console.log(`  LCP: ${(metrics.lcp / 1000).toFixed(2)}s`);
    console.log(`  TBT: ${metrics.tbt}ms\n`);
    
    results.push(metrics);
  }
  
  // Salvar histórico
  const history = JSON.parse(
    fs.readFileSync('performance-history.json', 'utf8') || '[]'
  );
  
  history.push(...results);
  fs.writeFileSync(
    'performance-history.json',
    JSON.stringify(history, null, 2)
  );
  
  console.log('✅ Resultados salvos em performance-history.json');
}

monitor();
```

---

### Dashboard Simples (HTML)

```html
<!-- dashboard.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Performance Dashboard - Lojinha Prio</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: system-ui, -apple-system, sans-serif;
      max-width: 1200px;
      margin: 0 auto;
      padding: 20px;
    }
    .metric {
      display: inline-block;
      padding: 20px;
      margin: 10px;
      background: #f5f5f5;
      border-radius: 8px;
      min-width: 200px;
    }
    .metric h3 {
      margin: 0 0 10px 0;
      color: #666;
    }
    .metric .value {
      font-size: 36px;
      font-weight: bold;
      color: #333;
    }
    .good { color: #22c55e; }
    .warning { color: #f59e0b; }
    .bad { color: #ef4444; }
  </style>
</head>
<body>
  <h1>📊 Performance Dashboard</h1>
  
  <div id="metrics"></div>
  
  <canvas id="chart" width="800" height="400"></canvas>
  
  <script>
    async function loadData() {
      const response = await fetch('performance-history.json');
      const data = await response.json();
      
      // Últimas métricas
      const latest = data[data.length - 1];
      
      document.getElementById('metrics').innerHTML = `
        <div class="metric">
          <h3>Performance Score</h3>
          <div class="value ${latest.score >= 90 ? 'good' : latest.score >= 50 ? 'warning' : 'bad'}">
            ${latest.score.toFixed(0)}
          </div>
        </div>
        
        <div class="metric">
          <h3>LCP</h3>
          <div class="value ${latest.lcp < 2500 ? 'good' : latest.lcp < 4000 ? 'warning' : 'bad'}">
            ${(latest.lcp / 1000).toFixed(2)}s
          </div>
        </div>
        
        <div class="metric">
          <h3>TBT</h3>
          <div class="value ${latest.tbt < 200 ? 'good' : latest.tbt < 600 ? 'warning' : 'bad'}">
            ${latest.tbt.toFixed(0)}ms
          </div>
        </div>
        
        <div class="metric">
          <h3>CLS</h3>
          <div class="value ${latest.cls < 0.1 ? 'good' : latest.cls < 0.25 ? 'warning' : 'bad'}">
            ${latest.cls.toFixed(3)}
          </div>
        </div>
      `;
      
      // Gráfico de evolução
      const ctx = document.getElementById('chart').getContext('2d');
      new Chart(ctx, {
        type: 'line',
        data: {
          labels: data.map(d => new Date(d.timestamp).toLocaleDateString()),
          datasets: [{
            label: 'Performance Score',
            data: data.map(d => d.score),
            borderColor: '#3b82f6',
            tension: 0.1
          }]
        },
        options: {
          responsive: true,
          scales: {
            y: {
              beginAtZero: true,
              max: 100
            }
          }
        }
      });
    }
    
    loadData();
  </script>
</body>
</html>
```

---

## 🎯 CHECKLIST DE EXECUÇÃO

### Dia 1
- [ ] Rodar `baseline-check.sh`
- [ ] Rodar `analyze-images.js`
- [ ] Instalar todas as ferramentas
- [ ] Documentar métricas iniciais

### Dia 2
- [ ] Configurar Cloudflare
- [ ] Setup Redis (GCP Memorystore)
- [ ] Implementar cache no Rails
- [ ] Testar e medir

### Dia 3-4
- [ ] Upload para Cloudinary
- [ ] Implementar Next.js Image
- [ ] Otimizar fonts
- [ ] Testar todas as páginas

### Dia 5
- [ ] Rodar `analyze-bundle.sh`
- [ ] Aplicar otimizações no next.config.js
- [ ] Otimizar components React
- [ ] Build e medir

### Dia 6-7
- [ ] Ativar Bullet gem
- [ ] Corrigir N+1 queries
- [ ] Executar `indices-lojinhaprio.sql`
- [ ] Aplicar `postgresql-tuning.sql`

### Dia 8
- [ ] Extrair Critical CSS
- [ ] Implementar ISR
- [ ] Configurar preload/prefetch

### Dia 9-10
- [ ] Otimizar GCP
- [ ] Testing completo
- [ ] Deploy gradual
- [ ] Monitoramento

---

**✅ Todos os scripts estão prontos para uso!**

*Basta copiar, colar e executar seguindo as instruções.*
