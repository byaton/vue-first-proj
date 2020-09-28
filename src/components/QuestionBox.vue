<template>
    <div>
        <b-jumbotron>
            <template v-slot:lead>
                {{currentQuestion.question}}
            </template>
            <hr class="my-4">
            <b-list-group>
                <b-list-group-item
                   v-for="(answer, index) in shuffledAnswers"
                   :class="[(!answered && index === selectedIndex) ? 'selected' :
                   (answered && index === selectedIndex && index === correctIndex ? 'correct' :
                   (answered && index === selectedIndex && index !== correctIndex ? 'wrong' : ''))]"
                   :key="index" @click="selectedAnswer(index)">
                    {{answer}}
                </b-list-group-item>
            </b-list-group>
            <b-button variant="primary" @click="submitAnswer" :disabled="(selectedIndex === -1) || answered">Submit</b-button>
            <b-button variant="success" v-on:click ="next" :disabled="(selectedIndex === -1) || (numTotal === 10) || !answered" >Next</b-button>            
        </b-jumbotron>
    </div>
</template>

<script>

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
        numTotal: Number
    },
    data() {
        return {
            selectedIndex: -1,
            shuffledAnswers: [],
            answered: false,
            correctIndex: 0
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = -1;
                this.shuffleAnswers();
                this.answered = false;
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion['correct_answer']);
            }
        }
    },
    // computed: {
    //     answers: function() {
    //         let answers = [...this.currentQuestion['incorrect_answers']];
    //         answers.push(this.currentQuestion.correct_answer);
    //         return answers;
    //     }
    // },
    methods: {
        selectedAnswer(index) {
            this.selectedIndex = index;
            console.log(this.shuffledAnswers[index], this.selectedIndex);
        },
        shuffleAnswers() {
            const answers = [...this.currentQuestion['incorrect_answers'], this.currentQuestion.correct_answer];
            this.shuffledAnswers = answers.sort(() => Math.random() - .5);
        },
        submitAnswer() {
            this.increment(this.shuffledAnswers[this.selectedIndex] === this.currentQuestion['correct_answer']);
            this.answered = true;
        },
        answeredClass(index) {
            let ansClass = '';
            if (index === this.selectedIndex) {
                if (this.answered) {
                    if (index === this.correctIndex) {
                        ansClass = 'correct';
                    } else {
                        ansClass = 'wrong';
                    }
                } else {
                    ansClass = 'selected';
                }
            }
            return ansClass;
        }
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 15px;
}

.list-group-item:hover {
    background-color: lightgray;
    cursor: pointer;
}

.btn {
    margin: 0 5px;
}

.selected {
    background-color: lightblue;
}

.correct {
    background-color: lightgreen;
}

.wrong {
    background-color: red;
}
</style>