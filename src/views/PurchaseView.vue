<template>
  <!-- 画面全体のレイアウトコンテナ -->
  <v-container>
    
    <!-- 中央揃え -->
    <v-row justify="center">
      
      <!-- 幅を指定したカラム：モバイルで全幅、タブレットサイズ以上で幅6 -->
      <v-col cols="12" md="6" class="text-center">
        
        <!-- タイトル表示 -->
        <h2 class="text-h5 font-weight-bold mb-4">購入ページ</h2>

        <!-- 商品リストの説明文 -->
        <p class="text-subtitle-1 mb-2">カートに入っている商品一覧：</p>

        <!-- カート内の商品を表示する Vuetify のリストコンポーネント -->
        <v-list dense class="mb-4">
          <!-- 数量が1以上の商品だけを1件ずつ表示 -->
          <v-list-item
            v-for="item in productsInCart"
            :key="item.id"
          >
            <!-- 商品名と個数を中央揃えで表示 -->
            <v-list-item-content class="text-center">
              <v-list-item-title>
                {{ item.name }} - {{ item.quantity }}個
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>

        <!-- 合計金額の表示 -->
        <v-divider class="my-3"></v-divider>
        <p class="text-subtitle-1 font-weight-bold mb-4">
          合計金額：{{ totalPrice }}円
        </p>

        <!-- 購入ボタン：購入処理中はローディング表示、商品がない時は非活性 -->
        <v-btn
          color="success"
          @click="purchase"
          :loading="loading"
          :disabled="loading || productsInCart.length === 0"
        >
          購入する
        </v-btn>

        <!-- 購入完了時に表示するポップアップメッセージ -->
        <v-alert
          v-if="showPopup"
          type="success"
          class="mt-6"
          border="left"
          colored-border
          elevation="2"
        >
          購入が完了しました！
        </v-alert>

      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    data() {
      return {
        loading: false,
        showPopup: false
      }
    },
    computed: {
      // 数量が1以上の商品だけを返す
      productsInCart() {
        return this.$store.state.product.products.filter(p => p.quantity > 0); // 名前空間の変更
      },
            totalPrice() {
        return this.$store.getters['product/totalPrice'];

      }
    },
    methods: {
      async purchase() {
        this.loading = true;
        await this.$store.dispatch('product/purchaseItems'); // 名前空間の変更
        this.loading = false;
        this.showPopup = true;
  
        // ポップアップは2秒後に消える
        setTimeout(() => {
          this.showPopup = false;
        }, 2000);
      }
    }
  }
</script>