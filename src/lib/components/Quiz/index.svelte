<script>
  import { quiz } from "./quiz";
  import { fade } from "svelte/transition";

  const wrongEmojis = [
    "🤦‍♀️",
    "🤔",
    "😬",
    "🙄",
    "😕",
    "🤷‍♂️",
    "🥴",
    "🤯",
    "🤪",
    "😩",
    "🤢",
    "🤮",
    "💩",
    "🤡",
    "🙈",
    "🙉",
    "🙊",
  ];
  const correctEmojis = [
    "👍",
    "🎉",
    "🥳",
    "🙌",
    "💯",
    "💪",
    "🤩",
    "🎊",
    "🥇",
    "🏆",
    "🌟",
    "🥂",
    "🍻",
    "🍾",
    "😎",
  ];

  const quizEndMessages = [
    {
      emoji: "😔",
      text: "Oh no! It seems you're just starting your food journey. Keep exploring and learning more about the wonderful world of food with Celebrate Your Plate.",
      isInRange: (score) => score >= 0 && score <= 0.1,
    },
    {
      emoji: "😊",
      text: "It looks like you're taking your first steps in the culinary world. Keep learning and trying new foods with Celebrate Your Plate to enhance your food knowledge.",
      isInRange: (score) => score > 0.1 && score <= 0.25,
    },
    {
      emoji: "🙂",
      text: "Not bad! You've got a basic understanding of food and ingredients. Continue exploring and discovering new flavors with Celebrate Your Plate to sharpen your culinary skills.",
      isInRange: (score) => score > 0.25 && score <= 0.5,
    },
    {
      emoji: "😀",
      text: "Great job! You've got a good grasp of food knowledge. Keep indulging in the world of food and refine your taste with Celebrate Your Plate.",
      isInRange: (score) => score > 0.5 && score <= 0.75,
    },
    {
      emoji: "😃",
      text: "Impressive! You're a true food enthusiast. Celebrate Your Plate is here to help you take your culinary expertise to the next level.",
      isInRange: (score) => score > 0.75 && score <= 0.9,
    },
    {
      emoji: "🤩",
      text: "Congratulations! You're a food connoisseur. Continue to embrace and celebrate the fantastic world of food with Celebrate Your Plate.",
      isInRange: (score) => score > 0.9 && score <= 1,
    },
  ];

  function getMessageForScore(score) {
    // Find the first message object with a score within the range.
    const messageObj = quizEndMessages.find((message) =>
      message.isInRange(score)
    );
    // If a message object is found, return the combined emoji and text.
    if (messageObj) {
      return { emoji: messageObj.emoji, text: messageObj.text };
    }
    // If no message object is found, return a default message.
    return { emoji: "🤷‍♂️", text: "Score not within the expected range." };
  }
  function lerpColor(a, b, amount) {
    const ah = parseInt(a.replace(/#/g, ""), 16),
      ar = ah >> 16,
      ag = (ah >> 8) & 0xff,
      ab = ah & 0xff,
      bh = parseInt(b.replace(/#/g, ""), 16),
      br = bh >> 16,
      bg = (bh >> 8) & 0xff,
      bb = bh & 0xff,
      rr = ar + amount * (br - ar),
      rg = ag + amount * (bg - ag),
      rb = ab + amount * (bb - ab);

    return (
      "#" +
      (((1 << 24) + (rr << 16) + (rg << 8) + rb) | 0).toString(16).slice(1)
    );
  }

  function getColorFromRange(value) {
    const startColor = "#e4b7ac";
    const middleColor = "#e4e4ac";
    const endColor = "#ace4ac";
    if (value <= 0.5) {
      return lerpColor(startColor, middleColor, value * 2);
    } else {
      return lerpColor(middleColor, endColor, (value - 0.5) * 2);
    }
  }

  function getRandomAssortment(n) {
    let numbers = Array.from({ length: n }, (_, i) => i);
    for (let i = numbers.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [numbers[i], numbers[j]] = [numbers[j], numbers[i]];
    }
    return numbers;
  }

  function getRandomElement(array) {
    return array[Math.floor(Math.random() * array.length)];
  }

  const amountOfQuestionsToShow = 3;
  let correctAmount = 0;
  let randomQuizOrder = getRandomAssortment(quiz.length);
  let currentQuizOrderIndex = 0;
  $: currentQuestion = quiz[randomQuizOrder[currentQuizOrderIndex]];

  let shownEmoji = null;
  let shownMessage = null;
  let shownIsCorrect = true;
  let finishedMessage = "";
  let finishedEmoji = "";
  let finishColor = "#ace4ac";
  function selectAnswer(option) {
    shownMessage = option?.selectionMessage;
    if (option?.isCorrect) {
      shownIsCorrect = true;
      correctAmount = correctAmount + 1;
      shownEmoji = getRandomElement(correctEmojis);
    } else {
      shownIsCorrect = false;
      shownEmoji = getRandomElement(wrongEmojis);
    }
    if (currentQuizOrderIndex >= amountOfQuestionsToShow - 1) {
      const { emoji, text } = getMessageForScore(
        currentQuizOrderIndex / amountOfQuestionsToShow
      );
      finishedMessage = text;
      finishedEmoji = emoji;
      finishColor = getColorFromRange(correctAmount / amountOfQuestionsToShow);
    }
    setTimeout(() => {
      currentQuizOrderIndex = currentQuizOrderIndex + 1;
    }, 3000);
    setTimeout(() => {
      shownMessage = null;
    }, 7000);
  }

  function tryAgain() {
    randomQuizOrder = getRandomAssortment(quiz.length);
    correctAmount = 0;
    currentQuizOrderIndex = 0;
  }
</script>

<div class="container" transition:fade={{ duration: 4000 }}>
  <div
    class="containerBG"
    style=" background-image: linear-gradient(
    rgba(240, 246, 237, 0.7),
    rgba(240, 246, 237, 0.9)
  ),
  url('quiz/{currentQuestion?.id}.png');"
  />
  <img
    class="questionImage"
    src="quiz/{currentQuestion?.id}.png"
    width="100%"
    height="auto"
  />
  <h3 class="question">{currentQuestion?.question}</h3>
  <div class="buttonsContainer">
    {#each currentQuestion?.options as option}
      <button class="optionButton" on:click={() => selectAnswer(option)}>
        {option?.text}
      </button>
    {/each}
  </div>
  {#if currentQuizOrderIndex >= amountOfQuestionsToShow}
    {#key finishColor}
      <div
        class="messageContainer finishedContainer"
        style="background-color: {finishColor}"
        transition:fade
      >
        <h3>You Got</h3>
        <h3 class="percent">
          {((correctAmount / amountOfQuestionsToShow) * 100).toFixed()}%
        </h3>
        <h3>Correct</h3>
        <p>{finishedMessage} {finishedEmoji}</p>
        <button class="optionButton" on:click={() => tryAgain()}>
          Try Again
        </button>
      </div>
    {/key}
  {/if}
  {#if shownMessage}
    <div
      class="messageContainer"
      transition:fade
      class:wrongMessageContainer={!shownIsCorrect}
    >
      <span class="emoji">{shownEmoji}</span>
      <p>{shownMessage}</p>
      <div class="line" />
    </div>
  {/if}
</div>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: "Nunito", sans-serif;
  }
  .container {
    position: relative;
    max-width: 450px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin: 1rem auto;
    padding: 2rem;
    box-shadow: 0 8px 20px rgba(97, 58, 41, 0.3), 0 0 1px #d6cbbe;
    border-radius: 30px;
    overflow: hidden;
  }
  .containerBG {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-size: cover;
    background-position: center center;
    background-repeat: no-repeat;
    filter: blur(10px);
    z-index: -1;
  }
  .questionImage {
    border-radius: 15px;
  }
  .question {
    min-height: 80px;
  }
  h3 {
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-size: clamp(1.1rem, 4vw, 1.5rem);
    color: #412b0f;
  }
  .buttonsContainer {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  .optionButton {
    font-size: clamp(0.9rem, 4vw, 1.2rem);
    padding: 0.7rem 1rem;
    background-color: #4caf50;
    color: #ffffff;
    border: none;
    border-radius: 15px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .optionButton:hover {
    background-color: #388e3c;
  }
  .messageContainer {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #ace4ac;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .messageContainer p {
    color: #412b0fdb;
    font-size: 30px;
    max-width: 80%;
    line-height: 1.5;
  }
  .wrongMessageContainer {
    background-color: #e4b7ac;
  }
  .finishedContainer h3 {
    font-size: 35px;
    margin-top: 0.5rem;
    margin-bottom: 0.5rem;
    font-weight: 400;
  }
  .finishedContainer p {
    color: #412b0fab;
    line-height: 1.3;
    font-size: 24px;
    margin: 2rem 0;
  }
  .emoji {
    font-size: 100px;
  }
  .percent {
    font-size: 80px !important;
    line-height: 0.5;
    font-weight: 700 !important;
  }
  .line {
    width: 0;
    margin-top: 2rem;
    max-width: 80%;
    height: 3px;
    background-color: #412b0f;
    animation: fillLine 13s forwards;
  }

  @keyframes fillLine {
    0% {
      width: 0;
    }
    100% {
      width: 100%;
    }
  }
</style>
