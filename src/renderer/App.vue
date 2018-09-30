<template>
  <div id="app">
    <nav>
      <h1>Zenith Studios Showcase</h1>
      <button>IMPORT</button>
    </nav>
    <form @submit.prevent="importFilm">
      <input v-model="path" type="text" placeholder="file path">
      <input v-model="name" type="text" placeholder="Film Name">
      <input v-model="productionTime" type="text" placeholder="production time">
      <input v-model="yearMade" type="text" placeholder="year made">
      <input v-model="screenShotTime" type="text" placeholder="timecode in seconds for screen shot">
      <button type="submit">submit</button>
    </form>
  </div>
</template>

<script>
  import './assets/main.scss'
  import '../../node_modules/minireset.css/minireset.sass'
  var ffmpeg = require('fluent-ffmpeg');
  const fs = require('fs');
  export default {
    name: 'showcase',
    data() {

      return {
        path: '',
        name: '',
        productionTime: '',
        yearMade: '',
        screenShotTime: '',
        data: {}
      }
    },
    mounted() {

// First I want to read the file
      this.data = JSON.parse(fs.readFileSync('data.json', 'utf8'));
    },

    methods: {
      importFilm() {
        let name = this.name + '.png'
        console.log(name)
        let proc = new ffmpeg(this.path)
        .takeScreenshots({
          count: 1,
          timemarks: [this.screenShotTime],
          filename: name,
          folder: './shots/'
        }, function (err) {
          console.log('screenshots were saved')
        });
        console.log(proc)


      }

    }
  }
</script>