<template>
  <h1>{{ myname }}</h1>

  <div v-if="pending">Loading Projects...</div>
  <div>
    <h2>Projects</h2>
    <ul>
      <li v-for="project in projects" :key="project.name">
        <h3>{{ project.name }}</h3>
        <p>{{ project.description }}</p>
      </li>
    </ul>
  </div>

  <div v-if="pending1">Loading Blogs...</div>
  <div>
    <h2>Blogs</h2>
    <ul>
      <li v-for="blog in blogs">
        <h3>{{ blog.title }}</h3>
        <p>{{ blog.content }}</p>
      </li>
    </ul>
  </div>


  <div>
    <h1>Create a New Project</h1>
    <form @submit.prevent="createProject">
      <div>
        <label for="name">Project Name: </label>
        <input type="text" v-model="newProject.name" id="name" required />
      </div>
      <div>
        <label for="description">Project Description: </label>
        <textarea v-model="newProject.description" id="description" required></textarea>
      </div>
      <button type="submit">Create Project</button>
    </form>
  </div>

  <div>
    <h1>Update the Project</h1>
    <form @submit.prevent="deleteProject">
      <div>
        <label for="p_id">Project ID: </label>
        <input type="text" v-model="newProject.p_id" id="p_id" required />
      </div>
      <button type="submit">update the Project</button>
    </form>
  </div>


  <div>
    <h1>Delete Project</h1>
    <form @submit.prevent="deleteProject">
      <div>
        <label for="p_id">Project ID: </label>
        <input type="text" v-model="newProject.p_id" id="p_id" required />
      </div>
      <button type="submit">Delete the Project</button>
    </form>
  </div>


  <NuxtPage />
</template>

<script setup>


const myname = "Tharushi De Silva";
import { ref } from 'vue';
const {
  data: projects,
  pending,
  error,
} = useFetch("http://localhost:5000/Projects");
const {
  data: blogs,
  pending1,
  error1,
} = useFetch("http://localhost:5000/Blogs");
const newProject = ref({
  name: "",
  description: "",
});

const createProject = async () => {
  console.log(newProject.value);
  try {
    const response = await fetch('http://localhost:5000/Projects', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newProject.value),
    });

    if (!response.ok) {
      throw new Error("Failed to create project.");
    }

    const result = await response.json();
    projects.value.push(result);//add new project and display it within the UI itself without refreshing

    //Clear the form
    newProject.value.name = '';
    newProject.value.description = '';
  } catch (error) {
    console.error('Error: ', error);
  }
};

const updateProject = async () => {
  console.log(newProject.value);
  try {
    const response = await fetch(`http://localhost:5000/Projects/${newProject.value.id}`, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newProject.value),
    });
    if (!response.ok) {
      throw new Error("Failed to update project.");
    }

    const result = await response.json();
    const index = projects.value.findIndex(p => p.id === newProject.value.id);
    if (index !== -1) {
      projects.value[index] = result;
    }

    newProject.value.id = null;

  } catch (error) {
    console.error('Error: ', error);
  }
};

const deleteProject = async (id) => {
  try {
    const response = await fetch(`http://localhost:5000/Projects/${id}`, {
      method: 'DELETE',
    });
    if (!response.ok) {
      throw new Error("Failed to delete project.");
    }

    projects.value = projects.value.filter(p => p.id !== id);
  } catch (error) {
    console.error('Error: ', error);
  }
};

</script>
<style></style>