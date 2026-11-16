<template>
  <ion-page>

    <ion-header>
      <ion-toolbar>
        <ion-title>Cryptocurrency</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">

      <!-- Tombol Refresh -->
      <div class="refresh-container">
        <ion-button
          @click="getCrypto"
          :disabled="loading"
        >
          Refresh
        </ion-button>
      </div>

      <!-- Loading -->
      <div v-if="loading" class="status-container">
        <ion-spinner name="crescent"></ion-spinner>
        <p>Mengambil data...</p>
      </div>

      <!-- Error -->
      <div
        v-else-if="errorMessage"
        class="status-container error"
      >
        {{ errorMessage }}
      </div>

      <!-- Daftar Cryptocurrency -->
      <ion-list v-else class="crypto-list">

        <ion-item
          v-for="coin in coins"
          :key="coin.id"
        >

          <div class="crypto-row">

            <!-- Rank -->
            <div class="rank-column">
              <span class="label">Rank</span>

              <strong>
                {{ coin.rank }}
              </strong>
            </div>

            <!-- Nama dan Symbol -->
            <div class="name-column">

              <span class="label">
                {{ coin.name }}
              </span>

              <strong>
                {{ coin.symbol }}
              </strong>

            </div>

            <!-- Harga -->
            <div class="price-column">

              <span class="label">
                USD
              </span>

              <strong>
                {{ formatPrice(coin.price_usd) }}
              </strong>

            </div>

          </div>

        </ion-item>

      </ion-list>

    </ion-content>

  </ion-page>
</template>


<script lang="ts">

import { defineComponent } from 'vue';

import axios from 'axios';

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButton,
  IonList,
  IonItem,
  IonSpinner
} from '@ionic/vue';


interface Coin {

  id: string;

  rank: number;

  name: string;

  symbol: string;

  price_usd: string;

}


export default defineComponent({

  name: 'HomePage',

  components: {

    IonPage,
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonButton,
    IonList,
    IonItem,
    IonSpinner

  },


  data() {

    return {

      coins: [] as Coin[],

      loading: false,

      errorMessage: ''

    };

  },


  mounted() {

    this.getCrypto();

  },


  methods: {

    async getCrypto() {

      this.loading = true;

      this.errorMessage = '';

      try {

        const response = await axios.get(
          'https://api.coinlore.net/api/tickers/'
        );

        this.coins = response.data.data.slice(0, 10);

      }

      catch (error) {

        console.error(error);

        this.errorMessage =
          'Gagal mengambil data cryptocurrency.';

      }

      finally {

        this.loading = false;

      }

    },


    formatPrice(price: string) {

      return Number(price).toLocaleString(
        'en-US',
        {
          minimumFractionDigits: 2,
          maximumFractionDigits: 6
        }
      );

    }

  }

});

</script>


<style scoped>

ion-content {

  --background: #f4f5f8;

}


.refresh-container {

  display: flex;

  justify-content: center;

  margin-top: 20px;

  margin-bottom: 20px;

}


.crypto-list {

  max-width: 450px;

  margin: auto;

  border:

    1px solid #d8c987;

}


ion-item {

  --background: #fff7d6;

  --border-color: #d8c987;

}


.crypto-row {

  width: 100%;

  display: grid;

  grid-template-columns:
    70px 1fr 120px;

  align-items: center;

  padding: 8px 0;

}


.rank-column,
.name-column,
.price-column {

  display: flex;

  flex-direction: column;

}


.label {

  font-size: 11px;

  color: #555;

  margin-bottom: 3px;

}


.rank-column strong {

  font-size: 22px;

}


.name-column strong {

  font-size: 20px;

}


.price-column strong {

  font-size: 17px;

}


.status-container {

  text-align: center;

  margin-top: 30px;

}


.error {

  color: #d00000;

}


@media
(max-width: 400px) {

  .crypto-row {

    grid-template-columns:
      55px 1fr 105px;

  }

  .price-column strong {

    font-size: 14px;

  }

}

</style>