<template>
  <div id="app">
    <div class="page-line">
      <div
        class="page"
        v-for="(page, index) in pages"
        :key="index"
        :class="{ active: activePage === index }"
        @click="setActivePage(index)"
      >
        <h5 v-html="page"></h5>
      </div>
    </div>

    <button @click="toggleTheme" class="toggle-theme-btn">
      Переключить тему
    </button>

    <button v-if="activePage != 0" @click="removePage" class="remove-theme-btn">
      Удалить страницу
    </button>

    <div class="field">
      <transition name="fade" mode="out-in">
        <div class="text" :key="activePage">
          <div v-if="activePage === 0">
            <div class="addText">
              <div class="textarea">
                <textarea v-model="addTexts"></textarea>
                <div
                  class="textareaLength"
                  :class="{
                    highlight: addTexts.trim() === '' || addTexts.length > 255,
                  }"
                >
                  {{ addTexts.length }} / 255
                </div>
              </div>
              <button
                @click="addText"
                :disabled="addTexts.trim() === '' || addTexts.length > 255"
              >
                Добавить
              </button>
            </div>
          </div>

          <div v-else v-html="texts[activePage]"></div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      addTexts: "",
      activePage: 0,
      pages: ["+"],
      texts: [""],
      darkTheme: false,
    };
  },
  created() {
    // Загружаем данные из localStorage при создании компонента
    const savedTexts = localStorage.getItem("texts");
    const savedPages = localStorage.getItem("pages");
    const savedTheme = localStorage.getItem("darkTheme");

    if (savedTexts) {
      this.texts = JSON.parse(savedTexts);
    }
    if (savedPages) {
      this.pages = JSON.parse(savedPages);
    }
    if (savedTheme) {
      this.darkTheme = JSON.parse(savedTheme);
      document.body.classList.toggle("dark", this.darkTheme);
    }
  },
  methods: {
    setActivePage(index) {
      this.activePage = index;
    },
    toggleTheme() {
      this.darkTheme = !this.darkTheme;
      document.body.classList.toggle("dark", this.darkTheme);
      localStorage.setItem("darkTheme", JSON.stringify(this.darkTheme));
    },
    removePage() {
      if (this.activePage > 0) {
        this.texts.splice(this.activePage, 1);
        this.pages.splice(this.activePage, 1);
        this.activePage = this.activePage - 1;
        localStorage.setItem("texts", JSON.stringify(this.texts));
        localStorage.setItem("pages", JSON.stringify(this.pages));
      }
    },
    addText() {
      this.texts.push(this.addTexts.trim());
      this.pages.push(this.addTexts.trim());
      this.addTexts = "";
      localStorage.setItem("texts", JSON.stringify(this.texts));
      localStorage.setItem("pages", JSON.stringify(this.pages));
    },
  },
};
</script>

<style>
#app {
  padding: 5%;
}
* {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  box-sizing: border-box;
  transition: background 0.5s, color 0.5s, box-shadow 0.5s !important;
}
.page-line {
  display: flex;
  gap: 10px;
  margin: 0 0 20px 0;
}
.page {
  height: 4vw;
  min-width: 4vw;
  max-width: 14vw;
  background-color: #f5f5dc;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: left;
  cursor: pointer;
  transition: 0.5s;
  border-radius: 3.5vw;
  box-shadow: 0.5vw 0.5vw 0.8vw rgba(0, 0, 0, 0.3), inset 0 0 0 rgba(0, 0, 0, 0);
  color: #333;
  font-size: 2vw;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  padding: 1vw;
}
.page h5 {
  justify-content: left;
  text-align: left;
  cursor: pointer;
  transition: 0.5s;
  font-size: 2vw;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.dark {
  background-color: #333;
  color: #ddd;
  & .page {
    background-color: #444;
    color: #ddd;
    box-shadow: 0.5vw 0.5vw 0.8vw rgba(255, 255, 255, 0.3),
      inset 0 0 0 rgba(0, 0, 0, 0);
  }
  & .page.active {
    box-shadow: 0 0 0 rgba(0, 0, 0, 0),
      inset 0.5vw 0.5vw 0.8vw rgba(255, 255, 255, 0.3);
  }
  & .field {
    box-shadow: 2px 2px 0.8vw rgba(255, 255, 255, 0.2);
    background-color: #444;
  }
  & .text {
    color: #ddd;
  }
  & .toggle-theme-btn {
    background-color: #444;
    color: #ddd;
  }
  & .addText textarea {
    border: 1px solid #888;
    background: #8b8b8b;
    color: #ddd;
  }
  & .addText textarea:focus {
    outline: none;
    border-color: #444;
  }
  & .addText button {
    color: #ddd;
    background-color: #333;
  }
  & .remove-theme-btn {
    transition: background 0.5s;
    background-color: #955252;
  }
  & .remove-theme-btn:hover {
    background: #c55d5d;
  }
  & .highlight {
    color: #f9afaf;
  }
}
.page.active {
  box-shadow: 0 0 0 rgba(0, 0, 0, 0), inset 0.5vw 0.5vw 0.8vw rgba(0, 0, 0, 0.3);
  transform: scale(0.98);
}
.field {
  width: 100%;
  height: auto;
  padding: 30px 40px;
  background-color: #f5f5dc;
  border-radius: 10px;
  box-shadow: 2px 2px 0.8vw rgba(0, 0, 0, 0.2);
  overflow: hidden;
}
.text {
  color: #333;
  font-size: 2vw;
  max-height: 1000px;
}
.text div {
  word-wrap: break-word;
  overflow-wrap: break-word;
  white-space: normal;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease, max-height 0.5s, transform 0.5s ease !important;
}
.fade-enter,
.fade-leave-to {
  max-height: 0;
  overflow: hidden;
  opacity: 0;
  transform: scale(0);
}
.toggle-theme-btn {
  margin: 10px 10px 10px 0;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.5s, color 0.5s;
  background-color: #e0e0e0;
  color: #333;
}
.remove-theme-btn {
  margin: 10px 10px 10px 0;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.5s, color 0.5s;
  background-color: #c55d5d;
  color: #ddd;
}
.remove-theme-btn:hover {
  background: #fd8585;
  color: #fff;
}
.addText {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.addText textarea {
  width: 100%;
  height: 200px;
  resize: none;
  padding: 10px 10px 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 16px;
  transition: border-color 0.5s;
  background: #e9e9e9;
}
.addText textarea:focus {
  outline: none;
  border-color: #e0e0e0;
}
.addText button {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.5s;
  background-color: #ddd;
}
.textarea {
  position: relative;
}
.textareaLength {
  font-size: 12px;
  position: absolute;
  right: 20px;
  bottom: 10px;
}
.highlight {
  color: #cf0000;
}
</style>
