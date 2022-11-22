<template>
  <div class="listing-section">
    <div class="filter">
      <input
        type="text"
        v-model="searchKey"
        placeholder="Search By"
        @keyup="filterBy()"
      />
      <label style="margin-left: 5000px">Sort By</label>
      <a href="#" class="sort" @click="sortBy('country')">Country Name</a>
      <a href="#" class="sort" @click="sortBy('carrier')">Carrier</a>
      <a href="#" class="sort" @click="sortBy('plan')">Plan Size</a>
    </div>
    <div class="product" v-for="item of filterdList" :key="item.id">
      <div class="product-body">
        <div class="image-box">
          <img :src="imagePrefix + item.carrier.imageUrl" />
        </div>
        <div class="text-box">
          <h2 class="item">{{ item.carrier.name }}</h2>
          <h3 class="price">Plan: {{ item.plan.expiry_type }}</h3>
          <p class="description">{{ item.plan.information }}</p>
          <div>
            <label class="data-plan"
              >Data:<b>{{ item.plan.size }}{{ item.plan.unit }}</b></label
            >
            <label class="country"
              >Country:<b>{{ item.carrier.country_code }}</b></label
            >
          </div>
          <a
            class="info"
            v-if="item.plan.tnc_url"
            :href="item.plan.tnc_url"
            target="_blank"
            >Check Info</a
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
type ProductList = {
  id: number;
  plan: {
    expiry_type: string;
    information: string;
    size: number;
    unit: string;
    tnc_url: string;
  };
  carrier: {
    country_code: string;
    imageUrl: string;
    name: string;
    country_codes_iso3: string;
  };
};

export default Vue.extend({
  name: "HomeView",
  created() {
    this.getList();
  },
  data() {
    return {
      imagePrefix: "https://craterapi.com",
      productList: [] as ProductList[],
      filterdList: [] as ProductList[],
      searchKey: "",
      sortKey: "",
    };
  },
  methods: {
    sortBy(name: string) {
      //sorting
      if (name == "country") {
        this.sortKey = this.sortKey == "country" ? "" : name;
        this.filterdList = this.filterdList.sort((a, b) => {
          if (a.carrier.country_code < b.carrier.country_code) {
            return this.sortKey == "country" ? 1 : -1;
          }
          if (a.carrier.country_code > b.carrier.country_code) {
            return this.sortKey == "country" ? -1 : 1;
          }
          return 0;
        });
      } else if (name == "carrier") {
        this.sortKey = this.sortKey == "carrier" ? "" : name;
        this.filterdList = this.filterdList.sort((a, b) => {
          if (a.carrier.name < b.carrier.name) {
            return this.sortKey == "carrier" ? 1 : -1;
          }
          if (a.carrier.name > b.carrier.name) {
            return this.sortKey == "carrier" ? 1 : -1;
          }
          return 0;
        });
      } else {
        this.sortKey = this.sortKey == "plan" ? "" : name;
        this.filterdList = this.filterdList.sort((a, b) => {
          if (a.plan.size < b.plan.size) {
            return this.sortKey == "plan" ? 1 : -1;
          }
          if (a.plan.size > b.plan.size) {
            return this.sortKey == "plan" ? 1 : -1;
          }
          return 0;
        });
      }
    },
    filterBy() {
      //filter the list
      this.filterdList = this.productList.filter(
        (c) =>
          c.carrier.name.toLowerCase().includes(this.searchKey.toLowerCase()) ||
          (c.carrier.country_code
            ? c.carrier.country_code
                .toLowerCase()
                .includes(this.searchKey.toLowerCase())
            : "") ||
          (c.plan.size
            ? c.plan.size
                .toString()
                .toLowerCase()
                .includes(this.searchKey.toLowerCase())
            : "")
      );
    },
    async getList() {
      //make http call
      const settings = {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ msisdn: "" }),
      };
      //calling http method
      const response = await (
        await fetch("https://craterapi.com/api/package/search", settings)
      ).json();

      this.productList = response;
      this.filterdList = response;
    },
  },
});
</script>
<style scoped>
.listing-section,
.cart-section {
  display: flex;
  flex-wrap: wrap;
}
.image-box img {
  max-width: 150px;
}
.product {
  width: 25%;
  position: relative;
}
.description {
  font-size: 12px;
  margin: 0;
  margin-bottom: 5px;
}

.product-body {
  border: 1px solid #ccc;
  margin: 5px 5px 0px 0px;
  width: 95%;
  padding: 5px;
  min-height: 300px;
  max-height: 300px;
}
.country {
  margin-left: 20px;
}

.text-box {
  width: 100%;
  float: left;
}
.image-box {
  width: 100%;
  overflow: hidden;
}

.item,
.price {
  clear: left;
  width: 100%;
  text-align: center;
  padding: 0;
  margin-top: 5px;
}
.info {
  font-size: 13px;
  position: absolute;
  color: #1365f8;
  left: 0;
  right: 0;
  bottom: 5px;
  text-decoration: underline;
  z-index: 1;
}
.filter {
  width: 100%;
  float: left;
  margin-bottom: 20px;
  text-align: left;
}
.filter input {
  width: 30%;
  height: 30px;
}
.sort {
  margin: 0px 10px 0px 10px;
}
@media only screen and (max-width: 700px) {
  .product {
    width: 50%!important;
  }
}

@media only screen and (max-width: 450px) {
  .product {
    width: 100%!important;
  }
}
</style>
