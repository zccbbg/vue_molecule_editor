<template>
  <div class="molecule">
    <div class="left_content">
      <a-tabs :type="'card'" v-model="activeKey" @change="changeTab">
        <a-tab-pane key="ketcher" tab="Ketcher"/>
        <a-tab-pane key="jsme" tab="JSME"/>
      </a-tabs>
      <iframe class="frame" id="idKetcher" src="./standalone/index.html" width="800" height="600" v-show="activeKey==='ketcher'"/>
      <JSME height="600px" width="800px" options="oldlook,star,atommovebutton,hydrogens" :model-value="jsmeSmiles" :onChange="changeSmiles" v-show="activeKey==='jsme'"/>
    </div>
    <div class="right_content">
      <p>
        <a-button @click="getSmiles" type="primary" icon="search">
          Get SMILES
        </a-button>
      </p>
      <p style="font-size: 14px">{{ activeKey==='ketcher'?ketcherSmiles:jsmeSmiles }}</p>
    </div>
  </div>
</template>

<script>
import JSME from "./JSME";

export default {
  name: 'MoleculeIndex',
  components: {
    JSME
  },
  data() {
    return {
      activeKey: 'ketcher',
      ketcher: null,
      ketcherSmiles: null,
      jsmeSmiles: null,
      currentSmiles: null,
    }
  },
  methods: {
    changeSmiles(val) {
      this.currentSmiles = val;
    },
    getSmiles() {
      if (this.activeKey === 'ketcher') {
        this.ketcher.getSmiles().then(res => {
          this.ketcherSmiles = res;
        }).catch(e => {
          console.log(e)
        })
      } else {
        this.jsmeSmiles = this.currentSmiles;
      }
    },
    changeTab() {
      this.currentSmiles = '';
      this.ketcherSmiles = ''
      this.jsmeSmiles = ''
    }
  },
  created() {
    this.$nextTick(() => {
      setTimeout(() => {
        let ketcherFrame = document.getElementById('idKetcher');
        let ketcher = null;
        if ('contentDocument' in ketcherFrame) {
          ketcher = ketcherFrame.contentWindow.ketcher;
        } else {
          ketcher = document.frames['idKetcher'].window.ketcher;
        }
        this.ketcher = ketcher;
      }, 500)
    })
  }
}
</script>


<style scoped>
.molecule {
  display: flex;
  padding: 30px;
}

.molecule .left_content {
  width: 800px;
}

.molecule .right_content {
  margin-left: 50px;
  margin-top: 50px;
}
</style>
