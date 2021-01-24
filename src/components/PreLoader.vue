<template>
  <div class="spinner-wrapper">
    <div class="spinner"></div>
  </div>
</template>

<style lang="scss" scoped>
.spinner-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  left: 50%;
  top: 36%;
  transform: translate(-50%, -50%);
  z-index: 223;
  width: 100vw;
  height: 120%;
  background: white;
}
.spinner {
  width: 80px;
  height: 80px;
  background-color: #fc6435;
  border-radius: 100%;
  -webkit-animation: sk-scaleout 1s infinite ease-in-out;
  animation: sk-scaleout 1s infinite ease-in-out;
}
@-webkit-keyframes sk-scaleout {
  0% {
    -webkit-transform: scale(0);
  }
  100% {
    -webkit-transform: scale(1);
    opacity: 0;
  }
}
@keyframes sk-scaleout {
  0% {
    -webkit-transform: scale(0);
    transform: scale(0);
  }
  100% {
    -webkit-transform: scale(1);
    transform: scale(1);
    opacity: 0;
  }
}
@media screen and (max-width: 768px) {
  .spinner-wrapper {
    top: 14% !important;
  }
}
</style>