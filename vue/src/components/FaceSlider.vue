<script setup lang="ts">
import { onMounted } from 'vue'

interface FaceState {
  happiness: number
  derp: number
  px: number
  py: number
}

interface UIState {
  btnHappyText?: string
  btnUnhappyText?: string
  titleText?: string
  subtitleText?: string
}

interface ConfigState {
  face: FaceState
  ui: UIState
}

onMounted(() => {
  const container = document.querySelector('.container') as HTMLElement
  const face = document.querySelector('.face-slider') as HTMLElement
  const btnHappy = document.querySelector('.button-happy') as HTMLElement
  const btnUnhappy = document.querySelector('.button-unhappy') as HTMLElement
  const title = document.querySelector('.title') as HTMLElement
  const subtitle = document.querySelector('.subtitle') as HTMLElement

  const config: {
    maxUnhappyCount: number
    animationSpeed: number
    states: {
      normal: ConfigState
      happy: ConfigState
      unhappy: ConfigState
    }
  } = {
    maxUnhappyCount: 3,
    animationSpeed: 0.1,
    states: {
      normal: {
        face: { happiness: 0.9, derp: 1, px: 0.5, py: 0.5 },
        ui: {
          btnHappyText: btnHappy.innerHTML,
          btnUnhappyText: btnUnhappy.innerHTML,
          titleText: title.innerHTML,
          subtitleText: subtitle.innerHTML
        }
      },
      happy: {
        face: { happiness: 1, derp: 0, px: 0.5, py: 0.5 },
        ui: {
          btnHappyText: '❤️',
          titleText: '太好了！',
          subtitleText: '我们在一起吧！'
        }
      },
      unhappy: {
        face: { happiness: 0.2, derp: 0, px: 0.5, py: 0.5 },
        ui: {
          btnUnhappyText: '再考虑一下',
          titleText: '不要拒绝我嘛',
          subtitleText: '再给我一次机会好不好'
        }
      }
    }
  }

  const state: {
    rejectCount: number
    animationId: number | null
    current: FaceState
    target: FaceState
  } = {
    rejectCount: 0,
    animationId: null,
    current: { ...config.states.normal.face },
    target: { ...config.states.normal.face }
  }

  function updateFaceCSS(): void {
    Object.entries(state.current).forEach(([prop, value]) => {
      face.style.setProperty(`--${prop}`, value.toString())
    })
  }

  function transitionToState(stateType: keyof typeof config.states, hideButton: HTMLElement | null = null): void {
    const targetState = config.states[stateType]

    if (targetState.face) {
      Object.assign(state.current, targetState.face)
    }

    if (targetState.ui) {
      const { btnHappyText, btnUnhappyText, titleText, subtitleText } = targetState.ui

      if (btnHappyText) btnHappy.innerHTML = btnHappyText
      if (btnUnhappyText) btnUnhappy.innerHTML = btnUnhappyText
      if (titleText) title.innerHTML = titleText
      if (subtitleText) subtitle.innerHTML = subtitleText
    }

    if (hideButton) {
      hideButton.style.visibility = 'hidden'
    } else {
      btnHappy.style.visibility = 'visible'
      btnUnhappy.style.visibility = 'visible'
      btnUnhappy.style.position = 'static'
      btnUnhappy.style.left = ''
      btnUnhappy.style.top = ''
      btnHappy.style.transform = 'scale(1)'
    }
    updateFaceCSS()
  }

  function stopAnimation(): void {
    if (state.animationId) {
      cancelAnimationFrame(state.animationId)
      state.animationId = null
    }
  }

  function startAnimation(): void {
    function updateFace(): void {
      let needsUpdate = false

      for (const prop in state.target) {
        if (state.target[prop as keyof FaceState] === state.current[prop as keyof FaceState]) continue

        needsUpdate = true
        if (Math.abs(state.target[prop as keyof FaceState] - state.current[prop as keyof FaceState]) < 0.01) {
          state.current[prop as keyof FaceState] = state.target[prop as keyof FaceState]
        } else {
          state.current[prop as keyof FaceState] +=
            (state.target[prop as keyof FaceState] - state.current[prop as keyof FaceState]) * config.animationSpeed
        }
      }

      if (needsUpdate) {
        updateFaceCSS()
      }

      state.animationId = requestAnimationFrame(updateFace)
    }

    updateFace()
  }

  container.addEventListener('mousemove', ({ clientX: x, clientY: y }: MouseEvent) => {
    const unhappyRect = btnUnhappy.getBoundingClientRect()
    const happyRect = btnHappy.getBoundingClientRect()
    const containerRect = container.getBoundingClientRect()

    const dx1 = x - (unhappyRect.x + unhappyRect.width * 0.5)
    const dy1 = y - (unhappyRect.y + unhappyRect.height * 0.5)
    const dx2 = x - (happyRect.x + happyRect.width * 0.5)
    const dy2 = y - (happyRect.y + happyRect.height * 0.5)

    const px = (x - containerRect.x) / containerRect.width
    const py = (y - containerRect.y) / containerRect.height

    const distUnhappy = Math.sqrt(dx1 * dx1 + dy1 * dy1)
    const distHappy = Math.sqrt(dx2 * dx2 + dy2 * dy2)
    const happiness = Math.pow(distUnhappy / (distHappy + distUnhappy), 0.75)

    state.target = { ...state.target, happiness, derp: 0, px, py }
  })

  container.addEventListener('mouseleave', () => {
    state.target = { ...config.states.normal.face }
  })

  btnHappy.addEventListener('click', () => {
    if (state.animationId) {
      btnHappy.style.transform = 'scale(1)'
      stopAnimation()
      transitionToState('happy', btnUnhappy)
    } else {
      state.rejectCount = 0
      transitionToState('normal')
      startAnimation()
    }
  })

  btnUnhappy.addEventListener('click', () => {
    if (state.animationId) {
      state.rejectCount++

      if (state.rejectCount >= config.maxUnhappyCount) {
        stopAnimation()
        transitionToState('unhappy', btnHappy)
      } else {
        btnUnhappy.style.position = 'absolute'
        btnUnhappy.style.left = `${Math.random() * 80}%`
        btnUnhappy.style.top = `${Math.random() * 80}%`
        state.target.happiness = Math.max(0.1, state.target.happiness - 0.1)
        btnHappy.style.transform = `scale(${1 + state.rejectCount * 0.2})`
      }
    } else {
      state.rejectCount = 0
      transitionToState('normal')
      startAnimation()
    }
  })

  startAnimation()
})
</script>

<template>
  <section class="container">
    <div class="content">
      <h1 class="title">我喜欢你</h1>
      <h2 class="subtitle">可以和我在一起吗？</h2>
      <figure class="face-slider">
        <div class="face-slider-blush face-slider-blush-left"></div>
        <div class="face-slider-blush face-slider-blush-right"></div>
        <div class="face-slider-eye face-slider-eye-left"></div>
        <div class="face-slider-eye face-slider-eye-right"></div>
        <div class="face-slider-mouth"></div>
      </figure>
      <button class="button button-happy">好的</button>
      <button class="button button-unhappy">再想想</button>
    </div>
  </section>
</template>

<style scoped>
* {
  box-sizing: border-box;
  font: inherit;
}

html {
  color: #ff4d6d;
  font-size: 62.5%;
  background: linear-gradient(135deg, #ffccd5, #ff8fa3);
}

@media screen and (max-width: 480px) {
  html {
    font-size: 50%;
  }
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
  padding: 0;
  margin: 0;
  width: 100vw;
  height: 100vh;
  font-family: 'Rubik', sans-serif;
}

.container {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  width: 72rem;
  max-width: 100%;
  min-width: 34rem;
  height: 50rem;
  max-height: 100%;
  min-height: 46rem;
  background-color: #ffffff;
  border-radius: 1rem;
  border: 1px solid #ffccd5;
  box-shadow: 0 8px 24px rgba(255, 77, 109, 0.2);
}

.content {
  flex: 1;
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  position: relative;
  margin: 2rem 4rem;
}

.title {
  margin: 0;
  padding: 0;
  position: absolute;
  top: 5%;
  text-align: center;
  width: 100%;
}

.subtitle {
  margin: 0;
  padding: 0;
  position: absolute;
  top: 15%;
  text-align: center;
  width: 100%;
  font-size: 1.5rem;
}

.button {
  color: #fff;
  border-radius: 1rem;
  text-decoration: none;
  padding: 1rem 2rem;
  margin-bottom: 1rem;
  min-width: 10rem;
  text-align: center;
  transition: background-color 0.3s, transform 0.3s;
}

.button-happy {
  background-color: #ff4d6d;
  border: none;
}

.button-happy:hover {
  background-color: #ff758f;
}

.button-unhappy {
  background-color: #ff8fa3;
  border: none;
}

.button-unhappy:hover {
  background-color: #ffb3c1;
}

.face-slider {
  --happiness: 0.9;
  --derp: 1;
  --px: 0.5;
  --py: 0.5;
  width: 22rem;
  max-width: 100%;
  height: 22rem;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-image: radial-gradient(#ffccd5, #ff4d6d);
  border-radius: 100%;
  overflow: hidden;
  margin: 0;
  align-self: center;
  flex: 0 0 auto;
  border: solid 2px #ff758f;
  box-shadow: 0px 0px 10px rgba(255, 77, 109, 0.3);
}

.face-slider,
.face-slider * {
  position: absolute;
}

.face-slider::before {
  content: '';
  display: block;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background-image: linear-gradient(to bottom, #ff4d6d, rgba(255, 77, 109, 0));
  opacity: calc(1 - var(--happiness));
}

.face-slider-blush {
  width: 20%;
  height: 10%;
  background-color: rgba(255, 100, 100, 0.3);
  border: 3px solid rgba(255, 100, 100, 0.3);
  top: calc(45% + var(--py) * 10%);
  border-radius: 100%;
  opacity: calc(var(--happiness) * var(--happiness) * 0.9 + 0.1);
}

.face-slider-blush-left {
  left: calc(7% + var(--px) * 2%);
}

.face-slider-blush-right {
  right: calc(9% - var(--px) * 2%);
}

.face-slider-eye {
  width: calc(26% - var(--happiness) * 2%);
  height: calc(26% - var(--happiness) * 2%);
  background-color: #f6f6f6;
  border-radius: 100%;
  top: calc(25% + var(--py) * 10%);
  overflow: hidden;
}

.face-slider-eye-left {
  left: calc(18% + var(--px) * 4%);
}

.face-slider-eye-left::after {
  transform: translate(calc((var(--px) + var(--derp) * 0.5) * 100%), calc((var(--py) + var(--derp) * 0.5) * 100%));
}

.face-slider-eye-right {
  right: calc(22% - var(--px) * 4%);
}

.face-slider-eye-right::after {
  transform: translate(calc((var(--px) + var(--derp) * -0.3) * 100%), calc((var(--py) + var(--derp) * -0.3) * 100%));
}

.face-slider-eye::after {
  content: '';
  display: block;
  background-color: #421;
  width: calc(55% - var(--happiness) * 10%);
  height: calc(55% - var(--happiness) * 10%);
  border-radius: 100%;
}

.face-slider-mouth {
  width: calc(51% - var(--happiness) * 2%);
  height: calc(26% - var(--happiness) * 2%);
  background-color: #ff4d6d;
  border-radius: calc((1 - var(--happiness)) * 10em) calc((1 - var(--happiness)) * 10em) calc(var(--happiness) * 16em) calc(var(--happiness) * 16em);
  top: calc(57.5% + var(--py) * 5%);
  left: calc(47.5% + var(--px) * 5%);
  transform: translateX(-50%);
  overflow: hidden;
  border: 3px solid #ff758f;
}

.face-slider-mouth::before {
  content: '';
  display: block;
  position: absolute;
  width: 20%;
  height: 20%;
  top: 0;
  left: 50%;
  background-color: white;
  border-radius: 0 0 0.5rem 0.5rem;
}

.face-slider-mouth::after {
  content: '';
  display: block;
  position: absolute;
  width: 60%;
  height: 50%;
  left: 10%;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 20rem 20rem 0 0;
}
</style>
