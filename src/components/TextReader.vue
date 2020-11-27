<template>
  <div
    class="drop"
    :class="getClasses"
    @dragover.prevent="dragOver"
    @dragleave.prevent="dragLeave"
    @drop.prevent="drop($event)"
  >
    <textarea v-model="textSource" v-if="textSource"></textarea>
    <h1 v-if="wrongFile">Wrong file type</h1>
    <h1 v-if="!textSource && !isDragging && !wrongFile">
      Перетяните <label for="uploadmytextfile">(или выберите)</label> текстовый
      файл
    </h1>

    <input type="file" id="uploadmytextfile" @change="requestUploadFile" />
    <div class="inputs" v-if="textSource">
      <input
        id="first"
        type="text"
        placeholder="Первое слово"
        v-model="firstWord"
      />
      <input
        id="first"
        type="text"
        placeholder="Второе слово"
        v-model="secondWord"
      />
      <button v-if="firstWord && secondWord" @click="findDist">Поиск</button>
    </div>
    <p v-if="wordErr">Ошибка:одно из слов не найдено</p>
    <div v-else>
      <p v-if="minDist">Минимальная дистанция слов - {{ minDist - 1 }}</p>
      <p v-if="maxDist">Максимальная дистанция слов - {{ maxDist - 1 }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "DropAnImage",
  data() {
    return {
      isDragging: false,
      wrongFile: false,
      textSource: null,
      firstWord: "",
      secondWord: "",
      minDist: null,
      maxDist: null,
      wordErr: false,
    };
  },
  computed: {
    getClasses() {
      return { isDragging: this.isDragging };
    },
  },
  methods: {
    dragOver() {
      this.isDragging = true;
    },
    dragLeave() {
      this.isDragging = false;
    },
    drop(e) {
      let files = e.dataTransfer.files;
      this.process(files);
    },
    requestUploadFile() {
      var src = this.$el.querySelector("#uploadmytextfile");
      let files = src.files;
      this.process(files);
    },
    process(files) {
      this.wrongFile = false;
      // allows only 1 file
      if (files.length === 1) {
        let file = files[0];
        // allows text only
        if (file.type.indexOf("text/") >= 0) {
          var reader = new FileReader();
          reader.onload = (f) => {
            this.textSource = f.target.result;
            this.isDragging = false;
          };
          // this is the method to read a text file content
          reader.readAsText(file);
        } else {
          this.wrongFile = true;
          this.textSource = null;
          this.isDragging = false;
        }
      }
    },
    findDist() {
      let arrOfWordsFromText = this.textSource
        .toLowerCase()
        .replace(/[.,:;$#-^_\n]/g, "")
        .split(/[ ]+/g);
      let first = this.firstWord.toLowerCase();
      let second = this.secondWord.toLowerCase();
      let minDist = arrOfWordsFromText.length;
      let maxDist = 0;
      let indexesFir = [];
      let indexesSec = [];
      for (let i = 0; i < arrOfWordsFromText.length; i++) {
        if (arrOfWordsFromText[i] == first) {
          indexesFir.push(i);
        }
        if (arrOfWordsFromText[i] == second) {
          indexesSec.push(i);
        }
        if (indexesFir != [] && indexesSec != []) {
          indexesFir.forEach((index1) => {
            indexesSec.forEach((index2) => {
              if (maxDist < Math.abs(index1 - index2))
                maxDist = Math.abs(index1 - index2);
              if (minDist > Math.abs(index1 - index2))
                minDist = Math.abs(index1 - index2);
            });
          });
        }
      }

      this.minDist = minDist;
      this.maxDist = maxDist;
      this.wordErr =
        indexesFir.length == 0 || indexesSec.length == 0 ? true : false;
      console.log(this.$data, arrOfWordsFromText);
    },
  },
};
</script>

<style scoped>
.drop {
  width: 400px;
  background-color: #eee;
  border: 10px solid #eee;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
  padding: 1rem;
  transition: background-color 0.2s ease-in-out;
  font-family: sans-serif;
}
.isDragging {
  background-color: #999;
  border-color: #fff;
}
textarea {
  width: 100%;
  height: 100%;
  object-fit: contain;
  resize: none;
}
input[type="file"] {
  display: none;
}
label {
  text-decoration: underline;
}

.inputs {
  display: flex;
  justify-content: space-between;
  padding-top: 5px;
}
</style>
