<template>
  <section class="flex w-full">
    <div class="m-auto">
      <h1 class="text-center text-2xl">Real Time Chat</h1>
      <div class="border rounded-lg">
        <div class="h-64 p-2">
          <div
            v-for="chat in state.chats"
            :key="chat.message"
            class="w-full text-right"
            :class="chat.userId === userId ? 'text-right' : ''"
          >{{ chat.message }}</div>
        </div>
        <div class="h-8 p-2">
          <input
            v-model="state.message"
            type="text"
            placeholder="Start typing..."
            class="p-1 border rounded shadow"
            @keydown.enter="addMessage"
          >
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { onMounted, reactive } from "vue";
import { chatsRef } from "../utilities/firebase";
import { useStore } from "vuex";

export default {
  name: "Chat",
  setup() {
    const state = reactive({
      chats: [],
      message: "",
      userId: null
    });

    // fetching authUser from store using Vuex 4
    const store = useStore();
    const user = computed(() => store.state.authUser.uid);

    onMounted(async () => {
      // append this in utilities/firebase.js
      /* const db = firebase.database();
      state.collection = db.ref("chats");
      */

      // listening the reactive data
      state.collection.on("child_added", snapshot => {

        //replaced by vuex store
        //state.userId = firebase.auth().currentUser.uid;
        
        state.chats.push({ key: snapshot.key, ...snapshot.val() });
        //state.chats = snapshot.val();
      });
    });

    function addMessage() {
      const newChat = chatsRef.push();
      newChat.set({ userId: state.userId, message: state.message });
      state.message = "";
    }

    return {
      state,
      addMessage,
      userId,
    };
  }
};
</script>