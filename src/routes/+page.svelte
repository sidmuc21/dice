<script>
  import { onMount, onDestroy } from "svelte";

  let diceFace = "⚀";
  let message = "Shake your device...";
  let lastShake = 0;

  function rollDice() {
    const diceFaces = ["⚀", "⚁", "⚂", "⚃", "⚄", "⚅"];
    const roll = Math.floor(Math.random() * 6);
    diceFace = diceFaces[roll];
    message = `You rolled a ${roll + 1}! 🎲`;
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

<div class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-br from-purple-600 to-pink-500 text-white">
  <h1 class="text-3xl md:text-4xl font-bold mb-8">Shake the phone to roll the dice 🎲</h1>

  <div class="text-9xl mb-6 drop-shadow-lg">
    {diceFace}
  </div>

  <div class="text-xl md:text-2xl font-medium mb-6">
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
