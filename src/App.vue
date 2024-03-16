
<template>
  <div>
    <button @click="onAccept">收听 单连接</button>
    <img ref="image" src="https://th.bing.com/th?id=ODLS.3dc6f5f0-f66b-4880-893d-62d29cba6f62&w=32&h=32&qlt=93&pcl=fffffa&o=6&pid=1.2" style="background: #fff;width: 120px;"/>

    <div class="phone">
        <div v-for="(item,index) in images" :key="index">
          <img :src="item.src" alt="" class="item">
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
// import JMuxer  from 'jmuxer'
// import WSAvcPlayer from 'ws-avc-player'
// import './assets/YUVCanvas'
// import './assets/Decoder'
// import './assets/WebGLCanvas'
// import './assets/Player'
// import './assets/stream'
import { ref, onMounted, reactive, toRefs } from 'vue'

const state = reactive({
  player:null,image:null,video:null,canvas:null,ctx:null,wss:null,play:null,jmuxer:null,videoCanvas:null,
  server:`ws://localhost:9001/push/100`,
  // 接流地址
  // accept:`ws://localhost:9001/admin`,
  accept:`wss://wss1.zzxxl.cn/admin`,
  // 图片池
  images:[],
})
// 
onMounted(()=>{
  let {player,accept} = state;
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
// 绘制画布
const onDraw = ()=>{
  let {canvas,video,ctx} = state,{width,height} = canvas;
  ctx.clearRect(0,0,width,height)
  ctx.drawImage(video,0,0,width,height)
  requestAnimationFrame(onDraw);
}
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
    onImage(data)
    // jmuxer.feed({
    //   video:new Uint8Array(data)
    // })
    console.log('接收:',data)
  }
  wss.onclose = ()=>{
    console.log('接收:断开连接')
  }
}
// 展示图片
// 前8字符
const onImage = (data)=>{
  let {image,images} = state
  let slicedBuffer = data.slice(0,32)
  let decoder = new TextDecoder('utf-8');
  let uuid = decoder.decode(new Uint8Array(slicedBuffer),{stream: true}).split('$').join(''); // 

  console.log(slicedBuffer,uuid,image)
  let buff = data.slice(32,-1)
  let blob = new Blob([buff],{type:'image/jpg'})
  let src = URL.createObjectURL(blob)
  let find = images.find(item=>item.uuid === uuid)
  if(find == undefined){
    images.push({
      uuid:uuid,
      src:src
    });
  }else{
    find.src = src
    if(uuid == 139){
      image.src = src
    }
  }
  image.srcObject = blob;
  // if(images[uuid]){
  //   images[uuid].src = src
  // }else{
  //   images[uuid] = {
  //     src:src
  //   }
  // }
  // console.log(src)
  
  // image.onload = ()=>{
  //   URL.revokeObjectURL(image.src)
  // }
  // image.src = imageUrl
}
const { images,image,video,canvas,ctx,player,videoCanvas } = toRefs(state)
</script>
<style scoped>
canvas,video{
  max-width: 160px;
  /* width: 376px; */
  /* height: 666px; */
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
.item{
  max-width: 100%;
}
</style>
