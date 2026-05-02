<template>
  <div class="min-vh-100 py-5" style="background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);">
    <div class="container">

      <!-- Header -->
      <div class="text-center mb-5">
        <div class="display-1 mb-2">👥</div>
        <h1 class="fw-bold text-white display-4">Employee Management System</h1>
        <p class="text-white-50 fs-5">Manage your workforce efficiently</p>
        <hr class="border-secondary w-25 mx-auto">
      </div>

      <!-- Alert -->
      <div v-if="alert.message" :class="`alert alert-${alert.type} alert-dismissible fade show rounded-4 shadow`" role="alert">
        {{ alert.message }}
        <button type="button" class="btn-close" @click="alert.message = ''"></button>
      </div>

      <!-- Form Card -->
      <div class="card border-0 rounded-4 shadow-lg mb-5" style="backdrop-filter: blur(10px); background: rgba(255,255,255,0.97);">
        <div class="card-header border-0 rounded-top-4 py-4 px-4" :style="editMode ? 'background: linear-gradient(90deg, #f7971e, #ffd200);' : 'background: linear-gradient(90deg, #11998e, #38ef7d);'">
          <h4 class="mb-0 fw-bold text-dark">
            {{ editMode ? '✏️ Update Employee Record' : '➕ Add New Employee' }}
          </h4>
        </div>
        <div class="card-body p-4">
          <div class="row g-4">
            <div class="col-md-6">
              <label class="form-label fw-bold text-secondary">👤 Full Name</label>
              <input v-model="form.name" type="text" class="form-control form-control-lg rounded-3 border-2" placeholder="e.g. John Doe" />
            </div>
            <div class="col-md-6">
              <label class="form-label fw-bold text-secondary">💼 Designation</label>
              <input v-model="form.designation" type="text" class="form-control form-control-lg rounded-3 border-2" placeholder="e.g. Software Engineer" />
            </div>
            <div class="col-md-6">
              <label class="form-label fw-bold text-secondary">🏢 Department</label>
              <input v-model="form.department" type="text" class="form-control form-control-lg rounded-3 border-2" placeholder="e.g. IT" />
            </div>
            <div class="col-md-6">
              <label class="form-label fw-bold text-secondary">💰 Salary (₹)</label>
              <input v-model="form.salary" type="number" class="form-control form-control-lg rounded-3 border-2" placeholder="e.g. 50000" />
            </div>
            <div class="col-12 d-flex gap-3 mt-2">
              <button class="btn btn-lg px-5 fw-bold rounded-3 text-white" :style="editMode ? 'background: linear-gradient(90deg, #f7971e, #ffd200); color: #000 !important;' : 'background: linear-gradient(90deg, #11998e, #38ef7d); color: #000 !important;'" @click="submitForm">
                {{ editMode ? '💾 Update Employee' : '➕ Add Employee' }}
              </button>
              <button class="btn btn-lg btn-outline-secondary px-4 fw-bold rounded-3" @click="resetForm">✖ Cancel</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Stats Row -->
      <div class="row g-4 mb-5">
        <div class="col-md-4">
          <div class="card border-0 rounded-4 shadow text-white text-center py-4" style="background: linear-gradient(135deg, #667eea, #764ba2);">
            <div class="fs-1 fw-bold">{{ employees.length }}</div>
            <div class="fs-6 opacity-75">Total Employees</div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card border-0 rounded-4 shadow text-white text-center py-4" style="background: linear-gradient(135deg, #f093fb, #f5576c);">
            <div class="fs-1 fw-bold">{{ departments.length }}</div>
            <div class="fs-6 opacity-75">Departments</div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="card border-0 rounded-4 shadow text-white text-center py-4" style="background: linear-gradient(135deg, #4facfe, #00f2fe);">
            <div class="fs-1 fw-bold">₹{{ avgSalary }}</div>
            <div class="fs-6 opacity-75">Avg. Salary</div>
          </div>
        </div>
      </div>

      <!-- Table Card -->
      <div class="card border-0 rounded-4 shadow-lg" style="background: rgba(255,255,255,0.97);">
        <div class="card-header border-0 rounded-top-4 py-4 px-4 d-flex justify-content-between align-items-center" style="background: linear-gradient(90deg, #0f2027, #2c5364);">
          <h4 class="mb-0 fw-bold text-white">📋 Employee Records</h4>
          <span class="badge rounded-pill fs-6 px-4 py-2" style="background: linear-gradient(90deg, #11998e, #38ef7d); color: #000;">
            {{ employees.length }} Employees
          </span>
        </div>
        <div class="card-body p-0">
          <div class="table-responsive">
            <table class="table table-hover align-middle mb-0">
              <thead style="background: #f8f9fa;">
                <tr>
                  <th class="ps-4 py-3 text-secondary">ID</th>
                  <th class="py-3 text-secondary">Name</th>
                  <th class="py-3 text-secondary">Designation</th>
                  <th class="py-3 text-secondary">Department</th>
                  <th class="py-3 text-secondary">Salary</th>
                  <th class="py-3 text-secondary text-center">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="employees.length === 0">
                  <td colspan="6" class="text-center py-5 text-muted fs-5">
                    😔 No employees found. Add one above!
                  </td>
                </tr>
                <tr v-for="emp in employees" :key="emp.id" class="border-bottom">
                  <td class="ps-4">
                    <span class="badge bg-dark rounded-pill px-3">#{{ emp.id }}</span>
                  </td>
                  <td>
                    <div class="d-flex align-items-center gap-2">
                      <div class="rounded-circle text-white d-flex align-items-center justify-content-center fw-bold" style="width:40px; height:40px; background: linear-gradient(135deg, #667eea, #764ba2); font-size: 16px;">
                        {{ emp.name ? emp.name[0].toUpperCase() : '?' }}
                      </div>
                      <span class="fw-semibold">{{ emp.name }}</span>
                    </div>
                  </td>
                  <td><span class="badge rounded-pill px-3 py-2" style="background:#e3f2fd; color:#1565c0;">{{ emp.designation }}</span></td>
                  <td><span class="badge rounded-pill px-3 py-2" style="background:#e8f5e9; color:#2e7d32;">{{ emp.department }}</span></td>
                  <td><span class="fw-bold" style="color:#2e7d32;">₹{{ Number(emp.salary).toLocaleString() }}</span></td>
                  <td class="text-center">
                    <button class="btn btn-sm fw-bold me-2 px-3 rounded-3" style="background: linear-gradient(90deg, #f7971e, #ffd200);" @click="editEmployee(emp)">✏️ Edit</button>
                    <button class="btn btn-sm fw-bold px-3 rounded-3" style="background: linear-gradient(90deg, #f5576c, #f093fb); color: white;" @click="deleteEmployee(emp.id)">🗑️ Delete</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import axios from 'axios'

const API = 'https://69f07c75c1533dbedc9d0b20.mockapi.io/api/employees'

export default {
  data() {
    return {
      employees: [],
      form: {
        name: '',
        designation: '',
        department: '',
        salary: ''
      },
      editMode: false,
      editId: null,
      alert: {
        message: '',
        type: 'success'
      }
    }
  },
  computed: {
    departments() {
      return [...new Set(this.employees.map(e => e.department).filter(Boolean))]
    },
    avgSalary() {
      if (!this.employees.length) return 0
      const avg = this.employees.reduce((sum, e) => sum + Number(e.salary), 0) / this.employees.length
      return Math.round(avg).toLocaleString()
    }
  },
  mounted() {
    this.fetchEmployees()
  },
  methods: {
    async fetchEmployees() {
      const res = await axios.get(API)
      this.employees = res.data
    },
    async submitForm() {
      if (!this.form.name || !this.form.designation || !this.form.department || !this.form.salary) {
        this.showAlert('Please fill in all fields!', 'danger')
        return
      }
      if (this.editMode) {
        await axios.put(`${API}/${this.editId}`, this.form)
        this.showAlert('Employee updated successfully!', 'success')
      } else {
        await axios.post(API, this.form)
        this.showAlert('Employee added successfully!', 'success')
      }
      this.resetForm()
      this.fetchEmployees()
    },
    editEmployee(emp) {
      this.form = { name: emp.name, designation: emp.designation, department: emp.department, salary: emp.salary }
      this.editMode = true
      this.editId = emp.id
      window.scrollTo({ top: 0, behavior: 'smooth' })
    },
    async deleteEmployee(id) {
      if (confirm('Are you sure you want to delete this employee?')) {
        await axios.delete(`${API}/${id}`)
        this.showAlert('Employee deleted successfully!', 'danger')
        this.fetchEmployees()
      }
    },
    resetForm() {
      this.form = { name: '', designation: '', department: '', salary: '' }
      this.editMode = false
      this.editId = null
    },
    showAlert(message, type) {
      this.alert = { message, type }
      setTimeout(() => { this.alert.message = '' }, 3000)
    }
  }
}
</script>