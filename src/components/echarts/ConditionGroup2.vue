<template>
    <div class="query-builder" style="padding:20px; max-width:900px; margin:auto;">
      <h2>可视化 SQL 查询构建器</h2>
  
      <!-- 选择表 -->
      <div style="margin-bottom: 10px;">
        <label>选择表：</label>
        <select v-model="query.table">
          <option disabled value="">请选择</option>
          <option v-for="t in tables" :key="t" :value="t">{{ t }}</option>
        </select>
      </div>
  
      <!-- 字段多选 -->
      <div style="margin-bottom: 10px;">
        <label>选择字段：</label>
        <select v-model="query.select" multiple size="6" style="min-width: 250px;">
          <option v-for="f in fields" :key="f" :value="f">{{ f }}</option>
        </select>
      </div>
  
      <!-- 聚合字段 -->
      <div style="margin-bottom: 10px;">
        <label>聚合字段：</label>
        <div v-for="(agg, i) in query.aggregates" :key="i" style="margin-bottom: 5px;">
          <select v-model="agg.func" style="width: 100px;">
            <option disabled value="">函数</option>
            <option>SUM</option>
            <option>COUNT</option>
            <option>AVG</option>
            <option>MAX</option>
            <option>MIN</option>
          </select>
          (
          <select v-model="agg.field" style="width: 150px;">
            <option disabled value="">字段</option>
            <option v-for="f in fields" :key="f" :value="f">{{ f }}</option>
          </select>
          )
          <button @click="removeAggregate(i)">删除</button>
        </div>
        <button @click="addAggregate">+ 添加聚合字段</button>
      </div>
  
      <!-- 递归条件构建器 -->
      <div style="margin-bottom: 10px;">
        <label>查询条件：</label>
        <condition-group
          :group="query.where"
          :fields="fields"
          @update-group="val => query.where = val"
        />
      </div>
  
      <!-- 其他参数 -->
      <div style="margin-bottom: 10px;">
        <label>GROUP BY：</label>
        <input v-model="query.groupBy" placeholder="字段1, 字段2" style="width: 300px;" />
      </div>
      <div style="margin-bottom: 10px;">
        <label>HAVING：</label>
        <input v-model="query.having" placeholder="SUM(price) > 100" style="width: 300px;" />
      </div>
      <div style="margin-bottom: 10px;">
        <label>ORDER BY：</label>
        <input v-model="query.orderBy" placeholder="price DESC" style="width: 300px;" />
      </div>
      <div style="margin-bottom: 10px;">
        <label>LIMIT：</label>
        <input v-model.number="query.limit" type="number" min="0" style="width: 100px;" />
      </div>
  
      <!-- 生成按钮 -->
      <button @click="generateSQL" style="padding: 8px 16px; font-size: 16px;">生成 SQL</button>
  
      <!-- SQL 输出 -->
      <div v-if="sql" style="margin-top: 20px; white-space: pre-wrap; background: #f4f4f4; padding: 15px; border-radius: 5px; font-family: monospace;">
        <h3>生成的 SQL:</h3>
        {{ sql }}
      </div>
    </div>
  </template>
  
  <script>
  import ConditionGroup from './ConditionGroup.vue';
  
  export default {
    components: { ConditionGroup },
    data() {
      return {
        tables: ['orders', 'products', 'users'],
        fields: ['id', 'name', 'price', 'quantity', 'status', 'created_at'],
        query: {
          table: '',
          select: [],
          aggregates: [],
          where: { op: 'AND', children: [] },
          groupBy: '',
          having: '',
          orderBy: '',
          limit: 100,
        },
        sql: ''
      };
    },
    methods: {
      addAggregate() {
        this.query.aggregates.push({ func: 'SUM', field: this.fields[0] });
      },
      removeAggregate(idx) {
        this.query.aggregates.splice(idx, 1);
      },
      generateSQL() {
        if (!this.query.table) {
          alert('请选择数据表');
          return;
        }
        const selects = [];
        if (this.query.select.length > 0) {
          selects.push(...this.query.select);
        }
        this.query.aggregates.forEach(agg => {
          if (agg.func && agg.field) {
            selects.push(`${agg.func}(${agg.field})`);
          }
        });
        if (selects.length === 0) {
          alert('请至少选择一个字段或聚合字段');
          return;
        }
        let sql = `SELECT ${selects.join(', ')} FROM ${this.query.table}`;
  
        const whereStr = this.parseConditionGroup(this.query.where);
        if (whereStr) sql += ` WHERE ${whereStr}`;
  
        if (this.query.groupBy) sql += ` GROUP BY ${this.query.groupBy}`;
        if (this.query.having) sql += ` HAVING ${this.query.having}`;
        if (this.query.orderBy) sql += ` ORDER BY ${this.query.orderBy}`;
        if (this.query.limit) sql += ` LIMIT ${this.query.limit}`;
  
        this.sql = sql + ';';
      },
      parseConditionGroup(group) {
        if (!group || !group.children || group.children.length === 0) return '';
        const parts = group.children.map(child => {
          if (child.children) {
            return `(${this.parseConditionGroup(child)})`;
          } else {
            if (!child.field || !child.operator) return '';
            const val = typeof child.value === 'string' ? `'${child.value}'` : child.value;
            return `${child.field} ${child.operator} ${val}`;
          }
        }).filter(Boolean);
        return parts.join(` ${group.op} `);
      }
    }
  };
  </script>
  
  <style scoped>
  label {
    font-weight: 600;
    margin-right: 8px;
  }
  button {
    cursor: pointer;
  }
  </style>
  