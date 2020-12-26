<template>
  <div class="app py-3">
    <div class="container">
      <div class="row" v-show="image_text">
        
        <div class="col-12 col-sm-6">
          <canvas ref="canvas" />
        </div>
        <div class="col-12 col-sm-6">
          <pre
            v-html="image_text"
          />
        </div>

      </div>
      <div class="row">
        
        <div class="col-12  mt-3">
          <div class="mb-3">
            <label for="formFile" class="form-label">Select an image</label>
            <input class="form-control" type="file" id="formFile" accept="image/*" @input="onChangeFile($event.target)" ref="file">
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return {
      image_html_obj: '',
      image_text: '',
    }
  },
  methods: {

    onChangeFile(e){
      let file = e.files[0];

      let reader = new FileReader();

      reader.onload = (e) => {
        this.drawOnCanvas(e.target.result)
      }

      reader.readAsDataURL(file)
    },

    drawOnCanvas(img_base64){
      let canvas = this.$refs.canvas
      let ctx = canvas.getContext("2d")

      let img = new Image()
      img.src = img_base64
      img.onload = () => {
        let max_width = 110

        let width = img.width
        let height = img.height

        if (img.width > max_width) {
          width = max_width
          let width_percent = 100 / img.width * width
          height = img.height / 100 * width_percent
        }

        canvas.width = width
        canvas.height = height
        ctx.drawImage(img, 0, 0, canvas.width, canvas.height)

        this.drawAsText(ctx)
      }

    },

    drawAsText(ctx){
      let imgData = ctx.getImageData(0, 0, ctx.canvas.width, ctx.canvas.height)
      let width_origin = ctx.canvas.width
      let width = width_origin

      this.image_text = ''
      for(let i = 0 + 4 - 4, len = imgData.data.length; i < len; i += 4){
        let cellNumber = (imgData.data[i + 0] + imgData.data[i + 1] + imgData.data[i + 2]) / 3
        cellNumber = 256 - cellNumber

        let symbol = ' ' //  ░▒▓█
        if (cellNumber > 51.2 * 1) {
          symbol = '░'
        }
        if (cellNumber > 51.2 * 2) {
          symbol = '▒'
        }
        if (cellNumber > 51.2 * 3) {
          symbol = '▓'
        }
        if (cellNumber > 51.2 * 4) {
          symbol = '█'
        }

        /*if (cellNumber >= 256 / 2) {
          symbol = '#'
        }*/

        if (--width) {
          this.image_text += `${symbol} `
        } else {
          this.image_text += `${symbol} <br/>`
          width = width_origin
        }
      }
    },

  },
}
</script>

<style lang="scss">

.app {
}

canvas {
  box-shadow: 0 0 10px rgba(0,0,0,.1);
  width: 100%;
  height: auto;
}

pre {
  font-family: monospace;
  line-height: 1.125;
  user-select: all;
  font-size: 5px;
}

</style>
