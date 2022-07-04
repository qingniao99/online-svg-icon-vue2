<template>
  <div class="online-svg-wrap">
      <div class="icon" :style="genStyle"></div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'onlineSvgIcon',
  props: {
    url: String
  },
  data() {
    return {
      svg: '',
    }
  },
  computed: {
    genStyle() {
        if (!this.svg) return {};
        const mode = this.svg.includes('currentColor')
          ? 'mask'
          : 'background-img'

        const uri = `url("data:image/svg+xml;utf8,${this.encodeSvg(this.svg)}")`
        // monochrome
        if (mode === 'mask') {
          return {
            'background-color': 'currentColor',
            'height': '1em',
            'width': '1em',
            'mask': `${uri} no-repeat`,
            '-webkit-mask': `${uri} no-repeat`,
            'mask-size': '100% 100%',
            '-webkit-mask-size': '100% 100%',
          }
        }
        // colored
        else {
          return {
            'background': `${uri} no-repeat`,
            'background-size': '100% 100%',
            'background-color': 'transparent',
            'height': '1em',
            'width': '1em',
          }
        }
      },
  },
  watch: {
    'url'() {
      this.getSvg();
    }
  },
  mounted() {
    this.getSvg();  
  },
  methods: {
    async getSvg() {
      const res = await axios.get(this.url);
      this.svg = res.data;
    },
    encodeSvg(svgString) {
        return svgString.replace('<svg',(~svgString.indexOf('xmlns')?'<svg':'<svg xmlns="http://www.w3.org/2000/svg"'))
        //
        //   Encode (may need a few extra replacements)
        //
        .replace(/"/g, '\'')
        .replace(/%/g, '%25')
        .replace(/#/g, '%23')       
        .replace(/{/g, '%7B')
        .replace(/}/g, '%7D')         
        .replace(/</g, '%3C')
        .replace(/>/g, '%3E')

        .replace(/\s+/g,' ');
      }
  },
}
</script>
<style scoped>
.online-svg-wrap {
  display: inline-block;
}
</style>
