<script lang="ts" setup>
import { useTheme } from '@/hooks/useTheme';
import { useEditorStore } from '@/store/editor';
import { skinDraft } from '@/types/editor';
import { getLightColor } from '@/utils/mcskineditor/color';


const editorStore = useEditorStore();

const model = ref<number>(editorStore.draftHistory.length >= 0 ? 0 : -1)

const { activeThemeName } = useTheme()

const selectedStyle = computed(() => {
  return {
    color: editorStore.color,
    backgroundColor: getLightColor(editorStore.color, 0.3) as string,
  }
})


watch(
  editorStore.draftHistory, () => {
    model.value = 0
  }
)

const loadSkinDraft = (item: skinDraft) => {
  const sortId = editorStore.draftHistory.length - 1 - item.id
  if (sortId === model.value) return
  editorStore.skineditor.backToDraft(item.skin)
}

</script>
<template>
  <div class="LeftSideBar" flex flex-col v-show="editorStore.draftHistory.length > 0">
    <v-slide-group show-arrows mandatory direction="vertical" v-model="model" :theme="activeThemeName">
      <template v-slot:prev>
        <v-btn ripple icon>
          <v-icon>fas fa-arrow-up</v-icon>
        </v-btn>
      </template>
      <v-slide-group-item v-for="item in editorStore.draftHistory" :key="item.id" v-slot="{ isSelected, toggle }">
        <v-card :style="isSelected ? selectedStyle : {}" my-2 @click="toggle">
          <v-img aspect-ratio="16/9" w-24 h-32 cover :src="item.show_img" @click="loadSkinDraft(item)"></v-img>
        </v-card>
      </v-slide-group-item>
      <template v-slot:next>
        <v-btn ripple icon>
          <v-icon>fas fa-arrow-down</v-icon>
        </v-btn>
      </template>
    </v-slide-group>
  </div>
</template>
<style lang="scss" scoped>
</style>
