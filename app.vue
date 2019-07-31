<template>
  <div>
    <div>
      <input @change="create" type="text" />
      <label for>id</label>
    </div>
    <div>
      <input v-model="id2" type="text" />
      <label for>friend id</label>
    </div>
    <button @click="connect">conn</button>
    <div>
      <input v-model="message" type="text" />
      <label for>message</label>
    </div>
    <button @click="send">send</button>
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
      message: "",
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
      let msg = `@${this.peer.id}: ${this.message}`;
      this.messages.push(msg);
      this.conn.send(msg);
    },
    connect(e) {
      const id = e.target.value;
      this.conn = this.peer.connect(this.id2);
      this.conn.on("open", () => {
        this.conn.send(`@${this.peer.id}: connected.`);
      });
    }
  }
};
</script>