<template>
  <div class="molecule">
    <p>
      <a-input-search class="input-search" v-model="smiles"
                      placeholder="input smiles text"
                      size="large"
                      @search="onSearch"
      >
        <template #enterButton>
          <a-button>Apply</a-button>
        </template>
      </a-input-search>
    </p>
    <div class="flex">
      <div class="left_content">
        <a-tabs :type="'card'" v-model="activeKey" @change="changeTab">
          <a-tab-pane key="ketcher" tab="Ketcher"/>
          <a-tab-pane key="jsme" tab="JSME"/>
        </a-tabs>
        <iframe class="frame" id="idKetcher" src="./standalone/index.html" width="800" height="600"
                v-show="activeKey==='ketcher'"/>
        <JSME height="600px" width="800px" options="oldlook,star,atommovebutton,hydrogens" :model-value="jsmeSmiles"
              :onChange="changeSmiles" v-show="activeKey==='jsme'"/>
      </div>
      <div class="right_content">
        <h2>搜索条件</h2>
        <a-radio-group class="radio-group" v-model="searchKey">
          <a-radio :value="1">子结构搜索</a-radio>
          <a-radio :value="2">精确搜索</a-radio>
          <a-radio :value="3">90%相似搜索</a-radio>
          <a-radio :value="4">60%相似搜索</a-radio>
          <a-radio :value="5">30%相似搜索</a-radio>
        </a-radio-group>
        <p>
          <a-button @click="queryRecords" type="primary" icon="search">
            Search Results
          </a-button>
        </p>
        <a-divider />
        <p>
          <a-button @click="getSmiles" type="primary" icon="search">
            Get SMILES
          </a-button>
        </p>
        <p style="font-size: 14px">{{ activeKey === 'ketcher' ? ketcherSmiles : jsmeSmiles }}</p>
      </div>
    </div>
    <div class="el-login-footer">
      <span
      >Copyright © 2017-{{ year }} ichengle.top
        技术支持：18556959326,微信同号.</span
      >
    </div>
  </div>

</template>

<script>
import JSME from "./JSME";
import { Modal } from 'ant-design-vue';

export default {
  name: "MoleculeIndex",
  components: {
    JSME,
  },
  data() {
    return {
      searchKey: 1,
      activeKey: 'ketcher',
      year: 2021,
      ketcher: null,
      ketcherSmiles: null,
      jsmeSmiles: null,
      currentSmiles: null,
      smiles: null,
    }
  },
  methods: {
    onSearch() {
      this.$nextTick(() => {
        this.initKetcher();
        this.ketcher.setMolecule(this.smiles)
        this.jsmeSmiles = this.smiles
      })
    },
    changeSmiles(val) {
      this.currentSmiles = val;
    },
    getYear() {
      this.year = new Date().getFullYear();
    },
    getSmiles() {
      if (this.activeKey === "ketcher") {
        if (!this.ketcher) {
          this.initKetcher();
        }
        this.ketcher
          .getSmiles()
          .then((res) => {
            this.ketcherSmiles = res;
          })
          .catch((e) => {
            console.log(e);
          });
      } else {
        this.jsmeSmiles = this.currentSmiles;
      }
    },
    changeTab() {
      this.currentSmiles = "";
      this.ketcherSmiles = "";
      this.jsmeSmiles = "";
    },
    initKetcher() {
      let ketcherFrame = document.getElementById('idKetcher');
      let ketcher = null;
      if ("contentDocument" in ketcherFrame) {
        ketcher = ketcherFrame.contentWindow.ketcher;
      } else {
        ketcher = document.frames["idKetcher"].window.ketcher;
      }
      this.ketcher = ketcher;
    },
    queryRecords() {
      Modal.info({
        title: () => '温馨提示',
        content: () => '该功能为高级版功能，请联系微信： zccbbg',
      });
    }
  },
  created() {
    this.getYear();
    this.$nextTick(() => {
      setTimeout(() => {
        this.initKetcher();
      }, 500);
    });
  },
};
</script>

<style scoped>
.molecule {
  padding: 30px;
}

.input-search {
  width: 800px;
}

.flex {
  display: flex;
}

.molecule .left_content {
  width: 800px;
}

.molecule .right_content {
  width: calc(100% - 800px);
  margin-left: 50px;
  margin-top: 50px;
}
.el-login-footer {
  height: 40px;
  line-height: 40px;
  position: fixed;
  bottom: 0;
  width: 100%;
  text-align: center;
  font-family: Arial;
  font-size: 12px;
  letter-spacing: 1px;
}

.radio-group {
  display: grid;
}

.ant-radio-wrapper {
  padding: 10px 0 !important;
}
</style>
