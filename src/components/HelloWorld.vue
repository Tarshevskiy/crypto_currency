<template>
  <v-container>
    <v-row class="text-center">
      <v-col
        style="display: flow-root; line-height: 660px"
        v-if="!data_loaded"
        cols="8"
      >
        <v-progress-circular
          indeterminate
          color="primary"
        ></v-progress-circular>
      </v-col>
      <v-col v-else cols="8">
        <highcharts
          :style="`height:` + list_height + 'px'"
          :options="chartOptions"
        ></highcharts>
      </v-col>

      <v-col ref="currency_list" cols="4">
        <v-card class="mx-auto">
          <v-list dense>
            <v-list-item-group
              dense
              mandatory
              v-model="selectedItem"
              color="primary"
            >
              <v-list-item v-for="(item, i) in items" :key="i">
                <v-row dense class="text-center" @click="draw_chart(item)">
                  <v-col cols="6">{{ item.text }}</v-col>
                  <v-col cols="6">{{ item.currency }}</v-col>
                  <!-- <v-col cols="4">3</v-col> -->
                </v-row>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Vue from "vue";
import { Chart } from "highcharts-vue";
import VueAxios from "vue-axios";
import axios from "axios";

Vue.use(VueAxios, axios);

export default {
  name: "HelloWorld",
  components: {
    highcharts: Chart,
  },

  data() {
    return {
      coin_list: [
        "BTC",
        "ETH",
        "LINK",
        "BCH",
        "LTC",
        "XRP",
        "EOS",
        "TRX",
        "USDT",
        "YFI",
        "BSV",
        "DOT",
        "BNB",
        "ADA",
        "VET",
        "XMP",
        "UNI",
      ],
      chartOptions: {
        title: {
          text: "Updated every 10 seconds",
        },
        series: [
          {
            name: "Currency",
            data: [],
          },
        ],
        xAxis: {
          type: "datetime",
          crosshair: true,

          title: {
            text: "Period",
          },
        },
        yAxis: {
          crosshair: true,
          title: {
            text: "Currency",
          },
        },
      },
      selectedItem: 0,
      items: [],
      list_height: 660,
      data_loaded: false,
    };
  },
  async created() {
    let ans = await this.get_list_data();
    this.make_list(ans.data);

    this.draw_chart(this.items[0]);
  },
  methods: {
    get_period_hour_data(coin) {
      try {
        return axios.get(
          `https://min-api.cryptocompare.com/data/v2/histominute?fsym=${coin}&tsym=USD&limit=10`
        );
      } catch (err) {
        console.log("err", err);
      }
    },
    get_list_data() {
      try {
        return axios.get(
          `https://min-api.cryptocompare.com/data/pricemulti?fsyms=${this.coin_list}&tsyms=USD`
        );
      } catch (err) {
        console.log("err", err);
      }
    },
    get_now_data(selected_coin) {
      try {
        return axios.get(
          `https://min-api.cryptocompare.com/data/price?fsym=${selected_coin}&tsyms=USD`
        );
      } catch (err) {
        console.log("err", err);
      }
    },
    async draw_chart(item) {
      this.data_loaded = false;

      this.chartOptions.series[0].data = [];
      let data = await this.get_period_hour_data(item.coin);

      data.data.Data.Data.forEach((e) => {
        this.chartOptions.series[0].data.push({
          x: new Date(e.time * 1000),
          y: e.open,
        });
      });

      for (let i = 0; i < 10000; i++) {
        window.clearInterval(i);
      }

      window.setInterval(async () => {
        console.log("test");
        let res = await this.get_now_data(item.coin);
        this.chartOptions.series[0].data.push({
          x: new Date(),
          y: res.data.USD,
        });
      }, 1000 * 10);
      this.data_loaded = true;
    },
    make_list(data) {
      this.items = [];
      Object.entries(data).forEach((e) => {
        const [key, val] = e;
        val;
        this.items.push({ text: key + "/USD", currency: val.USD, coin: key });
      });
    },
  },
};
</script>
