<script setup lang="ts">
import { ref } from "vue";

function imageUrl(url: string) {
  const result = new URL(url, import.meta.url) as unknown;
  const returnResult = result as string;
  return returnResult;
}

const infoOpen = ref(false);

const selectedAction = ref("");

const selectedPattern = ref(0);

function selectPattern(patternNum: number) {
  selectedPattern.value = patternNum;
}

const grid = ref(Array(12).fill(0));

function useBroomstick() {
  if (selectedAction.value === "broomstick") {
    selectedAction.value = "";
  } else {
    selectedAction.value = "broomstick";
  }
}

function useStone() {
  if (selectedAction.value === "stone") {
    selectedAction.value = "";
  } else {
    selectedAction.value = "stone";
  }
  selectPattern(7);
}

function useWind() {
  grid.value = Array(12).fill(0);
}

function selectGrid(gridNum: number) {
  if (selectedPattern.value > 0) {
    // laying a stone
    if (selectedPattern.value === 7) {
      const existingStones: number[] = [];
      grid.value.forEach((g: number, gridIndex: number) => {
        if (g === 7) {
          existingStones.push(gridIndex);
        }
      });
      if (existingStones.length < 4) {
        const quotient = Math.floor((gridNum - 1) / 4);
        const remainder = gridNum % 4;
        // only place the stone if there is no stone on the same row or column
        if (
          existingStones.find(
            (laidStone: number) =>
              (laidStone + 1) % 4 === remainder ||
              (quotient * 4 + 1 <= laidStone + 1 &&
                quotient * 4 + 4 >= laidStone + 1)
          ) === undefined
        ) {
          grid.value[gridNum - 1] = 7;
        }
      }
      console.log(existingStones);
    } else {
      grid.value[gridNum - 1] = selectedPattern.value;
    }
    // if (selectedPattern.value !== 7) {
    //   selectedPattern.value = 0;
    // }
  }
}
</script>

<template>
  <div
    @mouseenter="infoOpen = true"
    @mouseleave="infoOpen = false"
    class="z-10 text-black font-light text-[2vw] absolute left-[3vw] top-[1.5vw]"
  >
    ?
  </div>
  <div
    v-if="infoOpen"
    class="z-10 w-[360px] leading-tight bg-white absolute left-[2.5vw] top-[5.5vw] text-black text-[0.8vw] rounded-[25px] border border-black px-5 py-5 shadow-lg shadow-gray-400"
  >
    <p>
      This is Kare-san-sui(Zen garden), one of the Japanese garden styles
      consisting only of stone and sand moss. <br />
      In this garden, water is not used and water waves are expressed with sand.
      <br />
      Place your own stones and draw waves to understand the principles of Zen
      Gardens. And sraw waves of different shapes in the garden. <br />
    </p>
    <p class="mt-20">
      Bauhaus-Universität Weimar <br />Visuelle Kommunikation WiSe 22/23<br />
      Trust your (Graphical) System <br />Prof. Markus Weisbeck, Adrian Palko<br />
      Yunseon Yang <br />Web Development by Hiyeon Kim
    </p>
  </div>
  <div class="h-screen w-screen flex flex-col items-center bg-transparent">
    <div class="text-black font-light italic text-[3vw] mt-[2vw] shrink-0">
      Zen Garden
    </div>
    <div
      class="grow w-full flex flex-col items-center justify-center space-y-[3vw]"
    >
      <div
        class="grid grid-rows-4 grid-flow-col border border-t-black border-l-black"
      >
        <button
          @click.prevent="selectGrid(index)"
          v-for="index in 12"
          :key="index"
          class="border-r border-b border-r-black border-b-black w-[6vw] h-[6vw]"
        >
          <img
            v-if="grid[index - 1] === 7"
            class="w-full h-full"
            :src="imageUrl(`/patterns/stone.png`)"
          />
          <img
            v-else-if="grid[index - 1] > 0"
            class="w-full h-full"
            :src="imageUrl(`/patterns/pattern_${grid[index - 1]}.png`)"
          />
        </button>
      </div>
      <div
        v-if="selectedAction === 'broomstick'"
        class="h-[7vw] flex items-center justify-center"
      >
        <div
          class="border border-black rounded-[25px] flex flex-row space-x-3 px-7 py-5 shadow-lg shadow-gray-400"
        >
          <button
            v-for="index in 6"
            :key="index"
            class="w-[5vw] h-[5vw]"
            @click.prevent="selectPattern(index)"
          >
            <img :src="imageUrl(`/patterns/pattern_${index}.png`)" />
          </button>
        </div>
      </div>
      <div v-else class="h-[7vw]"></div>
      <div class="pb-10 flex flex-row space-x-10">
        <button class="w-[6vw] h-[6vw]" @click.prevent="useBroomstick">
          <img
            :class="
              selectedAction === 'broomstick'
                ? 'selectedAction'
                : 'actionButton'
            "
            :src="imageUrl(`/images/broomstick.png`)"
          />
        </button>
        <div class="flex flex-row space-x-5">
          <button class="w-[6vw] h-[6vw]" @click.prevent="useStone">
            <img
              :class="
                selectedAction === 'stone' ? 'selectedAction' : 'actionButton'
              "
              :src="imageUrl(`/images/stone.png`)"
            />
          </button>
        </div>
        <div class="flex flex-row space-x-5">
          <button class="w-[6vw] h-[6vw]" @click.prevent="useWind">
            <img class="actionButton" :src="imageUrl(`/images/wind.png`)" />
          </button>
        </div>
      </div>
    </div>
    <!-- <a href="https://vitejs.dev" target="_blank">
        <img src="/vite.svg" class="logo" alt="Vite logo" />
      </a>
      <a href="https://vuejs.org/" target="_blank">
        <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
      </a>
    <HelloWorld msg="Vite + Vue" /> -->
  </div>
</template>

<style scoped>
/* .logo {.
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
} */

.actionButton:hover {
  filter: drop-shadow(0 0 0.75em #ff00ff);
  /* -webkit-filter: drop-shadow(12px 12px 7px rgba(0, 0, 0, 0.5));
         filter: drop-shadow(12px 12px 7px rgba(0, 0, 0, 0.5)); */
}

.selectedAction {
  filter: drop-shadow(0 0 0.75em #ff00ff);
  /* -webkit-filter: drop-shadow(12px 12px 7px rgba(0, 0, 0, 0.5));
         filter: drop-shadow(12px 12px 7px rgba(0, 0, 0, 0.5)); */
}
</style>
