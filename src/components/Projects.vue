<template>
  <!-- My Projects Section -->

  <section class="px-5" >
    <h1 class="pt-5 mt-5 text-center" id="projects">My Projects</h1>

  
    <div 
      v-for="(group, index) in chunkedProjects" 
      :key="index" 
      class="row my-5 justify-content-center"
    >
      
      <ProjectCard 
        v-for="project in group" 
        :key="project.id" 
        :project="project"
      />
    </div>
  </section>
</template>

<script setup>
import { computed } from 'vue'
import ProjectCard from './ProjectCard.vue'
import projects from '../data/projects.json'

const chunkSize = 3

const chunkedProjects = computed(() => {
  const chunks = []
  for (let i = 0; i < projects.length; i += chunkSize) {
    chunks.push(projects.slice(i, i + chunkSize))
  }
  return chunks
})
</script>
