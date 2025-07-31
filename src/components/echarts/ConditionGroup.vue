<template>
  <div class="condition-group" style="margin-left: 20px; border-left: 2px solid #ddd; padding-left: 10px; margin-top: 10px;">
    <div style="margin-bottom: 10px;">
      <label>逻辑关系：</label>
      <select v-model="localGroup.op" @change="emitUpdate">
        <option value="AND">AND</option>
        <option value="OR">OR</option>
      </select>
    </div>

    <div v-for="(child, idx) in localGroup.children" :key="idx" style="margin-bottom: 6px;">
      <!-- 子组 -->
      <div v-if="child.children" style="position: relative; border: 1px dashed #aaa; padding: 8px; margin-bottom: 6px;">
        <button
          @click="removeChild(idx)"
          title="删除子组"
          style="position: absolute; top: 4px; right: 4px; color: red; background: none; border: none; font-size: 16px; cursor: pointer;"
        >
          ×
        </button>

        <condition-group
          :group="child"
          :fields="fields"
          @update-group="updateChild(idx, $event)"
          @remove="removeChild(idx)"
        />
      </div>

      <!-- 单个条件 -->
      <div v-else style="display: flex; gap: 8px; align-items: center;">
        <select v-model="child.field" @change="emitUpdate" style="min-width: 120px;">
          <option disabled value="">请选择字段</option>
          <option v-for="f in fields" :key="f" :value="f">{{ f }}</option>
        </select>

        <select v-model="child.operator" @change="emitUpdate" style="min-width: 80px;">
          <option>=</option>
          <option>!=</option>
          <option>&gt;</option>
          <option>&lt;</option>
          <option>&gt;=</option>
          <option>&lt;=</option>
          <option>LIKE</option>
        </select>

        <input v-model="child.value" @input="emitUpdate" placeholder="值" style="flex: 1;" />

        <button @click="removeChild(idx)" title="删除条件" style="color: red; background: none; border: none; font-size: 16px; cursor: pointer;">×</button>
      </div>
    </div>

    <div style="margin-top: 10px;">
      <button @click="addCondition" style="margin-right: 10px;">添加条件</button>
      <button @click="addGroup">添加子组</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ConditionGroup',
  props: {
    group: {
      type: Object,
      required: true
    },
    fields: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      localGroup: JSON.parse(JSON.stringify(this.group)) // 深拷贝，避免直接改 props
    };
  },
  watch: {
    group: {
      handler(newVal) {
        this.localGroup = JSON.parse(JSON.stringify(newVal));
      },
      deep: true
    }
  },
  methods: {
    emitUpdate() {
      this.$emit('update-group', JSON.parse(JSON.stringify(this.localGroup)));
    },
    updateChild(idx, newGroup) {
      this.localGroup.children.splice(idx, 1, newGroup);
      this.emitUpdate();
    },
    removeChild(idx) {
      this.localGroup.children.splice(idx, 1);
      this.emitUpdate();
    },
    addCondition() {
      this.localGroup.children.push({
        field: '',
        operator: '=',
        value: ''
      });
      this.emitUpdate();
    },
    addGroup() {
      this.localGroup.children.push({
        op: 'AND',
        children: []
      });
      this.emitUpdate();
    }
  }
};
</script>

<style scoped>
button:hover {
  opacity: 0.7;
}
</style>
