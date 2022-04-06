<script setup>
import { onMounted, computed, ref } from "vue";

const showModal = ref(false);
const positionMarker = ref({});

const computedStyleModal = computed(() => {
  if (positionMarker.value.modalPosition) {
    return {
      position: "absolute",
      left: `${positionMarker.value.modalPosition.x + 12}px`,
      top: `${positionMarker.value.modalPosition.y + 12}px`,
    };
  }

  return {};
});

onMounted(() => {
  const svg = document.getElementById("svgContainer");

  // Get Mouse Position
  const getMousePos = (e) => {
    const pt = svg.createSVGPoint(); // Created once for document

    pt.x = e.clientX;
    pt.y = e.clientY;

    // The cursor point, translated into svg coordinates
    const cursorpt = pt.matrixTransform(svg.getScreenCTM().inverse());

    // Divide the size of image
    positionMarker.value = {
      realPosition: {
        x: cursorpt.x,
        y: cursorpt.y,
      },
      // Divide the position by 2 to have a center of the pin
      modalPosition: {
        x: e.clientX + 12,
        y: e.clientY + 12,
      },
    };

    return {
      x: cursorpt.x,
      y: cursorpt.y,
    };
  };

  const createCircle = (x, y, size, svgId) => {
    const svgns = "http://www.w3.org/2000/svg";
    const circle = document.createElementNS(svgns, "circle");

    circle.setAttributeNS(null, "cx", x);
    circle.setAttributeNS(null, "cy", y);
    circle.setAttributeNS(null, "r", size);
    circle.setAttribute("id", svgId);

    circleIds.value.push(svgId);

    circle.addEventListener("click", (e) => {
      console.log(
        "voilà tu cliques tu as l'info et tu peux faire ce que tu veux 😇",
        e.target
      );
      // Ici tu peux faire ce que tu veux
    });

    svg.appendChild(circle);
  };

  // Click on svg container
  const circleIds = ref([]);
  const handleClickSvg = (e) => {
    showModal.value = true;

    getMousePos(e);

    createCircle(
      positionMarker.value.realPosition.x,
      positionMarker.value.realPosition.y,
      10,
      circleIds.value.length + 1
    );
  };

  // handleClickOutside
  const handleClickOutside = (e) => {
    if (
      e?.target?.attributes?.id?.value &&
      circleIds.value.includes(Number(e.target.attributes.id.value))
    )
      return;

    handleClickSvg(e);
  };

  svg.addEventListener("click", handleClickOutside, false);
});
</script>

<template>
  <div>
    <svg id="svgContainer" viewBox="0 0 1000 1000">
      <image
        xlink:href="http://localhost:3000/assets/tshirt.jpg"
        x="0"
        y="0"
        height="1000"
        width="1000"
      />
    </svg>

    <!-- <canvas id="canvas" width="360" height="438"></canvas> -->

    <div v-if="showModal" class="modal" :style="computedStyleModal">
      <p>Coucou Matthias ❤️</p>
      voici la position de ton click :<br />
      <pre>{{ positionMarker }}</pre>
    </div>
  </div>
</template>

<style>
.modal {
  position: fixed;
  background: white;
  padding: 20px;
  border: 1px solid #ccc;
}
</style>
