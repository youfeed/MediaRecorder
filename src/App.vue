
<template>
  <div>
    <button @click="onAccept" style="position: fixed;right: 10px;top: 10px;">收听 缓冲池({{ buffer.length }})</button>
    <!-- <img ref="image" src="https://th.bing.com/th?id=ODLS.3dc6f5f0-f66b-4880-893d-62d29cba6f62&w=32&h=32&qlt=93&pcl=fffffa&o=6&pid=1.2" style="background: #fff;width: 120px;"/> -->

    <div class="phone">
        <div v-for="(item) in images" :key="item.uuid">
          <YouCanvas :uuid="item.uuid" :task="item.task" :key="item.uuid"></YouCanvas>
          <!-- <img :ref="onRef(item)" :src="item.src" alt="" class="image"> -->
          <!-- <canvas  :ref="el=>{canvasRefs[item.uuid]=el}"  :data-uuid="item.uuid" class="canvas"></canvas> -->
          <!-- <canvas :ref="el=>onRef(el,item)" :data-uuid="item.uuid" class="canvas"></canvas> -->
        </div>
    </div>
    <!-- <canvas ref="canvas"></canvas>
    <video ref="video" muted autoplay></video>
    <button @click="onAccept">收听 ws://localhost:9002/@100</button>
    <button @click="onScreenshot">截图下载</button>
    <video ref="player" muted autoplay></video>
    <img ref="image" src="https://th.bing.com/th?id=ODLS.3dc6f5f0-f66b-4880-893d-62d29cba6f62&w=32&h=32&qlt=93&pcl=fffffa&o=6&pid=1.2" style="background: #f33;width: 120px;"/> -->
  </div>
</template>
<script setup>
import YouCanvas from './components/YouCanvas.vue'
// import JMuxer  from 'jmuxer'
// import WSAvcPlayer from 'ws-avc-player'
// import './assets/YUVCanvas'
// import './assets/Decoder'
// import './assets/WebGLCanvas'
// import './assets/Player'
// import './assets/stream'
import { ref, onMounted, reactive, toRefs } from 'vue'
const buffeRef = ref([])

const state = reactive({
  player:null,image:null,video:null,canvas:null,ctx:null,wss:null,play:null,jmuxer:null,videoCanvas:null,
  server:`ws://localhost:9001/push/100`,
  // 接流地址
  // accept:`ws://localhost:9001/admin`,
  accept:`wss://wss1.zzxxl.cn/admin`,
  // 图片池
  images:[],
  // 缓冲池
  buffer:buffeRef
})
// 
onMounted(()=>{
  let {player,accept} = state;
  // console.log(itemRefs)
  // onDraw();
  // state.ctx = state.canvas.getContext('2d')
  // const jmuxer = new JMuxer({
  //     node: player,
  //     mode: 'video',
  //     flushingTime:0,
  //     maxDelay:0,
  //     fps:25,
  //     debug: true
  //   });
  // state.jmuxer = jmuxer;
  // onAccept()
  // const wsavc = new WSAvcPlayer({useWorker:true})
  // wsavc.connect(accept);
  // document.body.appendChild(wsavc.AvcPlayer.canvas)
  // console.log(wsavc)
  // var play = mpegts.createPlayer({
  //     type: 'mpegts',  // could also be mpegts, m2ts, flv
  //     isLive: true,
  //     hasVideo:true,
  //     hasAudio:false,
  //     enableStashBuffer:false,
  //     liveSync:true,
  //     url: 'ws://localhost:9002/@100'
  // });
  // play.attachMediaElement(player);
  // state.play = play
  //   
  // if (navigator.mediaDevices && navigator.mediaDevices.getDisplayMedia) {
  //   navigator.mediaDevices.getDisplayMedia({ video: true }).then(stream => {
  //     const mediaRecorder = new MediaRecorder(stream, { mimeType: 'video/webm' });
  //     state.video.srcObject = stream
  //     state.ctx = state.canvas.getContext('2d')
  //     onDraw();onSocket()
  //     //
  //     mediaRecorder.ondataavailable = ()=>{
  //       onCapture()
  //       console.log(1111)
  //     }
  //     mediaRecorder.start(200);
  //   })
  //   // 
  // }
})
// 动态布局 
const onRef = (el,item)=>{
  if(item.refed != el.firstElementChild){
    item.refed = el.firstElementChild;
    if(el.firstElementChild.nodeName == 'CANVAS'){
      item.ctx = el.firstElementChild.getContext('2d');
    }
    console.log(111111111,el,item)
  }
  // console.log(222222222,el,item)
  // if(item && index && !item.ref){
  //   console.log(222222222)
  //   if(el.nodeName == 'CANVAS'){
  //     item.ctx = el.getContext('2d');
  //   }
  // }
  // if (!item[index]) {
  //   item[index].ref = el;
  // }
  // item.ref = el;
  // console.log(1,el,item,index)
}
// const onRef = (item)=>(el)=>{
//   item.ref = el;
//   if(el.nodeName == 'CANVAS'){
//     item.ctx = el.getContext('2d');
//   }
//   console.dir(item.ref)
// }
// 
const arrayBufferToBase64 = (buffer)=>{
  // console.log(buffer)
    var binary = '';
    var bytes = new Uint8Array(buffer);
    // var bytes = buffer;
    var len = bytes.byteLength;
    for (var i = 0; i < len; i++) {
        binary += String.fromCharCode(bytes[i]);
    }
    return window.btoa(binary);
  }
// 绘制画布
const onDraw = ()=>{
  let {uuid,buff} = state.buffer.shift() || {}
  // 直接渲染图片模式
  // if(uuid){
  //   let {ref} = onCanvas(uuid)
  //   var url= arrayBufferToBase64(buff);
  //   // console.log(url,buff)
  //   ref.src='data:image/jpeg;base64,'+url;
  // }
  // 画布渲染模式
  // console.dir(100000000,uuid)
  // if(uuid){
  //   let {refed,ctx} = find = onCanvas(uuid);
  //   if(refed == undefined){
  //     let canvas = canvasRefs.value[uuid]
  //     find.refed = canvas
  //     find.ctx = canvas.getContext('2d')
  //     console.log(888888888888888,find,uuid,canvas)
  //   }
  //   if(refed){
  //     console.log(100,refed.width,refed.height,ctx)
  //     // let {width,height} = ref
  //     let NImg = new Image();
  //     NImg.onload = function (e) {
  //       console.log(NImg)
  //       ctx.drawImage(NImg, 0, 0,375 * 4,667 * 4,0,0,375,667);
  //       URL.revokeObjectURL(NImg.src)
  //     }
  //     let blob = new Blob([buff],{type:'image/jpg'})
  //     NImg.src = window.URL.createObjectURL(blob)
  //     console.log('画布渲染模式',uuid)
  //   }
  // }
  // let dpr = 4;
  //   let NImg = new Image();
  //   NImg.onload = function (e) {
  //     console.log(999,this,img)
  //     ref.src = this.src
  //   //   console.log(uuid,ref.width,ref.height)
  //   //   ctx.clearRect(0, 0, ref.width, ref.height);
  //   //   ctx.drawImage(img, 0, 0,375 * dpr,667 * dpr,0,0,300,300);//,375 * dpr,667 * dpr,0,0,ref.width,ref.height
  //   //   // ctx.scale(devicePixelRatio, devicePixelRatio);
  //   //   // image.srcObject = img;
  //   //   // find.src = img.src
  //   //   URL.revokeObjectURL(img.src)
  //   }
  //   NImg.onerror = function (e) {
  //     console.log(233,e)
  //   }
  //   NImg.src = window.URL.createObjectURL(blob)
  // let {canvas,video,ctx} = state,{width,height} = canvas;
  // ctx.clearRect(0,0,width,height)
  // ctx.drawImage(video,0,0,width,height)
  // requestAnimationFrame(onDraw);
};
// 截图上传
const onCapture = ()=>{
  const {canvas,wss} = state
  canvas.toBlob(blob=>{
    wss.readyState == 1 ? wss.send(blob) : console.log(`wss - ${wss.readyState}`)
  },'image/jpg',1.0)
}
// 截图下载
const onScreenshot = ()=>{
  const {canvas} = state
  canvas.toBlob(blob=>{
    // let blob = new Blob([blob],{type:'image/jpg'})
    let a = document.createElement('a')
    a.href = URL.createObjectURL(blob)
    a.download = 'screenshot.jpg'
    a.click()
    URL.revokeObjectURL(a.href)
    
  },'image/jpg',1.0)
}
// 推送socket
const onSocket = ()=>{
  let {server} = state
  const wss = new WebSocket(server)
  wss.onopen = ()=>{
    console.log('连接成功')
  }
  wss.onmessage = (e)=>{
    console.log(e.data)
  }
  wss.onclose = ()=>{
    console.log('断开连接')
  }
  state.wss = wss
}
// 接收socket
const onAccept = ()=>{
  let {accept,jmuxer} = state
  const wss = new WebSocket(accept)
  wss.binaryType = 'arraybuffer'
  wss.onopen = ()=>{
    console.log('接收:连接成功')
    wss.send(JSON.stringify([{"type":"setAutoGetImageTimerGap","gapTime":"1.0"}]))
  }
  wss.onmessage = ({data})=>{
    if(typeof data == 'object'){
      onImage(data)
    }else{
      console.log('接收:',data)
    }
    // jmuxer.feed({
    //   video:new Uint8Array(data)
    // })
  }
  wss.onclose = ()=>{
    console.log('接收:断开连接')
  }
}
// 动态生成
const onCanvas = (uuid)=>{
  let {images,ctx} = state
  let find = images.find(item=>item.uuid === uuid)
  if(find == undefined){
    const taskRef = ref([])
    images.push({
      uuid:uuid,
      task:taskRef,
      src:'',
      ref:null
    });
    find = images.find(item=>item.uuid === uuid)
  }
  // console.log(canvasRefs)
  return find
}
// 前8字符
const onImage = (data)=>{
  let {images,buffer} = state
  let slicedBuffer = data.slice(0,32)
  let decoder = new TextDecoder('utf-8');
  let uuid = decoder.decode(new Uint8Array(slicedBuffer),{stream: true}).split('$').join(''); // 
  let buff = data.slice(32,-1)
  if(uuid){
    let {task} = onCanvas(uuid);
    // console.log('task',task)
    task.push({uuid:uuid,buff:buff})
    // buffer.push({uuid:uuid,blob:blob})
  }
}
const { images,image,video,canvas,ctx,player,videoCanvas,buffer } = toRefs(state)
</script>
<style scoped>
/* .canvas{
  width: 100%;
  height: 100%;
} */
.canvas,.image{
  background: #f3efef;
  /* width: 100%; */
  /* width: 376px; */
  /* height: 666px; */
  /* aspect-ratio: 9 / 19; */
  border: 1px solid #000;
}
video{
  object-fit: cover;
}
.phone{
  /* display: flex; */
  display: grid;
  grid-template-columns: repeat(6, 200px);
  /* align-items: center; */
}

</style>
