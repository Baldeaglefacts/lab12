<template>
  <div id="app">
    <h1 class="m-2">Student Sign In</h1>
    <new-student-form v-on:student-added="newStudentAdded"></new-student-form>
    <student-table v-bind:students="students"
                   v-on:student-present="studentArrivedOrLeft"
                   v-on:delete-student="studentDeleted">
    </student-table>
    <student-message v-bind:student="mostRecentStudent"></student-message>
  </div>
</template>

<script>
import NewStudentForm from './components/NewStudentForm.vue'
import StudentMessage from './components/StudentMessage.vue'
import StudentTable from './components/StudentTable.vue'

export default {
  name: 'app',
  components: {
    NewStudentForm,
    StudentMessage,
    StudentTable
  },
  data() {
    return {
      students: [],
      mostRecentStudent: {}
    }
  },
  mounted() {
    this.updateStudents()
  },
  methods: {
    newStudentAdded(student) {
      this.$student_api.addStudent(student).then( () => {
        this.updateStudents()
      }).catch( err => {
        console.log('Error fetching student list', err)
        if (err.response.data && Array.isArray(err.response.data)) {
          let msg = err.response.data.join(',')
          alert('Error adding student\n' + msg)
        }
        else {
          console.error('Error adding student', err.response)
          alert('Sorry, unable to add student')
        }
      })
    },
    studentArrivedOrLeft(student,present) {
      student.present = present
      this.$student_api.updateStudent(student).then( () => {
        this.mostRecentStudent = student
        this.updateStudents()
      }).catch( err => {
        console.error('Error updating student', err.response)
        alert('Sorry, unable to update student')
      })
    },
    studentDeleted(student) {
      this.$student_api.deleteStudent(student.id).then( () => {
        this.updateStudents()
        this.mostRecentStudent = {}
      }).catch( err => {
        console.error('Error deleting student', err.response)
        alert('Sorry, unable to delete student. They are too powerful to destroy.')
      })
    },
    updateStudents() {
      this.$student_api.getAllStudents().then( students => {
        this.students = students
      }).catch( err => {
        console.error('Error getting latest student list', err.response)
        alert('Sorry, unable to fetch student list')
      })
    }
  }
}
</script>

<style>
  @import "https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css";
</style>

