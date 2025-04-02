<template>
  <div>
    <h1>MY PROFILE</h1>
    <p>Welcome to MyProfile</p>
    <img src="/img/nay1.jpg" alt="Profile Picture" class="profile-img" />
    <div class="profile-description">
      <p>
        This profile is part of Project 2024_02 from the course 01418214 Practicum in Software Development.
        It displays student information and learning details, built with Vue.js and connected to a JSON server.
        Designed to be simple and editable, this project offers hands-on web development experience.
      </p>
      <p>-- CLICK STUDENT-INFO --</p>
    </div>
  </div>
</template>

<style scoped>
h1 {
  margin-top: 2rem;
  color: #fff;
  text-align: center;
}
p {
  color: #f0f0f0;
  text-align: center;
}
body {
  background-color: #313436;
  margin: 0;
  padding: 0;
}
.profile-description p {
  margin: 1rem auto;
  max-width: 800px;
  text-align: left;
  line-height: 1.8;
}
.profile-img {
  display: block;
  margin: 1rem auto;
  max-width: 200px;
  border-radius: 10px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.3);
}
</style>