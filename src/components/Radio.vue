<template>
  <div class="relative h-12 rounded w-32 shadow">
    <label class="w-full h-full flex items-center justify-center">
      <input type="radio" :name="name" :value="initialVal" @change="emitChecked">
      <span class="flex justify-center items-center" v-text="value"></span>
    </label>
  </div>
</template>

<script>
export default {
  name: "Radio",
  props: ["name", "value"],
  data: function() {
    return {
      initialVal: this.value,
      checkedState: false
    };
  },
  methods: {
    emitChecked: function(event) {
      this.checkedState = event.target.checked;
      this.$emit("input", this.initialVal);
    }
  }
};
</script>

<style lang="scss" scoped>
[type="radio"] {
  & + span::before,
  & + span::after {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    border-radius: 0.25rem;
    z-index: -10;
    transition: 0.5s ease;
  }

  &:not(:checked),
  &:checked {
    position: absolute;
    opacity: 0;
    pointer-events: none;

    & + span {
      position: relative;
      cursor: pointer;
      height: 100%;
      width: 100%;
      transition: 0.5s ease;
    }
  }

  &:checked {
    & + span::before {
      background-color: lighten($color: blue, $amount: 15);
      border-radius: 0.25rem;
    }
    & + span {
      color: white;
    }
  }

  &:not(:checked) {
    & + span::after,
    & + span::before {
      border: solid 2px lighten($color: blue, $amount: 15);
      border-radius: 0.25rem;
    }
  }
}
</style>
