<template>
  <h1>{{ myname }}</h1>

  <div v-if="pending">Loading Projects...</div>
  <div>
    <h2>Projects</h2>
    <ul>
      <li v-for="Project in Projects" :key="Project.name">
        <h3>{{ Project.name }}</h3>
        <p>{{ Project.description }}</p>
      </li>
    </ul>
  </div>

  <div v-if="pending1">Loading Blogs...</div>
  <div>
    <h2>Blogs</h2>
    <ul>
      <li v-for="Blog in Blogs">
        <h3>{{ Blog.title }}</h3>
        <p>{{ Blog.content }}</p>
      </li>
    </ul>
  </div>


  <div class>
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
    <form @submit.prevent="updateProject">
      <div>
        <label for="id">Project ID: </label>
        <!--<input type="text" v-model="updateProject.id" id="p_id" required />-->
        <select v-model="updateProject.id" id="ProjecttID" required>
          <option v-for="Project in Projects" :key="Project._id" :value="Project._id">{{ Project.name }}</option>
        </select>
      </div>
      <button type="submit">update the Project</button>
    </form>
  </div>


  <div>
    <h1>Delete Project</h1>
    <form @submit.prevent="deleteProject">
      <div>
        <label for="id">Project ID: </label>
        <input type="text" v-model="deleteProject.id" id="ProjectID" required />
      </div>
      <button type="submit">Delete the Project</button>
    </form>
  </div>



</template>

<script setup>


const myname = "Tharushi De Silva";
//import { ref } from 'vue';

const {
  data: Projects,
  pending1,
  error1,
} = useFetch("http://localhost:5000/Projects");
const {
  data: Blogs,
  pending2,
  error2,
} = useFetch("http://localhost:5000/Blogs");

const newProject = ref({
  name: '',
  description: '',
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

    Projects.value.push(result);//add new project and display it within the UI itself without refreshing

    //Clear the form
    newProject.value.name = '';
    newProject.value.description = '';
  } catch (error) {
    console.error(error);
  }
};


const updateProject = ref({
  id: '',
  name: '',
  description: '',
});

const editProject = async () => {
  console.log(updateProject.value);
  try {
    const response = await fetch(`http://localhost:5000/Projects/${updateProject.value.id}`, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(updateProject.value),
    });
    if (!response.ok) {
      throw new Error("Failed to update project.");
    }

    const result = await response.json();
    const index = Projects.value.findIndex(project => project.id === updateProject.value.id);
    if (index !== -1) {
      Projects.value[index] = result;
    }
    updateProject.value.id = '';
    updateProject.value.name = '';
    updateProject.value.description = '';

  } catch (error) {
    console.error('Error:', error);
  }

};

const deleteProject = ref({
  id: '',
});

const removeProject = async () => {
  console.log(deleteProject.value);
  try {
    const response = await fetch(`http://localhost:5000/Projects/${deleteProject.value.id}`, {
      method: 'DELETE',
    });
    if (!response.ok) {
      throw new Error("Failed to delete project.");
    }
    const result = await response.json();
    //to Remove the project from the list
    const index = Projects.value.findIndex((project) => project._id === deleteProject.value.id);
    if (index !== -1) {
      Projects.value.splice(index, 1);
    }

    //clear the form
    deleteProject.value.id = '';
  } catch (error) {
    console.error(error);
  }
};

</script>
<style>

.form {
  max-width: 400px;
  margin: 0 auto;
}

label {
  display:block;
  font-weight: bold;
}

input[type="text"],
textarea {
  width: 50%;
  padding: 8px;
  border: 1px solid #000000;
  border-radius: 5px;
}

</style>
