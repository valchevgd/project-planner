<template>
  <div class="home">
    <FilterNav @filterChange="filter = $event" :current-filter="filter"/>
    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject :project="project" @delete="handleDelete" @complete="handleComplete"/>
      </div>
    </div>
    <div v-else>
      <p>Loading projects...</p>
    </div>
  </div>
</template>

<script>
import SingleProject from '../components/SingleProject';
import FilterNav from '../components/FilterNav';

export default {
  name: 'Home',
  components: { SingleProject, FilterNav },
  data() {
    return {
      projects: [],
      filter: 'all'
    }
  },
  mounted() {
    fetch('http://localhost:3000/projects')
        .then(res => res.json())
        .then(data => this.projects = data)
        .catch(err => console.log(err.message))
  },
  methods: {
    handleDelete(id) {
      this.projects = this.projects.filter((project) => {
        return project.id !== id;
      });
    },
    handleComplete(id) {
      let project = this.projects.find(p => p.id === id);
      project.complete = ! project.complete;
    }
  },
  computed: {
    filteredProjects() {
      if (this.filter === 'completed') {
        return this.projects.filter(p => p.complete);
      }

      if (this.filter === 'ongoing') {
        return this.projects.filter(p => ! p.complete);
      }

      return this.projects;
    }
  }
}
</script>
