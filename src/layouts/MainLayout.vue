<template>
  <div>
    <q-splitter v-model="splitterModel" style="height: 700px">
      <template v-slot:before>
        <div class="q-pa-md">
          <q-tree
            :nodes="simple"
            node-key="label"
            selected-color="primary"
            v-model:selected="selected"
            @update:selected="onSelNode"
          />
        </div>
      </template>

      <template v-slot:after>
        <div class="q-px-md" style="width: 800px; max-width: 800px">
          <h5>{{ postData.title }}</h5>
          <p>{{ postData.analyz }}</p>
          <hr />
          <b class="text-primary">骗术揭秘</b>
          <pre style="white-space: pre-wrap">{{ postData.secret }}</pre>
          <hr />
          <b class="text-primary">基本特征</b>
          <pre style="white-space: pre-wrap">{{ postData.feature }}</pre>
          <hr />
          <b class="text-primary">惯用话术</b>
          <pre style="white-space: pre-wrap">{{ postData.content }}</pre>
        </div>
      </template>
    </q-splitter>
  </div>
</template>

<script>
import { ref } from "vue";
import tree from "src/res/tree.js";

export default {
  setup() {
    const simple = tree.data;
    const selected = ref(simple[0].children[0].children[0].label);
    const postData = ref({
      title: "",
      analyz: "",
      content: "",
      feature: "",
      secret: "",
    });

    function parse_feature(feature) {
      let text = "";
      if (feature.length > 0) {
        feature.forEach((item) => {
          text += `[${item.label}] ${item.value}\n`;
        });
      }
      return text;
    }
    function format_tag(html) {
      html = html.replace(/<br>/g, "\n");
      html = html.replace(/<.*?>/g, "");
      return html;
    }

    function onSelNode(target) {
      tree.data.forEach((item) => {
        item.children.forEach((p) => {
          p.children.forEach((c) => {
            if (c.label == target) {
              postData.value.title = target;
              postData.value.analyz = c.data.analyz;
              postData.value.secret = format_tag(c.data.secret);
              postData.value.feature = parse_feature(c.data.feature);
              postData.value.content = format_tag(c.data.content);
            }
          });
        });
      });
    }

    onSelNode(selected.value);

    return {
      splitterModel: ref(20),
      selected,
      simple,
      postData,
      onSelNode,
    };
  },
};
</script>
