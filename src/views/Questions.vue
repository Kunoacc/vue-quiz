<template>
  <div class="container mx-auto mt-10">
    <h2 class="font-bold">My view of me</h2>
    <br>
    <p>Lorem ipsum dolor, sit amet consectetur adipisicing elit. Aliquam dolorum, nesciunt dignissimos, accusantium cum corrupti quo neque ea voluptatem atque exercitationem aut? Dolores repellendus dolor qui esse quos est quis.</p>
    <br>
    <p>Scoring:</p>
    <ol>
      <li>No not me</li>
      <li>Not quite me</li>
      <li>Yes, me</li>
    </ol>
    <br>
    <div
      class="w-full p-5 border-2 border-grey-light rounded mb-10"
      v-for="(question, index) in questions"
    >
      <p class="font-bold text-2xl mb-2">{{index + 1}}</p>
      <p :class="{'mb-10': true, 'text-red': errors[index]}">{{question}}</p>
      <div class="flex justify-around">
        <Radio :name="'question-'+index" value="1" @input="$data => $set(answers ,index, $data)"></Radio>
        <Radio :name="'question-'+index" value="2" @input="$data => $set(answers ,index, $data)"></Radio>
        <Radio :name="'question-'+index" value="3" @input="$data => $set(answers ,index, $data)"></Radio>
      </div>
    </div>
    <div class="w-full my-20 text-center">
      <button
        class="bg-blue py-4 px-8 shadow rounded text-white hover:bg-blue-light hover:text-black"
        @click="evaluateAnswers"
      >Submit</button>
    </div>
  </div>
</template>

<script>
import Radio from "../components/Radio.vue";
import axios from "axios";
export default {
  name: "questions",
  created: function() {
    this.getQuestions();
  },
  data: function() {
    return {
      questions: [],
      answers: [],
      errors: [],
      fetching: true,
      evaluated: false,
    };
  },
  components: { Radio },
  methods: {
    getQuestions: async function() {
      const instance = axios.create({
        baseURL: "http://test.natterbase.com:3002",
        headers: {
          "x-access-token":
            "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiMDAwMDAwMDMiLCJpYXQiOjE1MzM2NDQwOTMsImV4cCI6MTU2NTA5MzY5M30.oMv_mQN6mAAmAVrRAozC7Ytk3omAye9P_TQ8Xyg3VOE"
        }
      });
      let questions = await instance.get("/questions");
      this.questions = questions.data.questions;
      this.makeAnswers(this.questions);
      this.fetching = false;
      console.log(questions.data);
    },

    makeAnswers: function(questions) {
      const self = this;
      questions.forEach((val, index) => {
        self.answers.push("");
        self.errors.push(false);
      });
    },

    evaluateAnswers: function() {
      const self = this;
      this.questions.forEach((val, index) => {
        if (self.answers[index] == "") {
          self.$set(self.errors, index, true);
        }
      });
      this.errors.includes(true)
        ? this.$toasted.show("Please select an option for all questions", {
            theme: "outline",
            position: "bottom-right",
            duration: "3000"
          })
        : this.$toasted.show("questions answered😉", {
            type: "success",
            theme: "bubble",
            position: "bottom-right",
            duration: "3000"
          });
        this.evaluated = true;
    },

    log: function(event) {
      console.log(event);
    }
  },

  watch: {
    answers: function(val) {
     if (this.fetching === false && this.evaluated === true) {
          const self = this;
          const answers = val.map(val => val == "");
          this.log(answers)
          answers.forEach((val, index) => {
              self.$set(self.errors, index, val);
            });
     }
    }
  }
};
</script>


