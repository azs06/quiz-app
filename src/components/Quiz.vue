<template>
    <section id="wrapper">
      <div class="quiz-block">
        <h2>Pets Quiz</h2>
        <div v-if="!showResult" class="quiz-block-inner clearfix">
          <div v-for="(item, index) in questions" :key="item.id"> 
            <div class="top-img">
                <img :src="images.relaxingWalk" alt="Relaxing Walk">
            </div>
            <template v-if="item.visibilty">  
                <h3>{{item.question}}</h3>
                <img v-if="item.image" class="center mb-40 dog-img" :src="item.image" alt="Freckles">
                <ul class="list-unstyled" v-for="(choiceItem, choiceIndex) in item.choices" :key="choiceIndex">
                    <li>
                        <button @click="checkAnswer(choiceItem.choice, index, choiceIndex)" class="btn-3" :class="`btn-3 ${choiceItem.class}`">
                            <span class="letter">{{choiceItem.choice.split('.')[0]}}</span>{{choiceItem.choice.split('.')[1]}}
                            <span class="state-icon material-icons md-18 wrong-icon">highlight_off</span>
                            <span class="state-icon material-icons md-18 right-icon">check_circle</span>
                        </button>
                    </li>                                  
                </ul>
                <div class="clearfix">
                    <button 
                        v-if="item.isQuestionAnswered"
                        @click="gotoNextQuestion(index)"
                        class="btn-next">
                        Next <img :src="images.rightArrow" class="right-arrow">
                    </button>
                </div> 
            </template>  
          </div>  
        </div>
        <div v-if="showResult" class="quiz-block-inner">
          <img class="center mb-42 mb-48" :src="images.winners" alt="Winners">
          <h1>Result</h1>
          <div class="result">You got <span class="count">{{ quizResult.correct }}</span> correct answers</div>
          <button @click="tryAgain()" class="try">Try again</button>              
        </div>
        <h4>Property of PactFi - 2022</h4>
      </div>
    </section>
</template>

<script>
import relaxingWalk from '../assets/images/relaxing-walk.png'
import rightArrow from '../assets/images/right-arrow.png'
import winners from '../assets/images/winners.png'
export default {
    name: "Quiz",
    props: {},
    data() {
        return {
            showResult: false,
            imageUrl: null,
            questions: [
                {
                    id: 1,
                    question: "Which breed is Freckles?",
                    choices: [
                        { choice: "A. Weimaraner", class: "answer" },
                        { choice: "B. Australian Cattledog", class: "answer" },
                        { choice: "C. Border Collie", class: "answer" },
                        { choice: "D. Pembroke", class: "answer" },
                    ],
                    image: null,
                    correctAnswer: "B. Australian Cattledog",
                    visibilty: true,
                    isQuestionAnswered: false,
                    answeredValue: null,
                },
                {
                    id: 2,
                    question: "What purpose of the cattledog breed for?",
                    choices: [
                        { choice: "A. To protect people", class: "answer" },
                        { choice: "B. To herd cattle", class: "answer" },
                        { choice: "C. To protect cattle", class: "answer" },
                        { choice: "D. To hunt", class: "answer" },
                    ],
                    correctAnswer: "C. To protect cattle",
                    visibilty: false,
                    isQuestionAnswered: false,
                    answeredValue: null,
                },
            ],
            images: {
                rightArrow,
                winners,
                relaxingWalk
            }
        };
  },

  created(){
      this.fetchImages();
  },

  computed: {
    quizResult() {
      let result = {
        correct: 0,
        wrong: 0,
        totalAnswered: 0,
        totalQuestions: this.questions.length,
      };
      this.questions.forEach((el) => {
        if (el.isQuestionAnswered) {
          if (el.correctAnswer === el.answeredValue) {
            result.correct++;
          } else {
            result.wrong++;
          }
          result.totalAnswered++;
        }
      });
      return result;
    },
  },

  methods: {
    async fetchImages() {
      this.loading = true;
      let response = await fetch(
        "https://dog.ceo/api/breed/cattledog/australian/images/random"
      );
      let jsonResponse = await response.json();
      if (jsonResponse && jsonResponse.message) {
        this.questions[0].image = jsonResponse.message;
      }
    },
    checkAnswer(answer, index, choiceIndex) {
      debugger;  
      const question = this.questions[index];
      if (question.correctAnswer === answer) {
        question.choices[choiceIndex].class = "right-answer";
      } else {
        question.choices[choiceIndex].class = "wrong-answer";
        question.choices.forEach((el) => {
          if (el.choice === question.correctAnswer) {
            el.class = "right-answer";
          }
        });
      }
      question.isQuestionAnswered = true;
      question.answeredValue = answer;
    },

    gotoNextQuestion(index) {
      this.questions[index].visibilty = false;
      if (
        this.questions.length - 1 === index ||
        this.questions[index].correctAnswer !==
          this.questions[index].answeredValue
      ) {
        this.showResult = true;
        this.questions[index].visibilty = false;
      } else {
        this.questions[index + 1].visibilty = true;
      }
    },
    tryAgain() {
      this.showResult = false;
      this.questions[0].visibilty = true;
      this.questions.forEach((data) => {
        data.choices = data.choices.map((item) => {
          return { ...item, class: "answer" };
        });
      });
      this.questions = this.questions.map((item) => {
        return {
          ...item,
          isQuestionAnswered: false,
          answeredValue: null,
        };
      });
    },
  }

}

</script>