<template lang="pug">
div
  h1 AP LADX Graphics Tools
  .flex.column.space
    label(for="rom") Link's Awakening DX English 1.0 Rom (.gbc)
    input(type="file" id="rom" name="rom" accept=".gbc" @change="readRom")
    span.red(v-if="romFile && !rom") Invalid ROM
  .flex.column.space
    label(for="img") Custom spritesheet (.png, .bin, or .bdiff)
    input(type="file" id="img" name="img" accept=".png, .bin, .bdiff" @change="readImg")
</template>

<script setup>
import { Jimp } from 'jimp'
import { ref } from 'vue'

const romFile = ref()
const rom = ref()

const readRom = event => {
  romFile.value = event.target.files[0]
  romFile.value.arrayBuffer().then(buffer => {
    let arr = new Uint8Array(buffer)
    let checksum = arr.reduce((acc, it) => acc + it, 0)
    if(arr.length == 1024*1024 && checksum == 89122269)
      rom.value = arr
    else
      rom.value = null
  })
}

const readImg = event => {
  let imgFile = event.target.files[0]
  imgFile.arrayBuffer().then(buffer => {
    let img = Jimp.fromBuffer(buffer)
    console.log(img)
  })
}

const encodeImg = bitmap => {
  let cols = bitmap.width / 8
  let rows = bitmap.height / 16
  if(cols != Math.floor(cols) || rows != Math.floor(rows))
    return
  let result = []
  for (let row=0; row<rows; row++){
    for (let col=0; col<cols; col++){
      for (let y=0; y<16; y++){
        let a = 0
        let b = 0
        for (let x=0; x<8; x++){
          let start = 4 * ((col*8+x) + cols*(row*16+y))
          let [r, g, b] = bitmap.data.slice(start, start+3)
          if(r==128)
            a |= 128 >> x
          if(g)
            b |= 128 >> x
        }
        result.push(a, b)
      }
    }
  }
  return result
}

const decodeImg = arr => {
  let rows = 256 / 8
  let cols =
}
</script>

<style lang="sass">
body
  font-family: sans-serif
.space
  margin-bottom: 24px
.flex
  display: flex
.column
  flex-direction: column
.red
  color: red
</style>
