<script>
import tooltip from '~/vue_shared/directives/tooltip';
import Icon from '~/vue_shared/components/icon.vue';
import { pluralize } from '~/lib/utils/text_utility';
import { __, sprintf } from '~/locale';

export default {
  components: {
    Icon,
  },
  directives: {
    tooltip,
  },
  props: {
    file: {
      type: Object,
      required: true,
    },
    showTooltip: {
      type: Boolean,
      required: false,
      default: false,
    },
    showStagedIcon: {
      type: Boolean,
      required: false,
      default: false,
    },
    forceModifiedIcon: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  computed: {
    changedIcon() {
      const suffix = this.file.staged && !this.showStagedIcon ? '-solid' : '';
      return this.file.tempFile && !this.forceModifiedIcon
        ? `file-addition${suffix}`
        : `file-modified${suffix}`;
    },
    stagedIcon() {
      return `${this.changedIcon}-solid`;
    },
    changedIconClass() {
      return `multi-${this.changedIcon} float-left`;
    },
    tooltipTitle() {
      if (!this.showTooltip) return undefined;

      const type = this.file.tempFile ? 'addition' : 'modification';

      if (this.file.changed && !this.file.staged) {
        return sprintf(__('Unstaged %{type}'), {
          type,
        });
      } else if (!this.file.changed && this.file.staged) {
        return sprintf(__('Staged %{type}'), {
          type,
        });
      } else if (this.file.changed && this.file.staged) {
        return sprintf(__('Unstaged and staged %{type}'), {
          type: pluralize(type),
        });
      }

      return undefined;
    },
  },
};
</script>

<template>
  <span
    v-tooltip
    :title="tooltipTitle"
    data-container="body"
    data-placement="right"
    class="ide-file-changed-icon"
  >
    <icon
      v-if="file.changed || file.tempFile || file.staged"
      :name="changedIcon"
      :size="12"
      :css-classes="changedIconClass"
    />
  </span>
</template>
