<template>
  <div class="editor">
    <h1>エディター画面</h1>
    <span>{{ user.displayName }}</span>
    <button @click="logout">ログアウト</button>
    <div>
      <button class="saveMemosBtn" @click="saveMemos">メモの保存</button>
    </div>
    <div class="editorWrapper">
      <textarea class="markdown" v-model="markdown"></textarea>
      <div class="preview" v-html="preview()"></div>
    </div>
  </div>
</template>

<script>
import marked from 'marked';
export default {
  name: 'editor',
  props: ['user'],
  data () {
    return {
      markdown:'',
    }
  },
  methods: {
    logout: function() {
      firebase.auth().signOut();
    },
    preview: function() {
      return marked(this.markdown);
    },
    saveMemos: function() {
      firebase.database()
        .ref('memos/'+this.user.uid)
        .set(this.markdown);
    }
  },
  created: function() {
    firebase.database()
      .ref('memos/'+this.user.uid)
      .once('value')
      .then(result => {
        if (result.val()) {
          this.markdown = result.val();
        }
      });
  }
}
</script>

<style lang="scss" scoped>
.editorWrapper {
  display: flex;
}
.markdown {
  width: 50%;
  height: 500px;
}
.preview {
  width: 50%;
  text-align: left;
}
.saveMemosBtn {
  margin: 10px;
}
</style>
