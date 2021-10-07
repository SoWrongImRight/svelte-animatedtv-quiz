<script>

  import { fade, blur, fly, slide, scale } from 'svelte/transition';

  import { onMount, beforeUpdate, afterUpdate, onDestroy } from 'svelte';

  import Question from "./Question.svelte";

  import Modal from "./Modal.svelte";

  let activeQuestion = 0;

  let score = 0;
  
  let quiz = getQuiz();

  let isModalOpen = false;

  function pickAnswer(answer) {
    if(answer === correctAnswer) {
      return result = "Correct!"
    }
    result = "Incorrect"
  }

  async function getQuiz() {
    const res = await fetch('https://opentdb.com/api.php?amount=10&category=32&type=multiple')
    const quiz = await res.json();
    return quiz;
  }

  function nextQuestion() {
    activeQuestion = activeQuestion + 1
  }

  function resetQuiz() {
    isModalOpen = false;
    score = 0;
    quiz = getQuiz();
    activeQuestion = 0;
  }

  function addToScore() {
    score = score + 1;
  }

  $: if(score > 1) {
    isModalOpen = true;
  }

  $: actualQuestion = activeQuestion + 1;

</script>

<style>

  .fade-wrapper {
    position: absolute;
  }

</style>

<div>

  <button on:click={resetQuiz}>Start New Quiz</button>

  <h3>My Score: {score}</h3>
  <h4>Question #{actualQuestion} of 10</h4>
   
  {#await quiz}
    Loading...
  {:then data}
    
    {#each data.results as question, index}
      {#if index === activeQuestion}
        <div in:fly={{x:100}} out:fly={{x:-200}} class="fade-wrapper">  
          <Question {addToScore} {nextQuestion} {question} />
        </div>
      {/if}
    {/each}
  
  {/await}



</div>

{#if isModalOpen}
  <Modal>
    <h2>You won!</h2>
    <p>Congratulations</p>
    <button on:click={resetQuiz}>Start Over</button>
  </Modal>
{/if}