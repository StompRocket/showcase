<template>
  <div id="app">
    <nav>
      <h1>Zenith Studios Showcase</h1>

    </nav>
    <form v-if="dev" @submit.prevent="importFilm">
      <input v-model="path" type="text" placeholder="file path">
      <input v-model="name" type="text" placeholder="Film Name">
      <input v-model="productionTime" type="text" placeholder="production time">
      <input v-model="yearMade" type="text" placeholder="year made">
      <input v-model="screenShotTime" type="text" placeholder="timecode in seconds for screen shot">
      <button type="submit">submit</button>
    </form>
    <div v-if="!dev" class="films">
      <div v-for="film in sortedArray" :key="film.name" class="filmContainer">
        <img  @click="open(film.path)" :src="film.shot" :alt="film.name">
        <h1>{{film.name}} ({{film.yearMade}})</h1>
        <p>Production Time: {{film.productionTime}}</p>
      </div>
    </div>

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
        dev: false,
        path: '',
        name: '',
        productionTime: '',
        yearMade: '',
        screenShotTime: '',
        data: {},
        sortedArray: null
      }
    },
    mounted() {

// First I want to read the file
      this.data = JSON.parse(fs.readFileSync('data.json', 'utf8'));
      function compare(a, b) {
        if (Number(a.yearMade) > Number(b.yearMade))
          return -1;
        if (Number(a.yearMade) < Number(b.yearMade))
          return 1;
        return 0;
      }

      this.sortedArray =  this.data.films.sort(compare);
    },


    methods: {
      open(path) {

        path  = path.replace(/ /g, "\\ ")
        console.log(path)
        var exec = require('child_process').exec;
        exec('open ' + path);

      },
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
        let film = {}
        film.path = this.path
        film.name = this.name
        film.shot = `./shots/${this.name}.png`;
        film.yearMade = this.yearMade;
        film.productionTime = this.productionTime
        this.data.films.push(film)
        fs.writeFile("./data.json", JSON.stringify(this.data), function(err) {
          if(err) {
            return console.log(err);
          }
          console.log("The file was saved!");
          this.path = ''
          this.name = ''
          this.productionTime = ''
          this.yearMade = ''
          this.screenShotTime = ''
        });

      }

    }
  }
</script>