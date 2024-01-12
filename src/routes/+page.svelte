<script lang="ts">
  type Game = 'waiting for input' | 'in progress' | 'game over'
  type Word = string

  let game: Game = 'waiting for input'
  let typedLetter = ' '

  let words: Word[] = "the quick brown fox jumps over the lazy dog The quick brown fox jumps over the lazy dog The quick brown fox jumps over the lazy dog The quick brown fox jumps over the lazy dog The quick brown fox jumps over the lazy dog The quick brown fox jumps over the lazy dog".split(' ')
  let wordIndex = 0
  let letterIndex = 0
  let correctLetters = 0

  let wordsEl: HTMLDivElement
  let letterEl: HTMLSpanElement | null
  let inputEl: HTMLInputElement

  function setGameState(state:Game) {
    game = state
  }

  function startGame() {
    setGameState('in progress')
  }

  function updateGameState() {
    setLetter()
    checkLetter()
    nextLetter()
    resetLetter()
  }

  function setLetter() {
    const isWordCompleted = letterIndex > words[wordIndex].length - 1

    if (!isWordCompleted) {
      letterEl = wordsEl.children[wordIndex].children[letterIndex] as HTMLSpanElement
    } else {
      letterEl = null;
    }
  }

  function checkLetter() {
    if (letterEl) {
      const currentLetter = words[wordIndex][letterIndex]

      if (typedLetter === currentLetter) {
        letterEl.classList.add('correct')
        letterEl.style.opacity = '0.8'
        increaseScore()
      } 

      if (typedLetter !== currentLetter) {
        letterEl.classList.add('incorrect')
        letterEl.style.color = 'var(--primary)'
        letterEl.style.opacity = '1'
      }
    }
  }

  function increaseScore() {
    correctLetters += 1
  }

  function nextLetter() {
    letterIndex += 1
    setLetter()
  }

  function resetLetter() {
    typedLetter = ''
  }

  function handleKeydown(event: KeyboardEvent){
    if (event.code === 'Space') {
      event.preventDefault()
    }

    if (game === 'waiting for input') {
      startGame()
    }
  }
</script>

<div class="game" data-game={game}>
  <input
    bind:this={inputEl}
    bind:value={typedLetter}
    on:input={updateGameState}
    on:keydown={handleKeydown}
    class="input"
    type="text"
  />

  <div bind:this={wordsEl} class="words">
    {#each words as word}
      <span class="word">
        {#each word as letter}
          <span class="letter">{letter}</span>
        {/each}
      </span>
    {/each}
  </div>
</div>

<style lang="scss">
  .words {
    --line-height: 1em
    --lines: 3

    width: 100%
    max-height: calc(var(--line-height) * var(--lines) * 1.42);
    display: flex;
    flex-wrap: wrap;
    gap: 0.6em;
    position: relative;
    font-size: 1.5rem;
    line-height: var(--line-height);
    overflow: hidden;
    user-select: none;

    .letter {
      opacity: 0.4;
      transition: all 0.3s ease;

      &.correct {
          opacity: 0.8;
      }

      &.incorrect {
          color: var(--primary);
          opacity: 1;
      }
    }
  }
</style>