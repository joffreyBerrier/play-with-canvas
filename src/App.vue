<script setup>
import { onMounted, computed, ref } from "vue";

const showModal = ref(false);
const positionMarker = ref({});

const computedStyleModal = computed(() => {
  if (positionMarker.value.middlePosition) {
    return {
      position: "absolute",
      left: `${positionMarker.value.realPosition.x + 12}px`,
      top: `${positionMarker.value.realPosition.y + 12}px`,
    };
  }

  return {};
});

onMounted(() => {
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  //Get Mouse Position
  const getMousePos = (c, evt) => {
    var rect = c.getBoundingClientRect();
    return {
      x: ((evt.clientX - rect.left) / (rect.right - rect.left)) * c.width,
      y: ((evt.clientY - rect.top) / (rect.bottom - rect.top)) * c.height,
    };
  };

  // Insert image on canvas
  const imageObj = new Image();
  imageObj.onload = () => {
    ctx.drawImage(
      imageObj,
      0,
      0,
      imageObj.width,
      imageObj.height,
      0,
      0,
      canvas.width,
      canvas.height
    );
  };
  imageObj.src = "tshirt.jpg";

  // Create pin image
  const pinImageObj = new Image();
  pinImageObj.src = "pin.png";

  // On click on canvas get position and set the pin
  canvas.addEventListener(
    "click",
    (evt) => {
      showModal.value = true;

      const { x, y } = getMousePos(canvas, evt);

      // Divide the size of image
      const imageWidth = Math.round(pinImageObj.width / 10);
      const imageHeight = Math.round(pinImageObj.height / 10);

      positionMarker.value = {
        realPosition: { x, y },
        // Divide the position by 2 to have a center of the pin
        middlePosition: {
          x: x - imageWidth / 2,
          y: y - imageHeight / 2,
        },
      };

      ctx.drawImage(
        pinImageObj,
        positionMarker.value.middlePosition.x,
        positionMarker.value.middlePosition.y,
        imageWidth,
        imageHeight
      );
    },
    false
  );
});
</script>

<template>
  <div>
    <canvas id="canvas" width="360" height="438"></canvas>

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
