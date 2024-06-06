<template>
  <div class="people-list">
    <h2>People List</h2>
    <ul>
      <li v-for="(person, index) in people" :key="index" class="person-item">
        <span>{{ person.name }}</span> - <span>{{ person.age }} years old</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      people: [
        { name: 'John', age: 30 },
        { name: 'Alice', age: 25 },
        { name: 'Bob', age: 35 },
        { name: 'Emma', age: 28 }
      ]
    };
  }
};
</script>

<style scoped>
.people-list {
  width: 300px;
  margin: auto;
}

h2 {
  text-align: center;
}

.person-item {
  list-style: none;
  margin-bottom: 10px;
}
</style>
