<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template #lead>
                {{currentQuestion.question}}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item
                    v-for="(shuffleAnswer, index) in shuffleAnswers" 
                    :key="index"
                    @click.prevent="selectAnswer(index)"
                    :class="answerClass(index)"
                >
                    {{shuffleAnswer}}
                </b-list-group-item>
            </b-list-group>
            <b-button @click="back" variant="cancel">
                Back
            </b-button>
            <b-button @click="next" variant="success" :disabled="selectedIndex === null">
                Next
            </b-button>
            <b-button 
                variant="primary"
                v-on:click="submitAnswer"
                :disabled="selectedIndex === null || answered"
            >
                Submit
            </b-button>

            
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props: {
        currentQuestion: Object,
        next: Function,
        back: Function,
        increment: Function
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffleAnswers: [],
            answered: false
        }
    },
    // computed: {
    //     answers() {
    //         let answers = [...this.currentQuestion.incorrect_answers]
    //         answers.push(this.currentQuestion.correct_answer)
    //         return answers
    //     }
    // },
    watch: {
        currentQuestion: {
            immediate: true,
            handler(){
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswer()
            }
        }
    },
    methods: {
        selectAnswer(index){
            this.selectedIndex= index
        },
        submitAnswer(){
            let isCorrect = false

            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }

            this.answered = true

            this.increment(isCorrect)
        },
        shuffleAnswer(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffleAnswers = _.shuffle(answers)

            this.correctIndex = this.shuffleAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerClass(index){
            let answerClass = ''

            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selected'
            }else if(this.answered && this.correctIndex === index){
                answerClass = 'correct'
            }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrect'
            }

            return answerClass
        }
    },
    mounted() {
    }
}
</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }
    .list-group-item:hover{
        background: #eee;
        cursor: pointer;
    }
    .btn {
        margin: 0 5px;
    }

    .selected{
        background: rgb(167, 220, 255);
    }
    .correct {
        background: rgb(176, 255, 176);
    }
    .incorrect {
        background: rgb(255, 164, 164);
    }
</style>