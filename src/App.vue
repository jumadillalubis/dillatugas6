<template>
  <div class="app-container">
    <form @submit.prevent="save" class="form-container">
      <input
        type="text"
        v-model="form.title"
        placeholder="Title"
        class="form-input"
      />
      <textarea
        v-model="form.content"
        placeholder="Content"
        class="form-input"
      ></textarea>
      <button type="submit" class="submit-button">{{ form.id ? 'Update' : 'Save' }}</button>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <div class="article">
          <h3 class="article-title">{{ article.title }}</h3>
          <p class="article-content">{{ article.content }}</p>
          <button @click="edit(article)" class="edit-button">Edit</button>
          <button @click="remove(article.id)" class="delete-button">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="load-button">Load</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      }
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        // Update existing article
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
        }
      } else {
        // Add new article
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load(); // Load articles when component is mounted
  }
};
</script>

<style>
.app-container {
  font-family: Arial, sans-serif;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #ffe4e1; /* Light pink background */
}

.form-container {
  margin-bottom: 20px;
  padding: 20px;
  border: 1px solid #ffb6c1;
  border-radius: 8px;
  background-color: #ffffff; /* White background for the form */
}

.form-input {
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ffb6c1;
  border-radius: 4px;
  box-sizing: border-box;
}

.submit-button {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  background-color: #d36980; /* Hot pink background */
  color: white;
  cursor: pointer;
}

.submit-button:hover {
  background-color: #f01149; /* Deep pink background on hover */
}

.article-list {
  list-style: none;
  padding: 0;
}

.article-item {
  margin-bottom: 15px;
}

.article {
  padding: 15px;
  border: 1px solid #ffb6c1;
  border-radius: 8px;
  background-color: #ffffff; /* White background for articles */
}

.article-title {
  margin-top: 0;
}

.article-content {
  margin-bottom: 10px;
}

.edit-button,
.delete-button,
.load-button {
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 5px;
}

.edit-button {
  background-color: #ff6976; /* Hot pink background */
  color: white;
}

.edit-button:hover {
  background-color: f01149; /* Deep pink background on hover */
}

.delete-button {
  background-color: #ff0000; /* Orange red background */
  color: white;
}

.delete-button:hover {
  background-color: #b30303; /* Red background on hover */
}

.load-button {
  display: block;
  width: 100%;
  padding: 10px;
  margin-top: 20px;
  border: none;
  border-radius: 4px;
  background-color: #eb3b67; /* Hot pink background */
  color: white;
}

.load-button:hover {
  background-color: #f01149; /* Deep pink background on hover */
}
</style>
