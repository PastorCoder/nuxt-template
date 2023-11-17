<template>
  <div class="min-h-screen bg-white">
    <div>
      <v-breadcrumbs :items="items"></v-breadcrumbs>
    </div>

    <div class="my-6 flex justify-between">
      <p class="text-4xl font-bold">Templates</p>
      <v-btn class="bg-primary">SAVE</v-btn>
    </div>
    <v-divider :thickness="1" class="border-opacity-75"></v-divider>
    <DndProvider :backend="HTML5Backend">
      <!-- container -->
      <template class="bg-primary/10 rounded-lg mt-6 max-w-[50%]">
        <v-row class="rounded-lg p-6 w-full flex flex-col gap-y-10">
          <Card
            v-for="(card, index) in cards"
            :id="card.id"
            :key="card.id"
            :text="card.text"
            :index="index"
            :move-card="moveCard"
          />
        </v-row>
      </template>
    </DndProvider>
  </div>
</template>

<script lang="ts" setup>
const route = useRoute();
import { DndProvider } from "vue3-dnd";
import { HTML5Backend } from "react-dnd-html5-backend";

// data: () => ({
//   formData: [
//     {
//       label: "",
//       id: "",
//       inputType: "",
//       data: "",
//     },
//   ],
// });

interface Item {
  id: number;
  text: string;
}

const cards = ref<Item[]>([
  {
    id: 1,
    text: "Subject",
  },
  {
    id: 2,
    text: "Description",
  },
  // {
  //   id: 3,
  //   text: "Write README",
  // },
]);

const moveCard = (dragIndex: number, hoverIndex: number) => {
  const item = cards.value[dragIndex];
  cards.value.splice(dragIndex, 1);
  cards.value.splice(hoverIndex, 0, item);
};

const items = [
  "Form Templates",
  decodeURI(route.path.replaceAll("-", " ").split("/")[2]),
];
</script>
