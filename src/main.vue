<template>
  <div>
    <input @change="create" type="text"/>
    <input v-model="id2" type="text"/>
    <button @click="connect">conn</button>
    <input @change="send" type="text"/>
    <div id="log">
      <div v-for="m in messages" :key="m">{{m}}</div>
    </div>
  </div>
</template>

<script>
import Peer from "peerjs";
export default {
  data() {
    return {
      peer: null,
      conn: null,
      id2: "",
      messages: []
    };
  },
  methods: {
    create(e) {
      const id = e.target.value;
      this.peer = new Peer(id);
      this.peer.on("connection", conn => {
        conn.on("data", data => {
          this.messages.push(data);
        });
      });
    },
    send(e) {
      const msg = e.target.value;
      this.conn.send(this.peer.id + msg);
    },
    connect(e) {
      const id = e.target.value;
      this.conn = this.peer.connect(this.id2);
      this.conn.on("open", () => {
        this.conn.send("hi!");
      });
    }
  }
};
</script>