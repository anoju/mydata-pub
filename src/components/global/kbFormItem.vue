<template>
  <div
    class="form_item"
    :class="{ty2:type2}"
  >
    <div
      v-if="!!label || !!$slots.labelLeft || !!$slots.labelRight"
      class="fm_lb"
      :class="labelClass"
      :style="{
        width: `${labelWidth}px`,
        flex: `0 0 ${labelWidth}px`
      }"
    >
      <div class="left">
        {{ label }}
        <ico-require v-if="!!label && required" />
        <slot name="labelLeft" />
      </div>
      <div
        v-if="!!$slots.labelRight"
        class="right"
      >
        <slot name="labelRight" />
      </div>
    </div>
    <div class="fm_cont">
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  name: 'kbFormItem',
  props: {
    label: { type: String, default: null },
    labelClass: { type: String, default: null },
    labelWidth: { type: [String, Number], default: null },
    required: { type: Boolean, default: false },
    type2: { type: Boolean, default: false },
  },
  mounted() {

  },
};
</script>
