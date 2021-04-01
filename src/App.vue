<template>
  <div id="app">
    <section class="hero is-warning is-fullheight">
      <!--heroBody-->
      <div class="hero-body">
        <!--container-->
        <div class="container is-fluid mw-1200">
          <!--columns-->
          <div class="columns is-centered is-marginless is-paddingless box">
            <!--sidebar-->
            <div class="column is-5 sidebar">
              <div class="sidebarContainer">
                <div class="tags is-marginless">
                  <span class="tag is-medium is-rounded">#quiz</span>
                  <span class="tag is-medium is-rounded">#js</span>
                </div>

                <!-- title -->
                <h1 class="title is-2">Đố vui</h1>
                <h2 class="subtitle is-5">Trang đố vui đơn giản.</h2>

                <!-- description -->
                <p class="subtitle is-6 has-text-justified">
                  Created using <em>VueJs</em>, <em>Monkeys Team</em>,
                  <em>animate.css</em>, and <em>Font Awesome 4</em>.
                </p>
              </div>

              <div class="sidebarFooter">
                <p>
                  Photo by <a href="https://unsplash.com/@debidiemski">Deborah Diem</a>
                </p>
              </div>
            </div>
            <!--/sidebar-->

            <!--questionBox-->
            <div class="column is-7 questionBox is-paddingless is-marginless">
              <!-- transition -->
              <transition
                enter-active-class="animated jackInTheBox"
                leave-active-class="animated zoomOut"
                mode="out-in"
              >
                <div v-if="!quizStarted" v-bind:key="quizStarted" class="quizForm">
                  <div class="titleContainer">
                    <h2 class="title is-4">Bắt đầu</h2>
                  </div>

                  <div class="quizFormContainer">
                    <div class="field">
                      <div class="field-label is-normal is-size-5">
                        <label class="label">Tên của bạn</label>
                      </div>
                      <div class="control">
                        <input
                          class="input is-rounded"
                          type="text"
                          v-model="quiz.user"
                          placeholder="Enter your name"
                          required
                        />
                      </div>
                    </div>
                    <!-- <div class="field">
                      <div class="field-label is-normal is-size-5">
                        <label class="label">Difficulty</label>
                      </div>

                      <div class="control">
                        <label class="radio">
                          <input type="radio" name="0" checked />
                          Easy
                        </label>
                        <label class="radio">
                          <input type="radio" name="1" />
                          Medium
                        </label>
                        <label class="radio">
                          <input type="radio" name="2" />
                          Hard
                        </label>
                      </div>
                    </div> -->

                    <a
                      class="pagination-previous button is-medium is-info is-rounded is-outlined is-pulled-right"
                      v-on:click="
                        if (quiz.user.length > 0) {
                          quizStarted = !quizStarted;
                        }
                      "
                      style="margin-bottom: 12px"
                    >
                      Start
                    </a>
                  </div>
                </div>

                <!--qusetionContainer-->
                <div
                  class="questionContainer"
                  v-if="questionIndex < quiz.questions.length && quizStarted"
                  v-bind:key="questionIndex"
                >
                  <!-- questionTitle -->
                  <div class="titleContainer">
                    <h2 class="title is-5">
                      {{ questionIndex + 1 }}. {{ quiz.questions[questionIndex].text }}
                    </h2>
                  </div>

                  <!-- quizOptions -->
                  <div class="optionContainer">
                    <div
                      class="option"
                      v-for="(response, index) in responsesArray"
                      @click="selectOption(index)"
                      :class="{ 'is-selected': userResponses[questionIndex] == index }"
                      :key="index"
                    >
                      {{ index | charIndex }}. {{ response.text }}
                    </div>
                  </div>

                  <!--quizFooter: navigation and progress-->
                  <div class="questionFooter">
                    <!--pagination-->
                    <nav
                      class="pagination is-centered"
                      role="navigation"
                      aria-label="pagination"
                    >
                      <!-- prevButton -->
                      <a
                        class="pagination-previous button is-info is-rounded is-outlined"
                        v-on:click="prev()"
                        :disabled="questionIndex < 1"
                      >
                        Câu trước
                      </a>

                      <!-- prevButton -->
                      <a
                        class="pagination-next button is-info is-rounded"
                        v-on:click="next()"
                        :disabled="questionIndex >= quiz.questions.length"
                      >
                        {{ userResponses[questionIndex] == null ? "Bỏ qua" : "Trả lời" }}
                      </a>
                    </nav>
                    <!--/pagination-->

                    <!--progress-->
                    <div class="progressContainer">
                      <progress
                        class="progress is-success is-small"
                        :value="(questionIndex / quiz.questions.length) * 100"
                        max="100"
                      >
                        {{ (questionIndex / quiz.questions.length) * 100 }}%
                      </progress>
                    </div>
                    <!--/progress-->
                  </div>
                  <!--/quizFooter-->
                </div>
                <!--/questionContainer-->

                <!--quizCompletedResult-->
                <div
                  v-if="questionIndex >= quiz.questions.length && quizStarted"
                  v-bind:key="questionIndex"
                  class="quizCompleted has-text-centered"
                >
                  <!-- quizCompletedIcon: Achievement Icon -->
                  <span class="icon is-large has-text-success">
                    <i class="fa fa-check-circle-o fa-3x"></i>
                  </span>

                  <!--resultTitleBlock-->
                  <h2 class="title">{{ quiz.user }} làm tốt lắm!</h2>
                  <p class="subtitle">
                    <!-- Total score: {{ myScore }} / {{ quiz.questions.length }} -->
                  </p>
                  <!--/resultTitleBlock-->
                </div>
                <!--/quizCompetedResult-->
              </transition>
            </div>
            <!--/questionBox-->
          </div>
          <!--/columns-->
        </div>
        <!--/container-->
      </div>
      <!--/heroBody-->
    </section>
  </div>
</template>

<script>
import data from "raw-loader!./assets/quiz.txt";
var myquiz = data.split("\n");
var myquizraw = [];
function shuffle(array) {
  let currentIndex = array.length;
  let temporaryValue;
  let randomIndex;
  console.log(123123123);

  // While there remain elements to shuffle...
  while (currentIndex !== 0) {
    // Pick a remaining element...
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex -= 1;

    // And swap it with the current element.
    temporaryValue = array[currentIndex];
    array[currentIndex] = array[randomIndex];
    array[randomIndex] = temporaryValue;
  }

  return array;
}
for (let index = 0; index < myquiz.length; index += 5) {
  var quizraw = {
    text: "",
    responses: [],
  };
  var splitstr = myquiz[index].split(":")[0] + ":";
  quizraw.text = myquiz[index].substring(splitstr.length).trim();

  if (myquiz[index].toLowerCase().startsWith("câu")) {
    myquiz[index + 1][0] == " "
      ? quizraw.responses.push({
          text: myquiz[index + 1].trim().substring(2),
          correct: true,
        })
      : quizraw.responses.push({ text: myquiz[index + 1].trim().substring(2) });
    myquiz[index + 2][0] == " "
      ? quizraw.responses.push({
          text: myquiz[index + 2].trim().substring(2),
          correct: true,
        })
      : quizraw.responses.push({ text: myquiz[index + 2].trim().substring(2) });
    myquiz[index + 3][0] == " "
      ? quizraw.responses.push({
          text: myquiz[index + 3].trim().substring(2),
          correct: true,
        })
      : quizraw.responses.push({ text: myquiz[index + 3].trim().substring(2) });
    myquiz[index + 4][0] == " "
      ? quizraw.responses.push({
          text: myquiz[index + 4].trim().substring(2),
          correct: true,
        })
      : quizraw.responses.push({ text: myquiz[index + 4].trim().substring(2) });
    quizraw.responses = shuffle(quizraw.responses);
    myquizraw.push(quizraw);
  }
}

console.log(myquizraw);
const quiz = {
  user: "Dave",
  questions: myquizraw,
};
const userResponseSkelaton = Array(quiz.questions.length).fill(null);

export default {
  name: "App",
  data() {
    return {
      quiz: {
        user: "",
        questions: shuffle(myquizraw),
      },
      questionIndex: 0,
      userResponses: userResponseSkelaton,
      quizStarted: false,
      isActive: false,
      myScore: 0,
    };
  },
  filters: {
    charIndex(i) {
      return String.fromCharCode(97 + i);
    },
  },
  computed: {
    responsesArray() {
      return this.quiz.questions[this.questionIndex].responses;
    },
  },
  methods: {
    selectOption(index) {
      this.$set(this.userResponses, this.questionIndex, index);
    },
    next() {
      if (
        typeof this.quiz.questions[this.questionIndex].responses[
          this.userResponses[this.questionIndex]
        ] !== "undefined" &&
        this.quiz.questions[this.questionIndex].responses[
          this.userResponses[this.questionIndex]
        ].correct
      ) {
        this.myScore += 1;
        if (this.questionIndex < this.quiz.questions.length) this.questionIndex++;
      } else {
        this.questionIndex = Math.floor(this.questionIndex / 5) * 5;
        this.quiz.questions[this.questionIndex].responses = shuffle(
          this.quiz.questions[this.questionIndex].responses
        );
        alert("Bạn vừa trả lời sai nên bạn sẽ trở về câu " + (this.questionIndex + 1));
      }
    },

    prev() {
      if (this.quiz.questions.length > 0) this.questionIndex--;
    },
    // Return "true" count in userResponses
    score() {
      let score = 0;
      for (let i = 0; i < this.userResponses.length; i++) {
        if (
          typeof this.quiz.questions[i].responses[this.userResponses[i]] !==
            "undefined" &&
          this.quiz.questions[i].responses[this.userResponses[i]].correct
        ) {
          score += 1;
        }
      }
      return score;
    },
  },
};
</script>

<style lang="scss">
$trans_duration: 0.3s;
$titleBg: #29c5dc;
@import "./assets/bulma.min.css";
@import "./assets/animate.min.css";
// @import "./assets/font-awesome.min.css";
@import url("https://fonts.googleapis.com/css?family=Montserrat:400,400i,700");
@import url("https://fonts.googleapis.com/css?family=Open+Sans:400,400i,700");

html {
  overflow-y: auto;
}
body {
  font-family: "Open Sans", sans-serif;
  font-size: 14px;

  /* mocking native UI */
  cursor: default !important; /* remove text selection cursor */
  user-select: none; /* remove text selection */
  user-drag: none; /* disbale element dragging */
}

.button {
  transition: $trans_duration;
}
.title,
.subtitle {
  font-family: Montserrat, sans-serif;
  font-weight: normal;
}
.animated {
  transition-duration: $trans_duration/2;
}
.mw-1200 {
  max-width: 1200px !important;
}

.sidebar {
  background: url("https://source.unsplash.com/RVF0ngUujks");
  background-size: cover;

  border-radius: 6px 0px 0px 6px;
  z-index: 10;

  .sidebarContainer {
    padding: 10px;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.25);

    .tag {
      background: rgba(255, 255, 255, 0.25);
      color: rgba(255, 255, 255, 0.8);
    }

    .title {
      color: white !important;
    }
    .subtitle {
      color: rgba(255, 255, 255, 0.8) !important;
    }
  }
  .sidebarFooter {
    display: none;
    color: red;
  }
}

.questionBox {
  height: 100%;
  position: relative;
  display: flex;

  .titleContainer {
    background: $titleBg;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;

    h2 {
      color: white;
      padding: 18px;
    }
  }

  .quizForm {
    display: block;
    white-space: normal;

    height: 100%;
    width: 100%;

    .quizFormContainer {
      height: 100%;
      margin: 15px 18px;

      .field-label {
        text-align: left;
        margin-bottom: 0.5rem;
      }
    }
  }
  .quizCompleted {
    width: 100%;
    padding: 25px;
  }
  .questionContainer {
    white-space: normal;

    height: 100%;
    width: 100%;

    .optionContainer {
      margin-top: 12px;
      flex-grow: 1;
      .option {
        border-radius: 290486px;
        padding: 9px 18px;
        margin: 0 18px;
        margin-bottom: 12px;
        transition: $trans_duration;
        cursor: pointer;
        background-color: rgba(0, 0, 0, 0.05);
        border: transparent 1px solid;

        &.is-selected {
          border-color: #209cee;
          background-color: white;
        }
        &:hover {
          background-color: rgba(0, 0, 0, 0.1);
        }
        &:active {
          transform: scaleX(0.9);
        }
      }
    }

    .questionFooter {
      width: 100%;
      align-self: flex-end;

      .pagination {
        //padding: 10px 15px;
        margin: 15px 25px;
      }
      .progressContainer {
        margin: 15px 25px;
      }
    }
  }
}
@media screen and (min-width: 769px) {
  .container {
    height: 100%;

    .columns {
      // height: 100%;

      .column {
        // height: 100%;
      }
    }
  }

  .sidebar {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .questionBox {
    align-items: center;
    justify-content: center;

    .questionContainer {
      display: flex;
      flex-direction: column;
    }
  }
}

@media screen and (max-width: 768px) {
  .sidebar {
    height: auto !important;
    border-radius: 6px 6px 0px 0px;
  }
}
</style>
