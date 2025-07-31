<template>
    <div style="padding: 20px">
      <h2>可视化查询工具</h2>
  
      <!-- 表选择 -->
      <div>
        <label>选择表：</label>
        <select v-model="selectedTable">
          <option v-for="table in tables" :key="table" :value="table">{{ table }}</option>
        </select>
      </div>
  
      <!-- 字段选择 -->
      <div style="margin-top: 10px;">
        <label>选择字段：</label>
        <div>
          <label v-for="field in fields" :key="field">
            <input type="checkbox" v-model="selectedFields" :value="field" /> {{ field }}
          </label>
        </div>
      </div>
  
      <!-- 查询条件 -->
      <div style="margin-top: 10px;">
        <label>查询条件：</label>
        <div v-for="(cond, index) in conditions" :key="index" style="margin-bottom: 5px;">
          <select v-model="cond.field">
            <option disabled value="">选择字段</option>
            <option v-for="field in fields" :key="field" :value="field">{{ field }}</option>
          </select>
          <select v-model="cond.operator">
            <option value="=">=</option>
            <option value="!=">!=</option>
            <option value=">">&gt;</option>
            <option value="<">&lt;</option>
            <option value="LIKE">包含</option>
          </select>
          <input v-model="cond.value" placeholder="值" />
          <button @click="removeCondition(index)">删除</button>
        </div>
        <button @click="addCondition">+ 添加条件</button>
      </div>
  
      <!-- 查询按钮 -->
      <div style="margin-top: 15px;">
        <button @click="executeQuery">执行查询</button>
      </div>
  
      <!-- SQL 预览 -->
      <div v-if="sql" style="margin-top: 15px;">
        <strong>生成的 SQL：</strong>
        <pre>{{ sql }}</pre>
      </div>
  
      <!-- 结果展示（模拟） -->
      <div v-if="result.length > 0" style="margin-top: 10px;">
        <table border="1" cellpadding="5">
          <thead>
            <tr>
              <th v-for="key in selectedFields" :key="key">{{ key }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, index) in result" :key="index">
              <td v-for="key in selectedFields" :key="key">{{ row[key] }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        tables: ["users", "orders", "products"], // 可替换为 API 返回的表
        selectedTable: "users",
        fields: ["id", "name", "age", "email"], // 可替换为动态字段
        selectedFields: ["id", "name"],
        conditions: [],
        sql: "",
        result: [] // 模拟结果
      };
    },
    methods: {
      addCondition() {
        this.conditions.push({ field: "", operator: "=", value: "" });
      },
      removeCondition(index) {
        this.conditions.splice(index, 1);
      },
      executeQuery() {
        if (!this.selectedTable || this.selectedFields.length === 0) {
          alert("请选择表和字段");
          return;
        }
  
        // 构建 SQL
        let sql = `SELECT ${this.selectedFields.join(", ")} FROM ${this.selectedTable}`;
        if (this.conditions.length > 0) {
          const where = this.conditions
            .filter(c => c.field && c.value !== "")
            .map(c => {
              if (c.operator === "LIKE") {
                return `${c.field} LIKE '%${c.value}%'`;
              }
              return `${c.field} ${c.operator} '${c.value}'`;
            })
            .join(" AND ");
          if (where) sql += ` WHERE ${where}`;
        }
  
        this.sql = sql;
  
        // 模拟查询结果
        this.result = [
          { id: 1, name: "Alice", age: 25, email: "alice@example.com" },
          { id: 2, name: "Bob", age: 30, email: "bob@example.com" }
        ];
      }
    }
  };
  </script>
  
  <style scoped>
  label {
    margin-right: 10px;
  }
  </style>
  