<script setup lang="ts">
import { computed, ref, unref } from "vue";
import { useDrag, useDrop } from "vue3-dnd";
import { ItemTypes } from "../types/ItemTypes";
import type { XYCoord, Identifier } from "dnd-core";
import { toRefs } from "@vueuse/core";

const props = defineProps<{
  id: any;
  text: string;
  index: number;
  moveCard: (dragIndex: number, hoverIndex: number) => void;
}>();

interface DragItem {
  index: number;
  id: string;
  type: string;
}

const card = ref<HTMLDivElement>();
const [dropCollect, drop] = useDrop<
  DragItem,
  void,
  { handlerId: Identifier | null }
>({
  accept: ItemTypes.CARD,
  collect(monitor) {
    return {
      handlerId: monitor.getHandlerId(),
    };
  },
  hover(item: DragItem, monitor) {
    if (!card.value) {
      return;
    }
    const dragIndex = item.index;
    const hoverIndex = props.index;
    // Don't replace items with themselves
    if (dragIndex === hoverIndex) {
      return;
    }

    // Determine rectangle on screen
    const hoverBoundingRect = card.value?.getBoundingClientRect();

    // Get vertical middle
    const hoverMiddleY = (hoverBoundingRect.bottom - hoverBoundingRect.top) / 2;

    // Determine mouse position
    const clientOffset = monitor.getClientOffset();

    // Get pixels to the top
    const hoverClientY = (clientOffset as XYCoord).y - hoverBoundingRect.top;

    // Only perform the move when the mouse has crossed half of the items height
    // When dragging downwards, only move when the cursor is below 50%
    // When dragging upwards, only move when the cursor is above 50%

    // Dragging downwards
    if (dragIndex < hoverIndex && hoverClientY < hoverMiddleY) {
      return;
    }

    // Dragging upwards
    if (dragIndex > hoverIndex && hoverClientY > hoverMiddleY) {
      return;
    }

    // Time to actually perform the action
    props.moveCard(dragIndex, hoverIndex);

    // Note: we're mutating the monitor item here!
    // Generally it's better to avoid mutations,
    // but it's good here for the sake of performance
    // to avoid expensive index searches.
    item.index = hoverIndex;
  },
});

const [collect, drag] = useDrag({
  type: ItemTypes.CARD,
  item: () => {
    return { id: props.id, index: props.index };
  },
  collect: (monitor: any) => ({
    isDragging: monitor.isDragging(),
  }),
});

const { handlerId } = toRefs(dropCollect);
const { isDragging } = toRefs(collect);
const opacity = computed(() => (unref(isDragging) ? 0 : 1));

const setRef = (el: HTMLDivElement) => {
  card.value = drag(drop(el)) as HTMLDivElement;
};
</script>

<template>
  <div>
    <div
      class="rounded-lg px-2 py-2 bg-white"
      :ref="setRef"
      :style="{ opacity }"
      :data-handler-id="handlerId"
    >
      <v-col>
        <span class="flex justify-between">
          <label class="text-xl font-medium">{{ text }}</label>
          <v-icon icon="mdi-drag"></v-icon>
        </span>

        <v-text-field
          variant="outlined"
          type="text"
          class="mt-1"
          required
          placeholder="Type here"
        ></v-text-field>
      </v-col>
    </div>
    <!-- <div
      v-if="isShallowOver && !isDragging"
      :class="['indicator', { first: props.index === 0 }]"
    ></div> -->
  </div>
</template>
