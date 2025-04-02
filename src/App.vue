<template>
  <div id="app">
    <header class="header-bar">
      <h1 class="logo">PROFILE</h1>
      <nav class="menu">
        <span @click="currentSection = 'home'" :class="{ active: currentSection === 'home' }">Home</span>
        <span @click="currentSection = 'student'" :class="{ active: currentSection === 'student' }">Student Info</span>
        <span @click="currentSection = 'contact'" :class="{ active: currentSection === 'contact' }">Contact</span>
      </nav>
    </header>

    <div class="view-container">
      <Home v-if="currentSection === 'home'" />
      <div v-else-if="currentSection === 'student'">
        <StudentInfo />
        <LearningDetails />
      </div>
      <Contact v-else-if="currentSection === 'contact'" />
    </div>
  </div>
</template>

<script>
import Home from './components/Home.vue';
import StudentInfo from './components/StudentInfo.vue';
import LearningDetails from './components/LearningDetails.vue';
import Contact from './components/Contact.vue';

export default {
  components: { Home, StudentInfo, LearningDetails, Contact },
  data() {
    return {
      currentSection: 'home'
    };
  }
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
  background-color: #313436;
  color: #f0f0f0;
  font-family: 'Segoe UI', Arial, sans-serif;
}

#app {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
}

.header-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #1E201E;
  padding: 15px 30px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.3);
  margin-bottom: 20px;
}

.logo {
  font-size: 24px;
  font-weight: bold;
  color: #ffffff;
  margin: 0;
}

.menu {
  display: flex;
  gap: 20px;
}

.menu span {
  cursor: pointer;
  color: #cccccc;
  font-weight: bold;
  transition: color 0.3s ease, transform 0.2s ease;
}

.menu span:hover {
  color: #ffffff;
  transform: scale(1.05);
}

.menu .active {
  color: #745151;
  border-bottom: 2px solid #745151;
  padding-bottom: 2px;
}
</style>
