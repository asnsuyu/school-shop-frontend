<script setup>
import {useCartStore} from '@/stores/cartStore.js'

const cartStore = useCartStore()
const singleCheck = (selected, i) => {
  // console.log(selected, i)
  cartStore.singleCheck(i.skuId, selected)
}
const allCheck = (selected) => {
  cartStore.allCheck(selected)
}
console.log('这是增加的一行代码')
</script>

<template>
  <div class="xtx-cart-page">
    <div class="container m-top-20">
      <div class="cart">
        <table>
          <thead>
          <tr class="title">
            <th style="min-width: 120px">
              <el-checkbox :model-value="cartStore.isAll" @change="allCheck"/>
            </th>
            <th style="min-width: 400px">商品信息</th>
            <th style="min-width: 220px">单价</th>
            <th style="min-width: 180px">数量</th>
            <th style="min-width: 180px">小计</th>
            <th style="min-width: 140px">操作</th>
          </tr>
          </thead>
          <!-- 商品列表 -->
          <tbody>
          <tr v-for="i in cartStore.cartList" :key="i.id">
            <td>
              <!-- 写法1：@click="fn"                      这样写，回调fn中可以拿到1个参数：事件对象 -->
              <!-- 写法2：@click="fn(param)                这样写，回调fn中可以拿到1个指定参数：param -->
              <!-- 写法3：@click="(e) => fn(e, param, ...) 这样写，回调fn中可以拿到n个参数：事件对象、param1、..." -->
              <!-- 如果使用的非原生标签而是第三方组件，那么拿到的可能不是事件对象，而是剥离处理过的属性 -->
              <!-- singleCheck需要两个参数，选写法3 -->
              <el-checkbox :model-value="i.selected" @change="(selected) => singleCheck(selected, i)"/>
            </td>
            <td>
              <div class="goods">
                <RouterLink to="/"><img :src="i.picture" alt=""/></RouterLink>
                <div>
                  <p class="name ellipsis">
                    {{ i.name }}
                  </p>
                </div>
              </div>
            </td>
            <td class="tc">
              <p>&yen;{{ i.price }}</p>
            </td>
            <td class="tc">
              <el-input-number v-model="i.count" :min="1" :max="10"/>
            </td>
            <td class="tc">
              <p class="f16 red">&yen;{{ (i.price * i.count).toFixed(2) }}</p>
            </td>
            <td class="tc">
              <p>
                <el-popconfirm title="确认删除吗?" confirm-button-text="确认" cancel-button-text="取消"
                               @confirm="delCart(i)">
                  <template #reference>
                    <a href="javascript:">删除</a>
                  </template>
                </el-popconfirm>
              </p>
            </td>
          </tr>
          <tr v-if="cartStore.cartList.length === 0">
            <td colspan="6">
              <div class="cart-none">
                <el-empty description="购物车列表为空">
                  <el-button type="primary" @click="$router.push('/')">随便逛逛</el-button>
                </el-empty>
              </div>
            </td>
          </tr>
          </tbody>

        </table>
      </div>
      <!-- 操作栏 -->
      <div class="action">
        <div class="batch">
          共 {{ cartStore.allCount }} 件商品，已选择 {{ cartStore.selectedCount }}
          件，商品合计：
          <span class="red">¥ {{ cartStore.selectedPrice.toFixed(2) }} </span>
        </div>
        <div class="total">
          <el-button size="large" type="primary" @click="$router.push('/checkout')">下单结算</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.xtx-cart-page {
  margin-top: 20px;

  .cart {
    background: #fff;
    color: #666;

    table {
      border-spacing: 0;
      border-collapse: collapse;
      line-height: 24px;

      th,
      td {
        padding: 10px;
        border-bottom: 1px solid #f5f5f5;

        &:first-child {
          text-align: left;
          padding-left: 30px;
          color: #999;
        }
      }

      th {
        font-size: 16px;
        font-weight: normal;
        line-height: 50px;
      }
    }
  }

  .cart-none {
    text-align: center;
    padding: 120px 0;
    background: #fff;

    p {
      color: #999;
      padding: 20px 0;
    }
  }

  .tc {
    text-align: center;

    a {
      color: $xtxColor;
    }

    .xtx-numbox {
      margin: 0 auto;
      width: 120px;
    }
  }

  .red {
    color: $priceColor;
  }

  .green {
    color: $xtxColor;
  }

  .f16 {
    font-size: 16px;
  }

  .goods {
    display: flex;
    align-items: center;

    img {
      width: 100px;
      height: 100px;
    }

    > div {
      width: 280px;
      font-size: 16px;
      padding-left: 10px;

      .attr {
        font-size: 14px;
        color: #999;
      }
    }
  }

  .action {
    display: flex;
    background: #fff;
    margin-top: 20px;
    height: 80px;
    align-items: center;
    font-size: 16px;
    justify-content: space-between;
    padding: 0 30px;

    .xtx-checkbox {
      color: #999;
    }

    .batch {
      a {
        margin-left: 20px;
      }
    }

    .red {
      font-size: 18px;
      margin-right: 20px;
      font-weight: bold;
    }
  }

  .tit {
    color: #666;
    font-size: 16px;
    font-weight: normal;
    line-height: 50px;
  }

}
</style>