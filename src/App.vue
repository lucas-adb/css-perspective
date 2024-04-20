<script setup>
import { ref, computed } from "vue";

const perspective = ref(100);
const rotateX = ref(0);
const rotateY = ref(0);
const rotateZ = ref(0);

const bgColor = ref("#8d81f3");
const colorIndex = ref(0);

const shape = ref("square");

const color = computed(() => {
  if (shape.value === "triangle") {
    return {
      backgroundColor: "transparent",
      borderBottomColor: bgColor.value,
    };
  }

  if (shape.value === "heart") {
    return {
      backgroundColor: "transparent",
      "--heart-color": bgColor.value,
    };
  }

  return { backgroundColor: bgColor.value };
});

const setColor = () => {
  const colors = [
    "#8d81f3",
    "#ed78d9",
    "#ff7fad",
    "#ffa080",
    "#ffcc65",
    "#f9f871",
  ];

  if (colorIndex.value >= colors.length - 1) {
    bgColor.value = colors[0];
    colorIndex.value = 0;
  } else {
    bgColor.value = colors[colorIndex.value + 1];
    colorIndex.value += 1;
  }
};

const box = computed(() => {
  return {
    transform: `
      perspective(${perspective.value}px)
      rotateX(${rotateX.value}deg)
      rotateY(${rotateY.value}deg)
      rotateZ(${rotateZ.value}deg)
    `,
  };
});

const reset = () => {
  perspective.value = 100;
  rotateX.value = 0;
  rotateY.value = 0;
  rotateZ.value = 0;
};

const copy = async () => {
  let text = `transform:${box.value.transform};`;
  await navigator.clipboard.writeText(text);
  alert("CSS Copied to Clipboard");
};
</script>

<template>
  <div class="main-container">
    <h2>CSS3 Perspective Playground</h2>
    <main>
      <section class="settings">
        <div class="settings-container">
          <label>perspective: {{ perspective }}px;</label>
          <input type="range" min="0" max="999" v-model="perspective" />

          <label>rotateX: {{ rotateX }}deg; </label>
          <input type="range" min="-180" max="180" v-model="rotateX" />

          <label>rotateY: {{ rotateY }}deg; </label>
          <input type="range" min="-180" max="180" v-model="rotateY" />

          <label>rotateZ: {{ rotateZ }}deg; </label>
          <input type="range" min="-180" max="180" v-model="rotateZ" />

          <div class="btns-container">
            <button type="button" @click.prevent="reset">Reset</button>
            <button type="button" @click.prevent="setColor">Color</button>

            <select v-model="shape">
              <option value="square">Square</option>
              <option value="rectangle">Rectangle</option>
              <option value="circle">Circle</option>
              <option value="oval">Oval</option>
              <option value="triangle">Triangle</option>
              <option value="heart">Heart</option>
            </select>

            <button type="button" @click.prevent="copy">Copy</button>
          </div>
        </div>
      </section>
      <section class="output">
        <div class="box-container">
          <div class="box" :style="[box, color]" :class="shape"></div>
        </div>
      </section>
    </main>
  </div>

  <css-doodle>
    :doodle {
    @grid: 1x3 / 100vmax;
    position: absolute;
    top: 0; left: 0;
    z-index: 0;
    }

    @size: 100% 150%;
    position: absolute;

    background: @m(100, (
    linear-gradient(transparent, @p(
    #f9f871@repeat(2, @p([0-9a-f])),
    #ff7fad@repeat(2, @p([0-9a-f]))
    ))
    @r(0%, 100%) @r(0%, 100%) /
    @r(1px) @r(23vmin)
    no-repeat
    ));

    will-change: transform;
    animation: f 50s linear calc(-50s / @size() * @i()) infinite;
    @keyframes f {
    from { transform: translateY(-100%) }
    to { transform: translateY(100%) }
    }
  </css-doodle>
</template>

<style scoped></style>
