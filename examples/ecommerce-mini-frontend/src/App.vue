<template>
  <div class="app-container">
    <!-- 左侧：商品列表 -->
    <div class="product-section">
      <h2 class="section-title">商品列表</h2>
      <div class="product-grid">
        <div v-for="product in products" :key="product.id" class="product-card">
          <div class="product-info">
            <h3 class="product-name">{{ product.name }}</h3>
            <p class="product-desc">{{ product.description }}</p>
            <span class="product-price">¥{{ product.price.toFixed(2) }}</span>
          </div>
          <button class="action-btn" @click="addToCart(product)">加入购物车</button>
        </div>
      </div>
    </div>

    <!-- 右侧：购物车 -->
    <div class="cart-section">
      <h2 class="section-title">购物车</h2>
      <div v-if="cart.length === 0" class="empty-cart">
        购物车为空
      </div>
      <div v-else class="cart-items">
        <div class="cart-list">
          <div v-for="item in cart" :key="item.id" class="cart-item">
            <div class="cart-item-info">
              <h4 class="cart-item-name">{{ item.name }}</h4>
              <div class="cart-item-meta">
                <span class="cart-item-price">¥{{ item.price.toFixed(2) }}</span>
                <span class="cart-item-qty">x {{ item.quantity }}</span>
              </div>
            </div>
            <button class="remove-btn" @click="removeFromCart(item.id)">移除</button>
          </div>
        </div>
        <div class="cart-summary">
          <span class="summary-label">总计：</span>
          <span class="summary-total">¥{{ cartTotal.toFixed(2) }}</span>
        </div>
        <button 
          class="checkout-btn" 
          :disabled="isProcessing" 
          @click="checkout"
        >
          {{ isProcessing ? '结算中...' : '去结算' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 模拟商品数据
const products = ref([
  { id: 1, name: '极简机械键盘', description: '84键紧凑布局，红轴', price: 299.00 },
  { id: 2, name: '无线办公鼠标', description: '静音按键，人体工学设计', price: 89.00 },
  { id: 3, name: '高清显示器', description: '27英寸 4K分辨率', price: 1299.00 },
  { id: 4, name: '桌面收纳架', description: '实木材质，双层结构', price: 45.00 },
  { id: 5, name: '铝合金笔记本支架', description: '折叠便携，多档调节', price: 68.00 },
  { id: 6, name: '桌面拾音氛围灯', description: 'RGB色彩，支持音频同步', price: 128.00 }
])

// 购物车状态
const cart = ref([])
const isProcessing = ref(false)

// 添加到购物车
const addToCart = async (product) => {
  try {
    // 同步到后端
    const response = await fetch('http://localhost:3000/api/cart/items', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        productId: String(product.id),
        quantity: 1
      })
    })

    if (!response.ok) {
      const err = await response.json()
      throw new Error(err.message || '同步失败')
    }

    // 更新本地状态
    const existingItem = cart.value.find(item => item.id === product.id)
    if (existingItem) {
      existingItem.quantity++
    } else {
      cart.value.push({
        id: product.id,
        name: product.name,
        price: product.price,
        quantity: 1
      })
    }
  } catch (e) {
    alert(`加入购物车失败: ${e.message}`)
  }
}

// 从购物车移除（减少数量或直接移除）
const removeFromCart = (productId) => {
  const index = cart.value.findIndex(item => item.id === productId)
  if (index !== -1) {
    if (cart.value[index].quantity > 1) {
      cart.value[index].quantity--
    } else {
      cart.value.splice(index, 1)
    }
  }
}

// 计算总价
const cartTotal = computed(() => {
  return cart.value.reduce((total, item) => total + item.price * item.quantity, 0)
})

// 结算方法
const checkout = async () => {
  if (cart.value.length === 0) return

  isProcessing.value = true
  try {
    // 假设后端运行在 3000 端口
    const response = await fetch('http://localhost:3000/api/checkout', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ userId: 'user_dev' })
    })

    if (response.ok) {
      const order = await response.json()
      alert(`结算成功！订单号: ${order.id}`)
      cart.value = [] // 清空购物车
    } else {
      const error = await response.json()
      alert(`结算失败: ${error.message || '未知错误'}`)
    }
  } catch (e) {
    alert(`网络错误: ${e.message}`)
  } finally {
    isProcessing.value = false
  }
}
</script>

<style scoped>
/* 全局容器：单屏布局，去除多余留白 */
.app-container {
  display: flex;
  height: 100vh;
  width: 100%;
  background-color: #f5f5f5;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  box-sizing: border-box;
  overflow: hidden;
}

/* 通用标题 */
.section-title {
  font-size: 1.2rem;
  font-weight: 600;
  color: #111;
  margin: 0 0 20px 0;
  padding-bottom: 12px;
  border-bottom: 1px solid #e0e0e0;
}

/* 左侧商品区 */
.product-section {
  flex: 2;
  padding: 24px;
  background-color: #f9f9f9;
  border-right: 1px solid #e0e0e0;
  overflow-y: auto;
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 16px;
}

/* 极简卡片设计：无阴影，1px边框，纯色背景 */
.product-card {
  background-color: #ffffff;
  border: 1px solid #e0e0e0;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.product-info {
  margin-bottom: 16px;
}

.product-name {
  font-size: 1rem;
  font-weight: 600;
  color: #111;
  margin: 0 0 8px 0;
}

.product-desc {
  font-size: 0.85rem;
  color: #666;
  margin: 0 0 12px 0;
  line-height: 1.4;
}

.product-price {
  font-size: 1.1rem;
  font-weight: 700;
  color: #111;
}

/* 按钮设计：扁平，直角，纯色 */
.action-btn {
  background-color: #111;
  color: #fff;
  border: 1px solid #111;
  padding: 8px 12px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: background-color 0.2s;
  width: 100%;
}

.action-btn:hover {
  background-color: #333;
}

/* 右侧购物车区 */
.cart-section {
  flex: 1;
  min-width: 320px;
  max-width: 400px;
  padding: 24px;
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
}

.empty-cart {
  color: #999;
  font-size: 0.9rem;
  text-align: center;
  margin-top: 40px;
}

.cart-items {
  display: flex;
  flex-direction: column;
  flex: 1;
  overflow: hidden;
}

.cart-list {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

/* 购物车商品项：同样遵守极简风格 */
.cart-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  border: 1px solid #e0e0e0;
  background-color: #fafafa;
}

.cart-item-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.cart-item-name {
  margin: 0;
  font-size: 0.95rem;
  color: #111;
}

.cart-item-meta {
  font-size: 0.85rem;
  color: #666;
  display: flex;
  gap: 12px;
}

.remove-btn {
  background: transparent;
  color: #666;
  border: 1px solid #ccc;
  padding: 4px 8px;
  font-size: 0.8rem;
  cursor: pointer;
}

.remove-btn:hover {
  background: #eee;
  color: #111;
}

/* 结算区域 */
.cart-summary {
  margin-top: 24px;
  padding-top: 16px;
  border-top: 1px solid #e0e0e0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.summary-label {
  font-size: 1rem;
  color: #333;
}

.summary-total {
  font-size: 1.2rem;
  font-weight: 700;
  color: #111;
}

.checkout-btn {
  margin-top: 16px;
  background-color: #111;
  color: #fff;
  border: none;
  padding: 12px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  width: 100%;
}

.checkout-btn:disabled {
  background-color: #999;
  cursor: not-allowed;
}

.checkout-btn:hover:not(:disabled) {
  background-color: #333;
}

/* 隐藏滚动条让视觉更干净 */
::-webkit-scrollbar {
  width: 6px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: #ccc;
}
::-webkit-scrollbar-thumb:hover {
  background: #999;
}
</style>