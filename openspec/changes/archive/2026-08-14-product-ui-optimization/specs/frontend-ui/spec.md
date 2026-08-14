## Purpose

定义电商系统的前端 UI 表现和交互逻辑，确保符合 Modern Flat 设计规范并提供专业的商品展示体验。

## ADDED Requirements

### Requirement: 商品卡片展示
**Priority**: P0
**Rationale**: 商品卡片是电商 UI 的核心，直接决定了用户对产品的专业感认知。

系统 SHALL 以卡片形式展示商品，每张卡片必须包含图片、名称、描述、价格和操作按钮。

#### Scenario: 渲染商品卡片

- **GIVEN** 商品数据包含 { name, description, price, imageUrl }
- **WHEN** 页面加载商品列表
- **THEN** 系统展示 1px 边框的 Modern Flat 风格卡片
- **AND** 图片显示在卡片顶部，宽高比为 4:3
- **AND** 鼠标悬停时卡片边框颜色加深

### Requirement: 响应式商品网格
**Priority**: P1
**Rationale**: 确保不同设备上的用户都能获得一致且专业的浏览体验。

系统 SHALL 使用响应式网格布局展示商品卡片，确保在不同屏幕尺寸下保持紧凑。

#### Scenario: 响应式网格布局

- **WHEN** 在大屏幕上查看
- **THEN** 网格至少显示 3 列
- **WHEN** 在移动端查看
- **THEN** 网格自动调整为 1 列

### Requirement: 实时商品搜索
**Priority**: P2
**Rationale**: 提升用户在大规模商品目录中查找特定商品的效率。

系统 SHALL 提供基于商品名称和分类的实时搜索过滤功能。

#### Scenario: 动态过滤列表
- **GIVEN** 用户位于商品目录页面
- **WHEN** 用户在搜索框输入关键词
- **THEN** 商品列表立即更新，仅显示匹配该关键词的商品
- **AND** 如果没有匹配项，显示“未找到相关商品”的空状态反馈

<details>
<summary>View UI Prototype Code</summary>

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>极简商品展示 - Modern Flat Prototype</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Vue 3 CDN -->
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap');
        
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            overflow: hidden;
        }

        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }

        .flat-border {
            border: 1px solid #e2e8f0;
        }
        
        .aspect-4-3 {
            aspect-ratio: 4 / 3;
        }
    </style>
</head>
<body class="bg-white text-slate-900">
    <div id="app" class="h-screen flex flex-col overflow-hidden">
        <!-- 顶部导航 -->
        <header class="h-16 flex-shrink-0 flat-border border-t-0 border-l-0 border-r-0 flex items-center justify-between px-8 bg-white z-10">
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 bg-slate-900 flex items-center justify-center text-white text-xs font-bold">M</div>
                <h1 class="text-lg font-semibold tracking-tight uppercase">Minimal Store</h1>
            </div>
            
            <div class="flex-1 max-w-md mx-8 relative">
                <i data-lucide="search" class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-slate-400"></i>
                <input 
                    v-model="searchQuery"
                    type="text" 
                    placeholder="搜索商品..." 
                    class="w-full pl-10 pr-4 py-2 bg-slate-50 flat-border rounded-none focus:outline-none focus:border-slate-400 transition-colors text-sm"
                >
            </div>

            <div class="flex items-center gap-6 text-sm font-medium">
                <button @click="isCartOpen = !isCartOpen" class="relative">
                    <i data-lucide="shopping-bag" class="w-5 h-5"></i>
                    <span v-if="cartTotalItems > 0" class="absolute -top-1 -right-1 w-4 h-4 bg-slate-900 text-white text-[10px] flex items-center justify-center rounded-none">
                        {{ cartTotalItems }}
                    </span>
                </button>
            </div>
        </header>

        <!-- 主内容区 -->
        <main class="flex-1 flex overflow-hidden">
            <!-- 左侧商品网格 -->
            <section class="flex-1 overflow-y-auto p-8 no-scrollbar bg-slate-50">
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div 
                        v-for="product in filteredProducts" 
                        :key="product.id"
                        class="bg-white flat-border group transition-colors hover:border-slate-400"
                    >
                        <div class="aspect-4-3 overflow-hidden bg-white flat-border border-t-0 border-l-0 border-r-0 relative">
                            <img 
                                :src="product.imageUrl" 
                                :alt="product.name"
                                class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-105"
                                loading="lazy"
                            >
                            <div class="absolute top-3 right-3">
                                <span class="px-2 py-1 bg-white flat-border text-[10px] font-bold tracking-wider uppercase">New</span>
                            </div>
                        </div>
                        <div class="p-5 flex flex-col gap-3">
                            <div class="flex justify-between items-start">
                                <div>
                                    <h3 class="font-medium text-slate-900">{{ product.name }}</h3>
                                    <p class="text-xs text-slate-400 mt-1 uppercase tracking-widest">{{ product.category }}</p>
                                </div>
                                <span class="font-semibold text-sm">¥{{ product.price.toFixed(2) }}</span>
                            </div>
                            <button 
                                @click="addToCart(product)"
                                class="w-full py-2 bg-slate-900 text-white text-xs font-bold tracking-widest uppercase hover:bg-slate-800 transition-colors flex items-center justify-center gap-2"
                            >
                                <i data-lucide="plus" class="w-3 h-3"></i>
                                加入购物车
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 右侧购物车侧边栏 -->
            <aside 
                :class="['w-80 flex-shrink-0 bg-white flat-border border-t-0 border-r-0 border-b-0 flex flex-col transition-all duration-300', isCartOpen ? 'mr-0' : '-mr-80']"
            >
                <div class="p-6 flat-border border-t-0 border-l-0 border-r-0 flex items-center justify-between">
                    <h2 class="font-semibold tracking-tight">购物车 ({{ cartTotalItems }})</h2>
                    <button @click="isCartOpen = false"><i data-lucide="x" class="w-4 h-4 text-slate-400"></i></button>
                </div>

                <div class="flex-1 overflow-y-auto p-6 no-scrollbar space-y-6">
                    <div v-if="cart.length === 0" class="h-full flex flex-col items-center justify-center text-slate-400 space-y-2">
                        <i data-lucide="shopping-cart" class="w-8 h-8 stroke-1"></i>
                        <p class="text-xs">购物车是空的</p>
                    </div>
                    <div v-for="item in cart" :key="item.id" class="flex gap-4">
                        <div class="w-16 h-16 flex-shrink-0 flat-border bg-slate-50 overflow-hidden">
                            <img :src="item.imageUrl" class="w-full h-full object-cover">
                        </div>
                        <div class="flex-1 flex flex-col justify-between py-1">
                            <div class="flex justify-between">
                                <h4 class="text-xs font-medium">{{ item.name }}</h4>
                                <button @click="removeFromCart(item.id)"><i data-lucide="trash-2" class="w-3 h-3 text-slate-300 hover:text-red-500"></i></button>
                            </div>
                            <div class="flex justify-between items-end">
                                <span class="text-xs font-semibold">¥{{ (item.price * item.quantity).toFixed(2) }}</span>
                                <span class="text-[10px] text-slate-400 text-right">x {{ item.quantity }}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="p-6 flat-border border-b-0 border-l-0 border-r-0">
                    <div class="flex justify-between mb-4">
                        <span class="text-xs text-slate-500">总计</span>
                        <span class="font-bold">¥{{ cartTotalPrice.toFixed(2) }}</span>
                    </div>
                    <button class="w-full py-3 bg-slate-900 text-white text-xs font-bold tracking-[0.2em] uppercase hover:bg-slate-800 transition-colors">结算</button>
                </div>
            </aside>
        </main>
    </div>

    <script>
        const { createApp, ref, computed, onMounted, nextTick } = Vue;
        createApp({
            setup() {
                const searchQuery = ref('');
                const isCartOpen = ref(true);
                const cart = ref([]);
                const products = ref([
                    { 
                        id: 1, name: '极简机械键盘', price: 299.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1511467687858-23d96c32e4ae?auto=format&fit=crop&q=80&w=800', 
                        category: 'Peripherals' 
                    },
                    { 
                        id: 2, name: '无线办公鼠标', price: 89.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1527864550417-7fd91fc51a46?auto=format&fit=crop&q=80&w=800', 
                        category: 'Peripherals' 
                    },
                    { 
                        id: 3, name: '高清显示器', price: 1299.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1527443224154-c4a3942d3acf?auto=format&fit=crop&q=80&w=800', 
                        category: 'Hardware' 
                    },
                    { 
                        id: 4, name: '桌面收纳架', price: 45.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1586023492125-27b2c045efd7?auto=format&fit=crop&q=80&w=800', 
                        category: 'Office' 
                    },
                    { 
                        id: 5, name: '铝合金笔记本支架', price: 68.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1527443154391-507e9dc6c5cc?auto=format&fit=crop&q=80&w=800', 
                        category: 'Accessories' 
                    },
                    { 
                        id: 6, name: '桌面拾音氛围灯', price: 128.00, 
                        imageUrl: 'https://images.unsplash.com/photo-1550745165-9bc0b252728f?auto=format&fit=crop&q=80&w=800', 
                        category: 'Lighting' 
                    }
                ]);

                const filteredProducts = computed(() => {
                    if (!searchQuery.value) return products.value;
                    return products.value.filter(p => p.name.includes(searchQuery.value));
                });

                const cartTotalItems = computed(() => cart.value.reduce((t, i) => t + i.quantity, 0));
                const cartTotalPrice = computed(() => cart.value.reduce((t, i) => t + (i.price * i.quantity), 0));

                const addToCart = (product) => {
                    const item = cart.value.find(i => i.id === product.id);
                    if (item) item.quantity++; else cart.value.push({ ...product, quantity: 1 });
                    isCartOpen.value = true;
                    nextTick(() => lucide.createIcons());
                };

                const removeFromCart = (id) => cart.value = cart.value.filter(i => i.id !== id);

                onMounted(() => lucide.createIcons());

                return { searchQuery, isCartOpen, products, filteredProducts, cart, cartTotalItems, cartTotalPrice, addToCart, removeFromCart };
            }
        }).mount('#app');
    </script>
</body>
</html>
```
</details>
