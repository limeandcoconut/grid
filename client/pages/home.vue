<template>
  <div class="layout">
    <div/>
    <div id="canvas-container"/>
    <div/>
  </div>
</template>

<script>
import {
  title,
  author,
  description,
  host,
  color,
  og,
  twitter,
  ms,
  manifest,
  favicons,

} from '../../config/meta.config'
import { Application, Graphics } from 'pixi.js'
import { PolygonConvex2D, Circle2D, contact } from 'contact'
import { Vector2D } from 'friendly-vectors'

export default {
  name: 'home',

  metaInfo: {
    title,
    meta: [
      { name: 'description', content: description },
      { name: 'author', content: author },
      { property: 'og:title', content: title },
      { property: 'og:description', content: description },
      { property: 'og:image', content: og.image.src },
      { property: 'og:image:width', content: og.image.width },
      { property: 'og:image:height', content: og.image.height },
      { property: 'og:type', content: og.type },
      { property: 'og:url', content: host },
      { property: 'twitter:image', content: twitter.image.src },
      { property: 'twitter:image:alt', content: twitter.image.alt },
      { property: 'twitter:card', content: twitter.card },
      { name: 'twitter:creator', content: twitter.creator },
      { name: 'theme-color', content: color },
      { name: 'msapplication-TileImage', content: favicons.ms },
      { name: 'msapplication-TileColor', content: ms.color },
    ],
    link: [
      { rel: 'canonical', href: 'https://jacobsmith.tech' },
      { rel: 'shortcut icon', href: favicons.default },
      { rel: 'icon', href: favicons.x32, sizes: '32x32', type: 'image/png' },
      { rel: 'icon', href: favicons.x16, sizes: '16x16', type: 'image/png' },
      { rel: 'apple-touch-icon', href: favicons.apple, sizes: '180x180' },
      { rel: 'mask-icon', href: favicons.safariMask, color: color },
      { rel: 'icon', href: favicons.ms },
      { rel: 'manifest', href: manifest },
      // noindex, nofollow can go here too
    ],
  },

  data() {

    return {
      app: null,
      graphics: new Graphics(),
      polygon: new PolygonConvex2D([
        600, 370,
        700, 370,
        750, 600,
      ]),
      polygon2: new PolygonConvex2D([
        380, 350,
        330, 250,
        580, 350,
      ]), // .translate(new Vector2D(
      //   380,
      //   250,
      // ))

      velocity: new Vector2D(
        0,
        -1200,
      ),
      velocity2: new Vector2D(
        800,
        0,
      ),
      timer: null,
      counter: 0,
    }
  },

  mounted() {
    this.app = new Application({
      antialias: true,
      transparent: true,
      // backgroundColor: 0xB6471F,
      // resolution: window.devicePixelRatio || 1,
    })
    document.querySelector('#canvas-container').append(this.app.view)

    this.timer = setInterval(this.tick, 1000)
    // this.tick()
  },
  methods: {
    tick() {
      this.counter++
      if (this.counter > 11) {
        clearInterval(this.timer)
        return
      }
      let { graphics, polygon, polygon2, velocity, velocity2 } = this
      const collision = contact(
        polygon,
        polygon2,
        velocity,
        velocity2,
      )
      console.log(collision)

      console.log('COUNTER\n')
      console.log(this.counter)
      console.log('COUNTER\n')

      if (collision === true) {
        clearInterval(this.timer)
        return
      } else if (collision && collision !== 1) {
        velocity = velocity.scale(collision)
        velocity2 = velocity2.scale(collision)
      }

      this.polygon.translate(velocity)
      this.polygon2.translate(velocity2)

      graphics.clear()
      // Draw polygon
      graphics.lineStyle(0)
      graphics.beginFill(0x00ABA9, 1)
      graphics.drawPolygon(this.polygon.toArray())
      graphics.endFill()

      graphics.beginFill(!collision ? 0x00ABA9 : 0x00FFCB, 1)
      graphics.drawPolygon(this.polygon2.toArray())
      graphics.endFill()

      this.app.stage.addChild(graphics)
      console.log(polygon.toArray())
      if ((collision && collision !== 1) || (this.counter > 10)) {
        clearInterval(this.timer)
      }
    },
  },
}
</script>
<style lang="less" scoped>
@import '../styles/mixins.less';

.layout {
  background-color: white;
  display: grid;
  grid-template: auto / 1fr 10fr 1fr;
  align-items: center;
  align-content: center;
  justify-items: center;
  justify-content: center;
  height: 100vh;
  perspective: 60em;
  font-size: .fz(venti)[];
  .oxanium(500);

  .above(md; {
    grid-template: auto / 1fr .basis(80)[] 1fr;
  });

  .above(lg; {
    grid-template: auto / 1fr .basis(70)[] 1fr;
  });
}

#canvas-container {
  border: 2px solid @mars;
}

</style>
