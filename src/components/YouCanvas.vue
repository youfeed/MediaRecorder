<template>
  <div style="border: 1px solid #000;">
    <canvas class="canvas" ref="canvas"></canvas>
    <img class="image" ref="image">
    <div class="cache">{{uuid}} 缓冲:{{tasks.length}}</div>
  </div>
</template>

<script setup>
import { onMounted,reactive, toRefs } from 'vue';
const props = defineProps({
  uuid: String,
  task: Array,
});
const state = reactive({
  uuid:null,
  image:null,
  canvas:null,
  ctx:null,
  tasks:[]
})
onMounted(()=>{
  state.uuid = props.uuid;
  state.tasks = props.task;
  state.ctx = state.canvas.getContext('2d');
  // console.log(1000000000000000000,state.tasks,props.task)
  onDraw();
})
const onDraw = ()=>{
  let {image,canvas,ctx,tasks} = state
  let {uuid,buff} = tasks.shift() || {}
  // 直接渲染图片模式
  // if(uuid){
  //   let {ref} = onCanvas(uuid)
  //   var url= arrayBufferToBase64(buff);
  //   // console.log(url,buff)
  //   ref.src='data:image/jpeg;base64,'+url;
  // }
  // 画布渲染模式
  // console.dir(100000000,uuid)
  if(uuid){
    let {width,height} = canvas
    // console.log(100,canvas.width,canvas.height,ctx)
    let NImg = new Image();
    NImg.onload = function (e) {
      // console.log(NImg)
      ctx.drawImage(NImg,0,0);// , 0, 0,375 * 4,667 * 4,0,0,width,height
      image.src = NImg.src
      image.onload = function (e) {
        // console.log('imageimageimage',uuid)
      }
      URL.revokeObjectURL(NImg.src)
      requestAnimationFrame(onDraw);
    }
    let blob = new Blob([buff],{type:'image/jpg'})
    NImg.src = window.URL.createObjectURL(blob)
    // console.log('画布渲染模式',uuid)
  }else{
    requestAnimationFrame(onDraw);
  }
  
};
const {uuid,canvas,image,tasks} = toRefs(state)
</script>

<style scoped>
.canvas{
  max-width: 100%;
  
}
.image{
  max-width: 100%;
}
.cache{
  text-align: center;
}
</style>