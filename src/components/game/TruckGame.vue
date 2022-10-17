<script setup lang="ts">
import * as PIXI from 'pixi.js'
import { gsap } from 'gsap'
import type { Application, Container, Resource, Sprite, Texture, Ticker } from 'pixi.js'
import cone1 from '@/assets/cone-1.png'
import cone2 from '@/assets/cone-2.png'
import cone3 from '@/assets/cone-3.png'
import box1 from '@/assets/box-1.png'
import box2 from '@/assets/box-2.png'
import box3 from '@/assets/box-3.png'

import truck from '@/assets/truck.png'
import street from '@/assets/street.svg'

const emits = defineEmits(['gameDone'])
const pixiRender = ref()
const conesLoaded: Texture[] = []
const boxesLoaded: Texture[] = []
let truckLoaded: Texture
let streetLoaded: Texture

const BASE_DROP_SPEED = 8
let app: Application | null = null
let container: Container | null = null
let player: Sprite | null = null
let dropTicker: Ticker | null = null
const score = ref(0)

const moveObject = (e: any) => {
  if (!app || !player)
    return
  const { x, y } = e.data.global
  if (x < (app.renderer.width) && x > 0)
    player.x = x
}

const followMouse = () => {
  if (!app)
    return

  app.stage.interactive = true
  app.stage.on('pointermove', moveObject)
}

const hitItem = (item: Sprite, player: Sprite) => {
  const playerBox = player.getBounds()
  const itemBox = item.getBounds()
  return playerBox.x + 75 < itemBox.x + itemBox.width
    && playerBox.x + playerBox.width - 75 > itemBox.x
    && playerBox.y + 40 < itemBox.y + itemBox.height / 2
    && playerBox.height + playerBox.y > itemBox.y
}

const setupRoad = () => {
  if (!app || !container)
    return

  const streetSprite = new PIXI.Sprite(streetLoaded)
  const streetSpriteDuplicate = new PIXI.Sprite(streetLoaded)
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
    if (!app)
      return

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
  if (!app || !container)
    return

  const randomImage = conesLoaded[Math.floor(Math.random() * conesLoaded.length)]
  const randomPosition = Math.floor(Math.random() * app.renderer.width)
  const item = new PIXI.Sprite(randomImage)
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
    if (!app || !container || !player)
      return
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
  if (!app || !container || !player)
    return

  const randomImage = boxesLoaded[Math.floor(Math.random() * boxesLoaded.length)]
  const randomPosition = Math.floor(Math.random() * app.renderer.width)
  const item = new PIXI.Sprite(randomImage)
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
    if (!app || !container || !player)
      return

    if (item.y < app.renderer.height + 50)
      item.y += dropSpeed
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
    width: pixiRender.value.offsetWidth,
    height: pixiRender.value.offsetHeight,
  })
  pixiRender.value.appendChild(app.view)
  container = new PIXI.Container()
  container.sortableChildren = true
  app.stage.addChild(container)
  player = new PIXI.Sprite(truckLoaded)
  player.anchor.set(0.5, 1)
  player.x = app.renderer.width / 2
  player.y = app.renderer.height
  player.zIndex = 10
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
  setupRoad()
}
const resize = () => {
  if (!app)
    return

  app.renderer.resize(pixiRender.value.offsetWidth, pixiRender.value.offsetHeight)
}
const ticker = PIXI.Ticker.shared

const stopPixi = () => {
  dropTicker?.destroy()
  ticker.stop()
  app?.destroy(true)
  pixiRender.value.innerHTML = ''
}

const loadTextures = async () => {
  const box1Texture = PIXI.Texture.from(box1, { resourceOptions: { autoLoad: false } })
  const box2Texture = PIXI.Texture.from(box2, { resourceOptions: { autoLoad: false } })
  const box3Texture = PIXI.Texture.from(box3, { resourceOptions: { autoLoad: false } })

  const cone1Texture = PIXI.Texture.from(cone1, { resourceOptions: { autoLoad: false } })
  const cone2Texture = PIXI.Texture.from(cone2, { resourceOptions: { autoLoad: false } })
  const cone3Texture = PIXI.Texture.from(cone3, { resourceOptions: { autoLoad: false } })

  const truckTexture = PIXI.Texture.from(truck, { resourceOptions: { autoLoad: false } })
  const streetTexture = PIXI.Texture.from(street, { resourceOptions: { autoLoad: false } })

  await box1Texture.baseTexture.resource.load()
  await box2Texture.baseTexture.resource.load()
  await box3Texture.baseTexture.resource.load()

  await cone1Texture.baseTexture.resource.load()
  await cone2Texture.baseTexture.resource.load()
  await cone3Texture.baseTexture.resource.load()

  await truckTexture.baseTexture.resource.load()
  await streetTexture.baseTexture.resource.load()

  boxesLoaded.push(box1Texture)
  boxesLoaded.push(box2Texture)
  boxesLoaded.push(box3Texture)

  conesLoaded.push(cone1Texture)
  conesLoaded.push(cone2Texture)
  conesLoaded.push(cone3Texture)

  truckLoaded = truckTexture
  streetLoaded = streetTexture
}

onMounted(async () => {
  await loadTextures()
  loadPixi()
  window.onresize = resize
})

const stopClicked = () => {
  stopPixi()
  emits('gameDone', score.value)
}
</script>

<template>
  <div class="">
    <div ref="pixiRender" class="absolute top-0 left-0 w-100vw h-screen z-10" />
    <button class="px-4 py-2 bg-red-500 absolute top-10 right-10 z-20" @click="stopClicked">
      Stop
    </button>
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
