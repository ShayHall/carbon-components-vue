<template>
  <div
    data-tile="expandable"
    class="cv-tile-expandable bx--tile--expandable"
    tabindex="0"
    :class="[{ 'bx--tile--is-expanded': internalExpanded }]"
    :style="styleObject"
    @click="toggle"
    @keydown.enter.prevent="toggle"
    @keydown.space.prevent
    @keyup.space.prevent="toggle"
  >
    <button type="button" class="bx--tile__chevron">
      <ChevronDown16 v-if="componentsX" />
      <svg v-else width="12" height="8" viewBox="0 0 12 8" fill-rule="evenodd">
        <path d="M10.6 0L6 4.7 1.4 0 0 1.4l6 6.1 6-6.1z"></path>
      </svg>
    </button>
    <div class="bx--tile-content">
      <span data-tile-atf class="bx--tile-content__above-the-fold" ref="aboveFold">
        <slot>
          <!-- Above the fold content here -->
        </slot>
      </span>
      <span class="bx--tile-content__below-the-fold" ref="belowFold" v-show="internalExpanded || initialized">
        <slot name="below">
          <!-- Rest of the content here -->
        </slot>
      </span>
    </div>
  </div>
</template>

<script>
import { componentsX } from '../../internal/feature-flags';
import ChevronDown16 from '@carbon/icons-vue/es/chevron--down/16';

export default {
  name: 'CvTileExpandable',
  props: {
    expanded: Boolean,
  },
  components: { ChevronDown16 },
  data() {
    return {
      componentsX,
      styleObject: {
        maxHeight: 'initial',
      },
      initialized: false,
      internalExpanded: this.expanded,
    };
  },
  watch: {
    expanded(val) {
      if (val !== this.internalExpanded) {
        this.toggle(val);
      }
    },
  },
  methods: {
    toggle(force) {
      let currentHeight = this.$el.getBoundingClientRect().height;
      if (!this.initialized) {
        this.styleObject.maxHeight = `${currentHeight}px`;
        this.initialized = true;
      }

      this.$nextTick(() => {
        const forceType = typeof force;
        this.internalExpanded = forceType === 'boolean' ? force : !this.internalExpanded;

        const belowFoldHeight = this.$refs.belowFold.getBoundingClientRect().height;

        if (this.internalExpanded) {
          currentHeight += belowFoldHeight;
        } else {
          currentHeight -= belowFoldHeight;
        }
        this.styleObject.maxHeight = `${currentHeight}px`;
      });
    },
  },
};
</script>
