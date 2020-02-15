<template>
	<div class="question-box">
	 	<b-jumbotron>
	 		<template slot="lead">
	 			{{ currentQuestion.question }}
	 		</template>

	 		<hr class="my-4" />

	 		<b-list-group
	 			v-for="(answer, index) in shuffledAnswers" 
	 			:key="index"
	 		>
				<b-list-group-item
					@click="selectAnswer(index)"
					class="question-box__list-item"
					:class="answerClass(index)"
				>
					{{ answer }}
				</b-list-group-item>
			</b-list-group>

			<div class="mt-3">
		    	<b-button 
					variant="primary" 
					@click="submitAnswer" 
					class="mr-1" 
					href="#"
					:disabled="selectedAnswer === null || answered">
						Submit
				</b-button>

		    	<b-button 
					variant="success" 
					class="ml-1" 
					@click="next" 
					href="#"
					:disabled="!answered">
						Next
				</b-button>
		    </div>
    	</b-jumbotron>
    </div>
</template>

<script>
	import _ from 'lodash'

	export default {
		props: {
			currentQuestion: Object,
			next: Function,
			incrementProgress: Function
		},
		computed: {
			answers: function() {
				let answers = [...this.currentQuestion.incorrect_answers];
				answers.push(this.currentQuestion.correct_answer);

				return answers;
			}
		},
		watch: {
			currentQuestion: {
				immediate: true,
				handler() {
					this.answered = false;
					this.selectedAnswer = null;
					this.shuffleAnswers();
				}
			}
		},
		data() {
			return {
				selectedAnswer: null,
				correctAnswer: null,
				shuffledAnswers: null,
				answered: false
			}
		},
		methods: {
			answerClass: function(index) {
				if (index === this.selectedAnswer && !this.answered)
					return 'selected';
				else if (index === this.correctAnswer && this.answered)
					return 'correct';
				
				else if (index === this.selectedAnswer && this.answered && index !== this.correctAnswer)
					return 'incorrect';
			},
			submitAnswer: function() {
				let isCorrect = false;

				if (this.selectedAnswer === this.correctAnswer)
					isCorrect = true;

				this.answered = true;

				this.incrementProgress(isCorrect);
			}, 
			shuffleAnswers: function() {
				let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
				this.shuffledAnswers = _.shuffle(answers);

				this.correctAnswer = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
			},
			selectAnswer: function(index) {
				this.selectedAnswer = index;
			}
		}
	}
</script>

<style lang="stylus">
	@import './questionbox.styl'

	.selected
		background-color: lightblue;

	.correct
		background-color: lightgreen;

	.incorrect
		background-color: red;

	.question-box__list-item:hover
		background-color: #eee;
		cursor: pointer;
</style>