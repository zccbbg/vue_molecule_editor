<template>
  <div>
    <div
        :style="{
        width: width,
        height: height,
        outline: jsmeIsLoadedInternal ? '' : '1px solid black',
        outlineOffset: '-.5px',
        textAlign: 'center',
      }"
    >
      <div :id="id"></div>
      <div
          v-if="!jsmeIsLoadedInternal"
          :style="{
          position: 'absolute',
          top: '50%',
          left: '50%',
          transform: 'translate(-50%, -50%)',
          '-webkit-transform': 'translate(-50%, -50%)',
        }"
      >
<!--        JSME is loading, or JS is disabled-->
      </div>
    </div>
    <div>
      <div v-if="jsmeIsLoadedInternal">
        <div
            style="
            width: 100%;
            display: flex;
            flex-direction: row;
            flex-wrap: nowrap;
          "
        >
<!--          <span>Smiles:</span>-->
<!--          <div style="width: 100%">-->
<!--            <input-->
<!--                type="text"-->
<!--                placeholder="Enter Smiles"-->
<!--                style="width: 98%"-->
<!--                :value="modelValue"-->
<!--                v-on:input="(e) => updateValue(e)"-->
<!--                v-on:change="(e) => updateValue(e)"-->
<!--            />-->
<!--          </div>-->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      finalSrc: "jsme/jsme.nocache.js",
      jsmeIsLoadedInternal: false,
      JSA: null
    }
  },
  props: {
    width: {type: String, default: "800px"},
    height: {type: String, default: "500px"},
    id: {type: String, default: "JME" + Math.random()},
    options: {type: String, default: ""},
    onChange: {type: Function, required: false},
    src: {type: String, default: ""},
    border: {type: String, default: "5px solid black"},
    code: {type: String, default: "JME.class"},
    name: {type: String, default: "JME" + Math.random()},
    archive: {type: String, default: "JME.jar"},
    modelValue: {type: String, default: ""},
  },
  created() {
    const newScript = document.createElement("script");
    newScript.type = "text/javascript";
    newScript.src = this.src ? this.src : this.finalSrc;
    document.head.appendChild(newScript);
    window.jsmeOnLoad = () => {
      let JSA = new window.JSApplet.JSME(this.id,this.width,  this.height, {
        "options" : this.options
      });
      JSA.readGenericMolecularInput(this.modelValue);
      JSA.setCallBack("AfterStructureModified", (e) => {
        const newSmiles = e.src.smiles();
        this.updateValue(newSmiles);
        if (this.onChange) {
          this.onChange(newSmiles);
        }
      });
      this.JSA = JSA;
      this.jsmeIsLoadedInternal = true;
    };
  },
  watch: {
    modelValue: function (newValue, oldValue) {
      console.log(oldValue)
      this.JSA.readGenericMolecularInput(newValue);
    }
  },
  methods: {
    updateValue(inboundValue) {
      let value = "";
      if (!!inboundValue && inboundValue instanceof Event) {
        value = inboundValue.target;
      } else {
        value = inboundValue;
      }
      this.$emit("updateValue", value);
    }
  }
}
</script>