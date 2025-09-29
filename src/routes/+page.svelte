<script>
  import { onMount, onDestroy } from "svelte";

  let diceFace = "⚀";
  let message = "Shake your device...";
  let lastShake = 0;
  let isShaking = false;

  const diceFaces = ["⚀", "⚁", "⚂", "⚃", "⚄", "⚅"];

  function rollDice() {
    if (isShaking) return;

    isShaking = true;

    // Animate dice face changes
    let count = 0;
    const interval = setInterval(() => {
      diceFace = diceFaces[Math.floor(Math.random() * 6)];
      count++;
      if (count > 8) { // spin 8 times
        clearInterval(interval);
        const roll = Math.floor(Math.random() * 6);
        diceFace = diceFaces[roll];
        message = `You rolled a ${roll + 1}! 🎲`;
        isShaking = false;
      }
    }, 100);
  }

  function handleMotion(event) {
    const acc = event.accelerationIncludingGravity;
    if (!acc) return;

    const totalAcc = Math.sqrt(
      acc.x * acc.x + acc.y * acc.y + acc.z * acc.z
    );

    const now = Date.now();

    if (totalAcc > 15) {
      if (now - lastShake > 1000) {
        rollDice();
        lastShake = now;
      }
    }
  }

  onMount(() => {
    if (typeof window !== "undefined") {
      window.addEventListener("devicemotion", handleMotion);
    }
  });

  onDestroy(() => {
    if (typeof window !== "undefined") {
      window.removeEventListener("devicemotion", handleMotion);
    }
  });
</script>

<style>
  @keyframes dice-shake {
    0%, 100% { transform: rotate(0deg) translate(0, 0); }
    20% { transform: rotate(-10deg) translate(-2px, -2px); }
    40% { transform: rotate(10deg) translate(2px, -2px); }
    60% { transform: rotate(-10deg) translate(-2px, 2px); }
    80% { transform: rotate(10deg) translate(2px, 2px); }
  }

  .shake {
    animation: dice-shake 0.5s ease-in-out infinite;
  }
</style>

<div class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-br from-purple-600 to-pink-500 text-white px-4">
  <h1 class="text-2xl sm:text-3xl md:text-4xl font-bold mb-8 text-center">
    Shake the phone to roll the dice 🎲
  </h1>

  <div class="mb-6">
    <div
      class={`text-8xl sm:text-9xl drop-shadow-lg ${isShaking ? 'shake' : ''}`}
    >
      {diceFace}
    </div>
  </div>

  <div class="text-lg sm:text-xl md:text-2xl font-medium mb-6 text-center">
    {message}
  </div>

  <!-- fallback for desktop testing -->
  <button
    on:click={rollDice}
    class="px-6 py-3 rounded-lg bg-white text-purple-600 font-semibold shadow-md hover:bg-gray-100 transition"
  >
    Roll manually
  </button>
</div>
