<script setup>
import { onMounted } from 'vue'; // 【新增】引入 onMounted 钩子
import { IFrameHelper } from 'widget/helpers/utils';
import { CHATWOOT_ON_START_CONVERSATION } from '../constants/sdkEvents';
import AvailabilityContainer from 'widget/components/Availability/AvailabilityContainer.vue';
import { useMapGetter } from 'dashboard/composables/store.js';

const props = defineProps({
  availableAgents: { type: Array, default: () => [] },
  hasConversation: { type: Boolean, default: false },
});

const emit = defineEmits(['startConversation']);

const widgetColor = useMapGetter('appConfig/getWidgetColor');

const startConversation = () => {
  emit('startConversation');
  if (!props.hasConversation) {
    IFrameHelper.sendMessage({
      event: 'onEvent',
      eventIdentifier: CHATWOOT_ON_START_CONVERSATION,
      data: { hasConversation: false },
    });
  }
};

// 【新增】组件挂载时，自动执行进入对话框的方法
onMounted(() => {
  startConversation();
});
</script>

<template>
  <!-- 【修改】加入 v-if="false" 直接阻止渲染 -->
  <div
    v-if="false"
    class="flex flex-col gap-3 w-full shadow outline-1 outline outline-n-container rounded-xl bg-n-background dark:bg-n-solid-2 px-5 py-4"
  >
    <AvailabilityContainer :agents="availableAgents" show-header show-avatars />

    <button
      class="inline-flex items-center gap-1 font-medium text-n-slate-12"
      :style="{ color: widgetColor }"
      @click="startConversation"
    >
      <span>
        {{
          hasConversation
            ? $t('CONTINUE_CONVERSATION')
            : $t('START_CONVERSATION')
        }}
      </span>
      <i class="i-lucide-chevron-right size-5 mt-px" />
    </button>
  </div>
</template>
