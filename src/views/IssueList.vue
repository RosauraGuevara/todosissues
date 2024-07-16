<template>
  <div>
    <h1>Lista de problemas</h1>
    <el-button type="success" @click="getIssues()">Obtener incidencias</el-button>
    <el-row :gutter="12">
      <el-col :span="12" v-for="issue in issues" :key="issue.id">
        <el-card class="box-card" shadow="hover" style="margin: 5px 0;">
          <el-row :gutter="12">
            <el-col :span="21">{{ issue.title }}</el-col>
            <el-col :span="3">
              <el-button @click="closeIssue(issue.number)" type="success" icon="el-icon-check" circle></el-button>
            </el-col>
          </el-row>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'IssueList',
  data() {
    return {
      issues: []
    };
  },
  methods: {
    async getIssues() {
      try {
        const response = await axios.get('https://api.github.com/repos/RosauraGuevara/toDo/issues', {
          headers: {
            'Authorization': '${process.env.VUE_APP_GITHUB_TOKEN}'
          }
        });
        this.issues = response.data;
        console.log('Issues:', this.issues);
      } catch (error) {
        console.error('Error fetching issues:', error);
      }
    },
    async closeIssue(issueNumber) {
      try {
        const response = await axios.patch(`https://api.github.com/repos/RosauraGuevara/toDo/issues/${issueNumber}`, {
          state: 'closed'
        }, {
          headers: {
            'Authorization': 'token ${process.env.VUE_APP_GITHUB_TOKEN}'
          }
        });
        console.log('Issue cerrado:', response.data);
        this.issues = this.issues.filter(issue => issue.number !== issueNumber);
      } catch (error) {
        console.error('Error cerrando issue:', error);
      }
    }
  }
};
</script>

<style>

</style>
