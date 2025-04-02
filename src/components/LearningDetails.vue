<template>
  <section class="learning-details">
    <h2>Learning Details</h2>
    
    <table>
      <thead>
        <tr>
          <th>Course Code</th>
          <th>Course Name</th>
          <th>Credits</th>
          <th>Grade</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(course, index) in courses" :key="index">
          <td>{{ course.code }}</td>
          <td>{{ course.name }}</td>
          <td>{{ course.credits }}</td>
          <td>{{ course.grade }}</td>
          <td>
            <button @click="editCourse(index)">Edit</button>
            <button @click="deleteCourse(index)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

<!-- Add Course Form -->
    <button @click="openAddForm" v-if="!showAddForm && !editingCourse" class="add-course-btn">Add Course</button>
    <form v-if="showAddForm" ref="AddForm" @submit.prevent="addCourse" class="course-form">
      <h3>Add New Course</h3>
      <div class="form-group">
        <label>Course Code:</label>
        <input v-model="newCourse.code"  required pattern="^\d{8}$"  maxlength="8"  title="number of digits must be 8"/>
      </div>

      <div class="form-group">
        <label>Course Name:</label>
        <input v-model="newCourse.name" required />
      </div>

      <div class="form-group">
        <label>Credits:</label>
        <select v-model="newCourse.credits" required>
          <option disabled value="">Please select</option>
          <option :value="1">1</option>
          <option :value="2">2</option>
          <option :value="3">3</option>
        </select>
      </div>

      <div class="form-group">
        <label>Grade:</label>
        <select v-model="newCourse.grade" required>
          <option disabled value="">Please select</option>
          <option>A+</option>
          <option>A</option>
          <option>A-</option>
          <option>B+</option>
          <option>B</option>
          <option>B-</option>
          <option>C+</option>
          <option>C</option>
          <option>C-</option>
          <option>D+</option>
          <option>D</option>
          <option>D-</option>
          <option>F</option>
        </select>
      </div>

      <button type="submit">Save</button>
      <button type="button" @click="cancelAdd">Cancel</button>
    </form>

<!-- Edit Course Form -->
    <form v-if="editingCourse" ref="editForm" @submit.prevent="saveEditedCourse" class="course-form">
      <h3>Edit Course</h3>
      <div class="form-group">
        <label>Course Code:</label>
        <input v-model="editingCourse.code"  required pattern="^\d{8}$"  maxlength="8"  title="number of digits must be 8"/>
      </div>

      <div class="form-group">
        <label>Course Name:</label>
        <input v-model="editingCourse.name" required />
      </div>

      <div class="form-group">
        <label>Credits:</label>
        <select v-model.number="editingCourse.credits" required>
          <option disabled value="">Please select</option>
          <option :value="1">1</option>
          <option :value="2">2</option>
          <option :value="3">3</option>
        </select>
      </div>

      <div class="form-group">
        <label>Grade:</label>
        <select v-model="editingCourse.grade" required>
          <option disabled value="">Please select</option>
          <option>A+</option>
          <option>A</option>
          <option>A-</option>
          <option>B+</option>
          <option>B</option>
          <option>B-</option>
          <option>C+</option>
          <option>C</option>
          <option>C-</option>
          <option>D+</option>
          <option>D</option>
          <option>D-</option>
          <option>F</option>
        </select>
      </div>

      <button type="submit">Save</button>
      <button type="button" @click="cancelEdit">Cancel</button>
    </form>

  </section>
</template>

<script>
export default {
  data() {
    return {
      courses: [],
      showAddForm: false,
      newCourse: this.getEmptyCourse(),
      editingCourse: null,
      editingIndex: -1
    };
  },
  mounted() {
    this.fetchCourses();
  },
  methods: {
    getEmptyCourse() {
      return { code: '', name: '', credits: 0, grade: '' };
    },
    async fetchCourses() {
      try {
        const response = await fetch("http://localhost:3000/subjects");
        const data = await response.json();
        this.courses = data.map(subject => ({
          code: subject.id,
          name: subject.name,
          credits: subject.credits,
          grade: subject.grade
        }));
      } catch (error) {
        console.error("Failed to load courses:", error);
      }
    },
    async addCourse() {
      try {
        const response = await fetch("http://localhost:3000/subjects", {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            id: this.newCourse.code,
            name: this.newCourse.name,
            credits: this.newCourse.credits,
            grade: this.newCourse.grade
          })
        });
        if (response.ok) {
          this.courses.push({ ...this.newCourse });
          this.newCourse = this.getEmptyCourse();
          this.showAddForm = false;
        }
      } catch (error) {
        console.error("Failed to add course:", error);
      }
    },
    
    openAddForm() {
      this.showAddForm = true;
      this.$nextTick(() => {
        this.$refs.AddForm?.scrollIntoView({ behavior: 'smooth' });
      });
    },
    cancelAdd() {
      this.newCourse = this.getEmptyCourse();
      this.showAddForm = false;
    },
    editCourse(index) {
      this.editingCourse = { ...this.courses[index] };
      this.editingIndex = index;
      this.$nextTick(() => {
      this.$refs.editForm?.scrollIntoView({ behavior: 'smooth' });
  });
    },
    async saveEditedCourse() {
      try {
        const response = await fetch(`http://localhost:3000/subjects/${this.editingCourse.code}`, {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            id: this.editingCourse.code,
            name: this.editingCourse.name,
            credits: this.editingCourse.credits,
            grade: this.editingCourse.grade
          })
        });
        if (response.ok) {
          this.courses.splice(this.editingIndex, 1, { ...this.editingCourse });
          this.cancelEdit();
        }
      } catch (error) {
        console.error("Failed to update course:", error);
      }
    },
    cancelEdit() {
      this.editingCourse = null;
      this.editingIndex = -1;
    },
    async deleteCourse(index) {
      const code = this.courses[index].code;
      try {
        const response = await fetch(`http://localhost:3000/subjects/${code}`, {
          method: 'DELETE'
        });
        if (response.ok) {
          this.courses.splice(index, 1);
        }
      } catch (error) {
        console.error("Failed to delete course:", error);
      }
    }
  }
};
</script>


<style scoped>
.learning-details {
  max-width: 800px;
  margin: 2rem auto;
  padding: 2rem;
  background: #343434;
  border-radius: 12px;
  border: 1px solid #444;
  font-family: 'Segoe UI', sans-serif;
  color: #f0f0f0;
  box-shadow: 0 2px 8px rgba(255,255,255,0.05);
}

h2, h3 {
  margin-bottom: 1.2rem;
  font-weight: 600;
  color: #ffffff;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.95rem;
  margin-bottom: 1.5rem;
  color: #eaeaea;
}

th, td {
  padding: 0.75rem;
  text-align: left;
  border-bottom: 1px solid #555;
}

th {
  background: #2b2b2b;
  font-weight: 500;
  color: #dddddd;
}

td button {
  padding: 0.4rem 0.8rem;
  font-size: 0.85rem;
  border: 1px solid #666;
  background: #2b2b2b;
  border-radius: 6px;
  color: #f0f0f0;
  cursor: pointer;
  transition: background 0.2s ease;
}

td button:hover {
  background: #444;
}

/* ฟอร์ม */
.course-form {
  margin-top: 2rem;
  padding: 2rem;
  background: #2b2b2b;
  border: 1px solid #555;
  border-radius: 12px;
  box-shadow: 0 2px 6px rgba(255,255,255,0.05);
  color: #f0f0f0;
}

.form-group {
  margin-bottom: 1.5rem;
}

label {
  display: block;
  font-size: 0.9rem;
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: #dddddd;
}

input, select, textarea {
  width: 100%;
  padding: 0.6rem;
  font-size: 0.95rem;
  border: 1px solid #555;
  border-radius: 6px;
  background: #1f1f1f;
  color: #f0f0f0;
}

input:focus, select:focus, textarea:focus {
  border-color: #888;
  outline: none;
}

/* ปุ่ม */
button[type="submit"],
button[type="button"],
.add-course-btn {
  padding: 0.6rem 1.2rem;
  font-size: 0.95rem;
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.2s ease;
  border: 1px solid #ccc;
  background: #444;
  color: #ffffff;
}

button:hover {
  background: #555;
}

.add-course-btn {
  margin-bottom: 1rem;
}
</style>





