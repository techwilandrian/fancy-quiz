<template>
  <div class="home">
    <Header 
      :correctAnswer="correctAnswer"
      :totalQuestion="totalQuestion"
    />

    <b-container>
    	<b-row>
    		<b-col sm="6" offset-sm="3">
    			<QuestionBox v-if="questions"
    				:currentQuestion="questions[index]"
    				:next="next"
            :incrementProgress="incrementProgress"
    			/>
    		</b-col>
    	</b-row>
  	</b-container>
  </div>
</template>

<script>
import Header from '../components/Header.vue';
import QuestionBox from '../components/QuestionBox.vue';

export default {
  name: 'Home',
  components: {
    Header, QuestionBox
  },
  data() {
  	return {
      correctAnswer: 0,
      totalQuestion: 0,
  		questions: null,
  		index: 0
  	}
  },
  methods: {
    incrementProgress: function(isCorrect) {
      if (isCorrect)
        this.correctAnswer++;

      this.totalQuestion++;
    },
  	next: function() {
  		this.index++;
  	}
  },
  mounted: function() {
  	fetch('https://opentdb.com/api.php?amount=10&category=27&type=multiple', {
  		method: 'get'
  	}).then(response => {
  		return response.json()
  	}).then(jsonData => {
  		this.questions = jsonData.results;
  	})
  }
}
</script>
