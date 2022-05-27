<template>
  <div class="prose max-w-none min-h-screen pt-5 flex flex-col">
    <h1 class="text-center flex-grow-0">
      🧙 Image Wizard
    </h1>
    <div
      class="drop-area bg-slate-400 flex-grow mb-10"
      :class="{ 'bg-red-300': dragActive }"
    >
      I exists, believe bro {{ dragActive }}

      <div class="relative flex flex-col">
        <div
          v-for="image in images"
          :key="image.name"
          class="image w-5/6 flex content-center justify-center items-center self-center flex-col"
        >
          <h2>{{ image.name }}</h2>
          <img :src="image.src">
        </div>
      </div>
    </div>
  </div>
  <!--div class="prose max-w-none">
    <h1>{{ msg }}</h1>
    <p class="lead">
      An opinionated Vue 3, TypeScript, Tailwind CSS and ESLint template.
    </p>
    <p>
      View this project on <a
        href="https://github.com/vincentdoerig/vue3-typescript-tailwind-starter"
      >GitHub</a>. It uses <a href="https://github.com/vitejs/vite">vite</a> to provide a fast development experience with hot module replacement. Try it out by editing <code>components/HelloWorld.vue</code>.
    </p>
    <p>
      Vue router is also included and configured, you could try navigating to <router-link to="/about">
        another page
      </router-link> or to a page that
      <router-link to="/foo">
        does not exist
      </router-link>.
    </p>
    <h2>Installing</h2>
    <p>To quickly get started, enter a project name and run the commands below.</p>
    <div>
      <label
        for="project-name"
        class="block text-sm font-medium leading-5 text-gray-700"
      >Project name</label>
      <div class="relative max-w-xs mt-1 rounded-md shadow-sm">
        <input
          id="project-name"
          v-model="name"
          type="text"
          class="block w-full rounded sm:text-sm sm:leading-5"
          placeholder="vue3-exploration"
          aria-describedby="text-description"
        >
      </div>
    </div>
    <pre><span class="text-gray-500"># clone the starter</span>
git clone https://github.com/vincentdoerig/vue3-typescript-tailwind-starter {{ name || '&lt;project-name&gt;' }}
cd {{ name || '&lt;project-name&gt;' }}
rm -rf .git <span class="text-gray-500"># remove git folder</span>
git init <span class="text-gray-500"># initialise git for your new project</span></pre>
    <p>
      Alternatively, use this starter by clicking the <code>Use this template</code> button on <a
        href="https://github.com/vincentdoerig/vue3-typescript-tailwind-starter"
      >GitHub</a>.
    </p>
    <h2>Simple reactive state example</h2>
    <div class="flex">
      <span class="inline-flex mr-2 rounded-md shadow-sm">
        <button
          type="button"
          class="inline-flex items-center px-3 py-2 text-sm font-medium leading-4 text-white bg-red-600 border border-transparent rounded-md shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500 tabular-nums"
          @click="count++"
        >
          you clicked me {{ count }} times
        </button>
      </span>
      <span class="inline-flex rounded-md shadow-sm">
        <button
          type="button"
          class="inline-flex items-center px-3 py-2 text-sm font-medium leading-4 text-red-700 transition duration-150 ease-in-out bg-red-100 border border-transparent rounded-md hover:bg-red-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-300"
          @click="count = 0"
        >
          clear
        </button>
      </span>
    </div>
  </div !-->
</template>

<script lang="ts">
import { defineComponent, toRef, ref, CreateComponentPublicInstance } from 'vue'
import { globalState } from '../store'

type UploadedImage = {
  src: string | ArrayBuffer | null
  name: string
}

type Data = {
  dragActive: boolean
  layer: number
  images: Array<UploadedImage>
}

type This = CreateComponentPublicInstance<{}, {}, Data>

function check(str: string) {
  console.log(str)
  console.log(document.querySelector('.drop-area'))
}

function onDragEnter(this: This, event: DragEvent) {
    event.preventDefault()
  this.layer++
  this.dragActive = true
  // console.log('dragenter', event)

}

function onDragLeave(this: This, event: DragEvent) {
    event.preventDefault()
  if (--this.layer === 0) {
    this.dragActive = false
  }
  event.preventDefault()
  // console.log('dragleave', event)

}

function onDrop(this: This, event: DragEvent) {
  this.layer = 0
  this.dragActive = false

  event.preventDefault()

  const files = event.dataTransfer?.files
  if (files) {
    const images = Array.from(files).filter(file => file.name.endsWith('.jpg'))

    for (const image of images) {

      const reader = new FileReader()
        reader.readAsDataURL(image)

        reader.addEventListener('loadstart', () => {
          // add progress item
        })

        reader.addEventListener('progress', () => {
          // update progress item
        })

        reader.addEventListener('abort', () => {
          // remove progress item
        })

        reader.addEventListener('error', () => {
          // remove progress item
        })

        reader.addEventListener('load', () => {
          // what are you?
        })

        reader.addEventListener('loadend', () => {
          // remove progress item
          console.log(image.size)
          this.images.push({
            name: image.name,
            src: reader.result,
          })
        })
      }
  }
}

function addDragEventsToBody(this: This) {
  document.body.addEventListener('drag', (evt) => console.log('drag', evt))
  document.body.addEventListener('dragenter', onDragEnter.bind(this))
  document.body.addEventListener('dragleave', onDragLeave.bind(this))
  document.body.addEventListener('drop', onDrop.bind(this))
  document.body.addEventListener('dragover', (evt) => evt.preventDefault())
  document.body.addEventListener('dragend', (evt) => console.log('dragend', evt))
  document.body.addEventListener('dragstart', (evt) => console.log('dragstart', evt))
}

export default defineComponent<Record<string, unknown>, Record<string, unknown>, Data>({
  props: {
    msg: {
      type: String,
      default: '',
    },
  },

  setup() {
    check('setup')
    
    return {
      count: toRef(globalState, 'count'),
      name: ref(''),
    }
  },

  data() {
    return {
      dragActive: false,
      layer: 0,
      images: [],
    }
  },

  activated() {
    check('activated')
  },

  mounted() {
    check('mounted')
    addDragEventsToBody.call(this)
  },


  created() {
    check('created')
  },

  teardown() {
    check('teardown')
  },

})
</script>

<style>
.prose a {
  @apply text-gray-900 underline !important;
}
</style>
