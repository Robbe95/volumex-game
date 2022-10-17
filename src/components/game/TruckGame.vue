<script setup>
import * as PIXI from 'pixi.js'
import { gsap } from 'gsap'
import cone1 from '@/assets/cone-1.png'
import cone2 from '@/assets/cone-2.png'
import cone3 from '@/assets/cone-3.png'
import box1 from '@/assets/box-1.png'
import box2 from '@/assets/box-2.png'
import box3 from '@/assets/box-3.png'

import truck from '@/assets/truck.png'
import street from '@/assets/street.svg'

const pixiRender = ref()
const cones = [cone1, cone2, cone3]
const boxes = [box1, box2, box3]

const BASE_DROP_SPEED = 8
let app = null
let container = null
let player = null
let dropTicker = null
const score = ref(0)
const moveObject = (e) => {
  const { x, y } = e.data.global
  if (x < (app.renderer.width - (player.width / 2)) && x > 0)
    player.x = x
}
const congratulate = () => {
}
const followMouse = () => {
  app.stage.interactive = true
  app.stage.on('pointermove', moveObject)
}

const hitItem = (item, player) => {
  const playerBox = player.getBounds()
  const itemBox = item.getBounds()
  return playerBox.x + 75 < itemBox.x + itemBox.width
    && playerBox.x + playerBox.width - 75 > itemBox.x
    && playerBox.y + 40 < itemBox.y + itemBox.height / 2
    && playerBox.height + playerBox.y > itemBox.y
}

const setupRoad = () => {
  const streetSprite = new PIXI.Sprite(PIXI.Texture.from(street))
  const streetSpriteDuplicate = new PIXI.Sprite(PIXI.Texture.from(street))
  streetSprite.y = 0
  streetSpriteDuplicate.y = -app.renderer.height
  streetSprite.x = app.renderer.width / 2
  streetSpriteDuplicate.x = app.renderer.width / 2
  streetSprite.zIndex = -10
  streetSpriteDuplicate.zIndex = -10

  const roadTicker = new PIXI.Ticker()
  const dropSpeed = BASE_DROP_SPEED

  container.addChild(streetSprite)
  container.addChild(streetSpriteDuplicate)

  roadTicker.add((delta) => {
    streetSprite.y += dropSpeed
    streetSpriteDuplicate.y += dropSpeed
    if (streetSprite.y >= app.renderer.height)
      streetSprite.y = -streetSprite.height
    if (streetSpriteDuplicate.y >= app.renderer.height)
      streetSpriteDuplicate.y = -streetSpriteDuplicate.height
  })
  roadTicker.start()
}
const dropCone = () => {
  const randomImage = cones[Math.floor(Math.random() * cones.length)]
  const randomPosition = Math.floor(Math.random() * app.renderer.width)
  const item = new PIXI.Sprite(PIXI.Texture.from(randomImage))
  const itemTicker = new PIXI.Ticker()
  item.x = randomPosition
  item.y = -200
  item.zIndex = 1
  item.scale.set(0.4, 0.4)
  item.anchor.set(0.5, 1)
  item.rotation = Math.floor(Math.random() * 360)
  const dropSpeed = BASE_DROP_SPEED
  container.addChild(item)
  itemTicker.add((delta) => {
    if (item.y < app.renderer.height + 50)
      item.y += dropSpeed
    if (item.y >= app.renderer.height + 50) {
      itemTicker.destroy()
      container.removeChild(item)
    }
    if (hitItem(item, player)) {
      itemTicker.destroy()
      container.removeChild(item)
      score.value--
    }
  })
  itemTicker.start()
}

const dropBox = () => {
  const randomImage = boxes[Math.floor(Math.random() * boxes.length)]
  const randomPosition = Math.floor(Math.random() * app.renderer.width)
  const item = new PIXI.Sprite(PIXI.Texture.from(randomImage))
  const boxTicker = new PIXI.Ticker()
  item.x = randomPosition
  item.y = -200
  item.zIndex = 1
  item.scale.set(0.1, 0.1)
  item.anchor.set(0.5, 1)
  item.rotation = Math.floor(Math.random() * 360)
  const dropSpeed = BASE_DROP_SPEED
  container.addChild(item)
  boxTicker.add((delta) => {
    if (item.y < app.renderer.height + 50)
      item.y += dropSpeed
      // item.rotation += 0.02
      // dropSpeed += 0.2

    if (item.y >= app.renderer.height + 50) {
      boxTicker.destroy()
      container.removeChild(item)
      score.value = 0
    }
    if (hitItem(item, player)) {
      boxTicker.destroy()
      container.removeChild(item)
      score.value++
    }
  })
  boxTicker.start()
}
const loadPixi = () => {
  app = new PIXI.Application({
    backgroundAlpha: 0,
    autoResize: true,
    width: pixiRender.value.offsetWidth,
    height: pixiRender.value.offsetHeight,
  })
  pixiRender.value.appendChild(app.view)
  container = new PIXI.Container()
  container.sortableChildren = true
  app.stage.addChild(container)
  const texture = PIXI.Texture.from(truck)
  player = new PIXI.Sprite(texture)
  player.anchor.set(0.5, 1)
  player.x = app.renderer.width / 2
  player.y = app.renderer.height
  player.scale.set(0.4, 0.4)
  container.addChild(player)
  followMouse()
  dropCone()
  dropBox()

  let coneSeconds = 0
  let boxSeconds = 0

  dropTicker = new PIXI.Ticker()
  dropTicker.add((delta) => {
    coneSeconds += 1 / 60
    boxSeconds += 1 / 60

    if (coneSeconds >= 1) {
      coneSeconds = 0
      dropCone()
    }
    if (boxSeconds >= 2) {
      boxSeconds = 0
      dropBox()
    }
  })
  dropTicker.start()
}
const resize = () => {
  app.renderer.resize(pixiRender.value.offsetWidth, pixiRender.value.offsetHeight)
}
onMounted(() => {
  loadPixi()
  setupRoad()
  window.onresize = resize
})
</script>

<template>
  <div class="flex items-center justify-center flex-col h-100vh w-100vw bg-#1A1724 overflow-hidden">
    <div ref="pixiRender" class="absolute top-0 left-0 w-100vw h-screen z-10" />
    <div class="radial-background -translate-x-1/2 transform bottom-0 left-1/2 absolute w-1/2 h-1/2 " />
    <!-- <div class="full-road road-animation h-200vh w-20">
      <img ref="road" src="@/assets/street.svg" class="left-1/2 absolute transform -translate-x-1/2 h-screen -translate-y-full">
      <img ref="road" src="@/assets/street.svg" class="left-1/2 absolute transform -translate-x-1/2 h-screen">
    </div> -->
    <div class="font-bold text-white">
      Score: {{ score }}
    </div>
  </div>
</template>

<style scoped>
.radial-background {
  background: radial-gradient(47.69% 85.77% at 50% 100%, rgba(0, 80, 140, 0.3) 0%, rgba(0, 80, 140, 0) 100%) /* warning: gradient uses a rotation that is not supported by CSS and may not behave as expected */;

}

.road-animation {
  animation: road 2.3s linear infinite;
}

@keyframes road {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(100%);
  }
}
</style>
