<script>
  import Quiz from "$lib/components/Quiz/index.svelte";
</script>

<div class="header">
  <img src="favicon.png" width="90px" height="auto" />
  <h3>Know Your Meal <span>Quiz</span></h3>
</div>
<Quiz />

<style>
  :global(body) {
    background-color: #f1f7f3;
  }
  .header {
    display: flex;
    margin: auto;
    justify-content: center;
    margin-top: 1rem;
  }
  .header img {
    border: 1px solid #9bbe9d;
    border-radius: 1rem;
    overflow: hidden;
  }
  h3 {
    font-family: "Nunito", sans-serif;
    font-size: clamp(1.1rem, 4vw, 1.5rem);
    color: #412b0f;
    margin-left: 2rem;
  }
  span {
    font-style: italic;
    font-weight: 500;
    color: #4caf50;
  }
</style>
