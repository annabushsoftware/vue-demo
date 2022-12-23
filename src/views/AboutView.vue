<template>
  <div class="about">
    <h1>This is an about page</h1>
    <b-button variant="primary">Button</b-button>
  </div>
</template>

<style lang="scss">
.about {
  @media (min-width: 1024px) {
    min-height: 100vh;
    margin-top: calc(50vh - 86px);
  }

  @media(max-width: 1023px) {
    text-align: center;
  }
}
</style>
