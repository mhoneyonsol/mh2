<template>
  <button class="tip-button" @click="handleClick" :class="{ clicked: isClicked }">
    <span class="tip-button__text">Create token</span>
    <div class="coin-wrapper">
      <div ref="coin" class="coin">
        <div class="coin__middle"></div>
        <div class="coin__back"></div>
        <div class="coin__front">
          <img src="https://cryptologos.cc/logos/solana-sol-logo.png" alt="Solana Logo" class="solana-logo">
        </div>
      </div>
    </div>
  </button>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'ButtonAn',
  setup() {
    const isClicked = ref(false);
    const coin = ref(null);
    const maxMoveLoopCount = 90;
    let moveLoopCount = 0;
    let maxFlipAngle = 0;
    let sideRotationCount = 0;

    const resetCoin = () => {
      if (coin.value) {
        coin.value.style.setProperty('--coin-x-multiplier', 0);
        coin.value.style.setProperty('--coin-scale-multiplier', 0);
        coin.value.style.setProperty('--coin-rotation-multiplier', 0);
        coin.value.style.setProperty('--shine-opacity-multiplier', 0.4);
        coin.value.style.setProperty('--shine-bg-multiplier', '50%');
        coin.value.style.setProperty('--coin-y-multiplier', 0);
        coin.value.style.setProperty('--front-scale-multiplier', 1);
        coin.value.style.setProperty('--front-y-multiplier', 0);
        coin.value.style.setProperty('--middle-scale-multiplier', 0);
        coin.value.style.setProperty('--middle-y-multiplier', 0);
        coin.value.style.setProperty('--back-scale-multiplier', 0);
        coin.value.style.setProperty('--back-y-multiplier', 0);
        coin.value.style.setProperty('opacity', 1);
      }
      setTimeout(() => {
        isClicked.value = false;
      }, 300);
    };

    const flipCoinLoop = () => {
      moveLoopCount++;
      let percentageCompleted = moveLoopCount / maxMoveLoopCount;
      let angle = -maxFlipAngle * Math.pow((percentageCompleted - 1), 2) + maxFlipAngle;
      if (coin.value) {
        coin.value.style.setProperty('--coin-y-multiplier', -11 * Math.pow(percentageCompleted * 2 - 1, 4) + 11);
        coin.value.style.setProperty('--coin-x-multiplier', percentageCompleted);
        coin.value.style.setProperty('--coin-scale-multiplier', percentageCompleted * 0.6);
        coin.value.style.setProperty('--coin-rotation-multiplier', percentageCompleted * sideRotationCount);

        coin.value.style.setProperty('--front-scale-multiplier', Math.max(Math.cos(angle), 0));
        coin.value.style.setProperty('--front-y-multiplier', Math.sin(angle));
        coin.value.style.setProperty('--middle-scale-multiplier', Math.abs(Math.cos(angle), 0));
        coin.value.style.setProperty('--middle-y-multiplier', Math.cos((angle + Math.PI / 2) % Math.PI));
        coin.value.style.setProperty('--back-scale-multiplier', Math.max(Math.cos(angle - Math.PI), 0));
        coin.value.style.setProperty('--back-y-multiplier', Math.sin(angle - Math.PI));
        coin.value.style.setProperty('--shine-opacity-multiplier', 4 * Math.sin((angle + Math.PI / 2) % Math.PI) - 3.2);
        coin.value.style.setProperty('--shine-bg-multiplier', -40 * (Math.cos((angle + Math.PI / 2) % Math.PI) - 0.5) + '%');

        if (moveLoopCount > maxMoveLoopCount - 10) {
          // Make the coin disappear towards the end of the animation
          coin.value.style.setProperty('opacity', (maxMoveLoopCount - moveLoopCount) / 10);
        }
      }
      if (moveLoopCount < maxMoveLoopCount) {
        if (moveLoopCount === maxMoveLoopCount - 6) {
          coin.value.classList.add('shrink-landing');
        }
        window.requestAnimationFrame(flipCoinLoop);
      } else {
        coin.value.style.setProperty('opacity', 0);
        coin.value.classList.add('coin-landed');
        setTimeout(() => {
          coin.value.classList.remove('shrink-landing', 'coin-landed');
          resetCoin();
        }, 100); // Adjust timing if necessary
      }
    };

    const handleClick = () => {
      if (isClicked.value) return;
      isClicked.value = true;
      setTimeout(() => {
        sideRotationCount = Math.floor(Math.random() * 5) * 90;
        maxFlipAngle = (Math.floor(Math.random() * 4) + 3) * Math.PI;
        moveLoopCount = 0;  // Reset moveLoopCount
        flipCoinLoop();
      }, 50);
    };

    return { handleClick, isClicked, coin };
  },
};
</script>

<style scoped>
.solana-logo {
  width: 100%;
  height: 100%;
  border-radius: 50%;
}

.tip-button {
  background: none;
  margin-top: 150px;
  border: 0;
  border-radius: 0.25rem 0.25rem 0 0;
  cursor: pointer;
  font-family: "Quicksand", sans-serif;
  font-size: 0.75rem;
  font-weight: 600;
  height: 2.6rem;
  margin-bottom: -4rem;
  outline: 0;
  position: relative;
  top: 0;
  transform-origin: 0% 100%;
  transition: transform 50ms ease-in-out;
  width: 9.5rem;
  -webkit-tap-highlight-color: transparent;
}
.tip-button:active {
  transform: rotate(4deg);
}
.tip-button.clicked {
  animation: 150ms ease-in-out 1 shake;
  pointer-events: none;
}
.tip-button.clicked .tip-button__text {
  opacity: 0;
  transition: opacity 100ms linear 200ms;
}
.tip-button.clicked::before {
  height: 0.5rem;
  width: 60%;
}
.tip-button.clicked .coin {
  transition: margin-bottom 1s linear 200ms;
  margin-bottom: 0;
}
.tip-button.shrink-landing::before {
  transition: width 200ms ease-in;
  width: 0;
}
.tip-button.coin-landed::after {
  opacity: 1;
  transform: scale(1);
  transform-origin: 50% 100%;
}
.tip-button.coin-landed .coin-wrapper {
  background: radial-gradient(circle at 35% 97%, rgba(3, 16, 50, 0.4) 0.04rem, transparent 0.04rem), radial-gradient(circle at 45% 92%, rgba(3, 16, 50, 0.4) 0.04rem, transparent 0.02rem), radial-gradient(circle at 55% 98%, rgba(3, 16, 50, 0.4) 0.04rem, transparent 0.04rem), radial-gradient(circle at 65% 96%, rgba(3, 16, 50, 0.4) 0.06rem, transparent 0.06rem);
  background-position: center bottom;
  background-size: 100%;
  bottom: -1rem;
  opacity: 0;
  transform: scale(2) translateY(-10px);
}
.tip-button__text {
  color: #fff;
  margin-right: 1.8rem;
  opacity: 1;
  position: relative;
  transition: opacity 100ms linear 500ms;
  z-index: 3;
}
.coin-hidden {
  display:none;
}
.tip-button::before {
  background: #031032;
  border-radius: 0.25rem;
  bottom: 0;
  content: "";
  display: block;
  height: 100%;
  left: 50%;
  position: absolute;
  transform: translateX(-50%);
  transition: height 250ms ease-in-out 400ms, width 250ms ease-in-out 300ms;
  width: 100%;
  z-index: 2;
}
.tip-button::after {
  bottom: -1rem;
  color: #031032;
  content: "Thank you!";
  height: 110%;
  left: 0;
  opacity: 0;
  position: absolute;
  pointer-events: none;
  text-align: center;
  transform: scale(0);
  transform-origin: 50% 20%;
  transition: transform 200ms cubic-bezier(0, 0, 0.35, 1.43);
  width: 100%;
  z-index: 1;
}

.coin-wrapper {
  background: none;
  bottom: 0;
  height: 18rem;
  left: 0;
  opacity: 1;
  overflow: hidden;
  pointer-events: none;
  position: absolute;
  transform: none;
  transform-origin: 50% 100%;
  transition: opacity 200ms linear 100ms, transform 300ms ease-out;
  width: 100%;
}


.coin {
  --front-y-multiplier: 0;
  --back-y-multiplier: 0;
  --coin-y-multiplier: 0;
  --coin-x-multiplier: 0;
  --coin-scale-multiplier: 0;
  --coin-rotation-multiplier: 0;
  --shine-opacity-multiplier: 0.4;
  --shine-bg-multiplier: 50%;
  bottom: calc(var(--coin-y-multiplier) * 1rem - 3.5rem ); 
  height: 3.5rem;
  margin-bottom: 3.05rem;
  position: absolute;
  right: calc(var(--coin-x-multiplier) * 34% + 16%);
 transform: translateX(50%) scale(calc(0.52 + var(--coin-scale-multiplier))) rotate(calc(var(--coin-rotation-multiplier) * -1deg));
  transition: opacity 100ms linear 200ms, transform 100ms linear 200ms; /* Add transform transition for smooth movement */
  width: 3.5rem;
  z-index: 3;
  zoom:130%;
}
.coin__front, .coin__middle, .coin__back, .coin::before, .coin__front::after, .coin__back::after {
  border-radius: 50%;
  box-sizing: border-box;
  height: 100%;
  left: 0;
  position: absolute;
  width: 100%;
  z-index: 3;
}
.coin__front {
  background: radial-gradient(circle at 50% 50%, transparent 50%, rgba(115, 124, 153, 0.4) 54%, #c2cadf 54%), linear-gradient(210deg, #8590b3 32%, transparent 32%), linear-gradient(150deg, #8590b3 32%, transparent 32%), linear-gradient(to right, #8590b3 22%, transparent 22%, transparent 78%, #8590b3 78%), linear-gradient(to bottom, #fcfaf9 44%, transparent 44%, transparent 65%, #fcfaf9 65%, #fcfaf9 71%, #8590b3 71%), linear-gradient(to right, transparent 28%, #fcfaf9 28%, #fcfaf9 34%, #8590b3 34%, #8590b3 40%, #fcfaf9 40%, #fcfaf9 47%, #8590b3 47%, #8590b3 53%, #fcfaf9 53%, #fcfaf9 60%, #8590b3 60%, #8590b3 66%, #fcfaf9 66%, #fcfaf9 72%, transparent 72%);
  background-color: #8590b3;
  background-size: 100% 100%;
  transform: translateY(calc(var(--front-y-multiplier) * 0.3181818182rem / 2)) scaleY(var(--front-scale-multiplier));
}
.coin__front::after {
  background: rgba(0, 0, 0, 0.2);
  content: "";
  opacity: var(--front-y-multiplier);
}
.coin__middle {
  background: #737c99;
  transform: translateY(calc(var(--middle-y-multiplier) * 0.3181818182rem / 2)) scaleY(var(--middle-scale-multiplier));
}
.coin__back {
  background: radial-gradient(circle at 50% 50%, transparent 50%, rgba(115, 124, 153, 0.4) 54%, #c2cadf 54%), radial-gradient(circle at 50% 40%, #fcfaf9 23%, transparent 23%), radial-gradient(circle at 50% 100%, #fcfaf9 35%, transparent 35%);
  background-color: #8590b3;
  background-size: 100% 100%;
  transform: translateY(calc(var(--back-y-multiplier) * 0.3181818182rem / 2)) scaleY(var(--back-scale-multiplier));
}
.coin__back::after {
  background: rgba(0, 0, 0, 0.2);
  content: "";
  opacity: var(--back-y-multiplier);
}
.coin::before {
  background: radial-gradient(circle at 25% 65%, transparent 50%, rgba(255, 255, 255, 0.9) 90%), linear-gradient(55deg, transparent calc(var(--shine-bg-multiplier) + 0%), #e9f4ff calc(var(--shine-bg-multiplier) + 0%), transparent calc(var(--shine-bg-multiplier) + 50%));
  content: "";
  opacity: var(--shine-opacity-multiplier);
  transform: translateY(calc(var(--middle-y-multiplier) * 0.3181818182rem / -2)) scaleY(var(--middle-scale-multiplier)) rotate(calc(var(--coin-rotation-multiplier) * 1deg));
  z-index: 10;
}
.coin::after {
  background: #737c99;
  content: "";
  height: 0.3181818182rem;
  left: 0;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 100%;
  z-index: 2;
}

@keyframes shake {
  0% {
    transform: rotate(4deg);
  }
  66% {
    transform: rotate(-4deg);
  }
  100% {
    transform: rotate(0);
  }
}

</style>