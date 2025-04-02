<template>
  <section class="student-info-container">
    <header class="section-header">
      <h2 class="section-title">Student Information</h2>
      <button v-if="!isEditing" @click="isEditing = true" class="edit-btn" aria-label="Edit student information">
        <i class="fas fa-edit"></i> Edit
      </button>
    </header>

    <div class="info-display" v-if="!isEditing">
      <div class="profile-section">
        <img :src="`/${studentImages[currentImageIndex]}`" class="profile-img" @error="handleImageError" />
        <div class="controls">
          <button @click="prevImage" class="carousel-btn">⟨ Prev</button>
          <button @click="nextImage" class="carousel-btn">Next ⟩</button>
        </div>
        <div class="quick-info"><h3 class="student-name">{{ student.name }}</h3></div>
      </div>

      <div class="details-section">
        <div class="detail-item" v-for="(value, key) in displayFields" :key="key">
          <span class="detail-label">{{ key }}:</span>
          <span class="detail-value">{{ value }}</span>
        </div>
      </div>
    </div>

<!-- Edit Information Form -->
    <form v-else @submit.prevent="saveInfo" class="edit-form">
      <div class="form-grid">
        <div class="form-group">
          <label for="name">Full Name:</label>
          <input id="name" v-model.trim="student.name" required maxlength="20" />
        </div>

        <div class="form-group">
          <label for="id">Student ID :</label>
          <input id="id" v-model.trim="student.id" required pattern="[0-9]+" />
        </div>

        <div class="form-group">
          <label for="faculty">Faculty :</label>
          <input id="faculty" v-model.trim="student.faculty" required />
        </div>

        <div class="form-group">
          <label for="major">Major :</label>
          <input id="major" v-model.trim="student.major" required />
        </div>

        <div class="form-group">
          <label for="majorid">Major ID :</label>
          <input id="majorid" v-model.trim="student.majorid" required />
        </div>

        <div class="form-group">
          <label for="yr">Year:</label>
          <select id="yr" v-model="student.yr" required>
            <option disabled value="">Please select</option>
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
            <option value="4">4</option>
          </select>
        </div>  

        <div class="form-group">
          <label for="HighSchool">HighSchool :</label>
          <input id="HighSchool" v-model.trim="student.HighSchool" required />
        </div>

        <div class="form-group">
          <label for="email">Email :</label>
          <input id="email" v-model.trim="student.email" required type="email"/>
        </div>
      </div>

      <div class="form-actions">
        <button type="submit" class="save-btn"><i class="fas fa-save"></i> Save Changes</button>
        <button type="button" @click="cancelEdit" class="cancel-btn"><i class="fas fa-times"></i> Cancel</button>
      </div>
    </form>
    
  </section>
</template>

<script>
export default {
  data() {
    return {
      student: {},
      originalStudent: {},
      isEditing: false,
      studentImages: ['img/nay2.jpg', 'img/haerin.jpg', 'img/lisa.jpg', 'img/winter.jpg'],
      currentImageIndex: 0,
    };
  },
  computed: {
    displayFields() {
      return {
        ID : this.student.id,
        Faculty : this.student.faculty,
        Major : this.student.major,
        'Major ID' : this.student.majorid,
        Year : `ปี ${this.student.yr}`,
        HighSchool : this.student.HighSchool,
        Email : this.student.email,

      };
    }
  },
  mounted() {
    this.fetchStudent();
  },
  methods: {
    async fetchStudent() {
      try {
        const res = await fetch('http://localhost:3000/students/6630250150');
        if (!res.ok) throw new Error('Failed to fetch');
        const data = await res.json();
        this.student = data;
        this.originalStudent = { ...data };
        const idx = this.studentImages.indexOf(data.image);
        this.currentImageIndex = idx !== -1 ? idx : 0;
      } catch (err) {
        console.error(err);
      }
    },
    saveInfo() {
      this.isEditing = false;
      this.originalStudent = { ...this.student };

      this.$toast?.success('Information updated successfully!', { position: 'top-right', duration: 3000 });

      fetch(`http://localhost:3000/students/${this.student.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(this.student)
      }).catch(err => console.error('Failed to save:', err));
    },
    cancelEdit() {
      this.student = { ...this.originalStudent };
      this.isEditing = false;
    },
    handleImageError(e) {
      e.target.src = 'https://via.placeholder.com/150?text=No+Image';
    },
    nextImage() {
      this.currentImageIndex = (this.currentImageIndex + 1) % this.studentImages.length;
    },
    prevImage() {
      this.currentImageIndex = (this.currentImageIndex - 1 + this.studentImages.length) % this.studentImages.length;
    }
  }
};
</script>

<style scoped>

.student-info-container {
  max-width: 700px;
  margin: 2rem auto;
  padding: 2rem;
  background: #343434;
  border-radius: 12px;
  border: 1px solid #444;
  font-family: 'Segoe UI', sans-serif;
  color: #f0f0f0;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.section-title {
  font-size: 1.4rem;
  color: #ffffff;
  font-weight: 600;
}

.info-display {
  display: grid;
  grid-template-columns: 160px 1fr;
  gap: 2rem;
}

.profile-img {
  width: 150px;
  height: 150px;
  border-radius: 10px;
  object-fit: cover;
  display: block;
  margin: 0 auto;
  border: 2px solid #666;
  box-shadow: 0 2px 8px rgba(255,255,255,0.05);
}

.quick-info {
  text-align: center;
  margin-top: 1rem;
}

.student-name {
  font-size: 1.2rem;
  font-weight: 500;
  color: #eaeaea;
}

.details-section {
  display: grid;
  gap: 1rem;
}

.detail-item {
  display: flex;
  justify-content: space-between;
  font-size: 0.95rem;
  color: #dddddd;
}

.detail-label {
  font-weight: 500;
  color: #cccccc;
}

.detail-value {
  color: #ffffff;
}

.edit-form {
  margin-top: 1.5rem;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.2rem;
  font-weight: 500;
  color: #dddddd;
}

input, select, textarea {
  width: 100%;
  padding: 0.6rem;
  border: 1px solid #555;
  border-radius: 6px;
  background: #2b2b2b;
  color: #f0f0f0;
  font-size: 0.95rem;
}

input:focus, select:focus, textarea:focus {
  border-color: #888;
  outline: none;
}

button {
  background: none;
  border: none;
  padding: 0.7rem 1.4rem;
  border-radius: 6px;
  font-size: 0.95rem;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.2s ease;
}

.edit-btn {
  color: #6ea8ff;
  border: 1px solid #6ea8ff;
}

.edit-btn:hover {
  background: #444;
}

.save-btn {
  color: #4ade80;
  border: 1px solid #4ade80;
}

.save-btn:hover {
  background: #2e4633;
}

.cancel-btn {
  color: #f87171;
  border: 1px solid #f87171;
}

.cancel-btn:hover {
  background: #4a2c2c;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  margin-top: 2rem;
  padding-top: 1.5rem;
  border-top: 1px solid #444;
}

@media (max-width: 768px) {
  .info-display, .form-grid {
    grid-template-columns: 1fr;
  }

  .form-actions {
    flex-direction: column;
  }

  button {
    width: 100%;
    justify-content: center;
  }
}

.controls {
  display: flex;
  justify-content: center;
  gap: 0.75rem;
  margin: 0.5rem 0;
}

.controls button, .carousel-btn {
  padding: 0.25rem 0.5rem;
  font-size: 0.85rem;
  border: 1px solid #555;
  background: #2b2b2b;
  color: #f0f0f0;
  border-radius: 5px;
  cursor: pointer;
  transition: 0.2s ease;
}

.carousel-btn {
  padding: 0.4rem 1rem;
  border-radius: 6px;
  color: #cccccc;
}

.carousel-btn:hover {
  background: #444;
}

</style>