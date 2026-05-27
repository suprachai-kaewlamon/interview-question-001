<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import Swal from 'sweetalert2';

const API_URL = 'http://localhost:5188/api/employees';

interface Employee {
  id?: number;
  fullName: string;
  address: string;
  dateOfBirth: string;
}

const employees = ref<Employee[]>([]);
const showAddModal = ref(false);
const showViewModal = ref(false);
const selectedEmployee = ref<Employee | null>(null);

const newEmployee = ref({
  firstName: '',
  lastName: '',
  address: '',
  dateOfBirth: ''
});

const fetchEmployees = async () => {
  try {
    const response = await axios.get(API_URL);
    employees.value = response.data;
  } catch (error) {
    console.error("ดึงข้อมูลไม่สำเร็จ:", error);
  }
};

onMounted(() => {
  fetchEmployees();
});

const calculateAgeDetail = (dobString: string) => {
  if (!dobString) return '';
  const birthYear = new Date(dobString).getFullYear();
  const currentYear = new Date().getFullYear();
  return `${currentYear - birthYear} ปี`;
};

const calculateNewAge = computed(() => {
  return calculateAgeDetail(newEmployee.value.dateOfBirth);
});

const openAddModal = () => {
  newEmployee.value = { firstName: '', lastName: '', address: '', dateOfBirth: '' };
  showAddModal.value = true;
};
const closeAddModal = () => showAddModal.value = false;

const openViewModal = (emp: Employee) => {
  selectedEmployee.value = emp;
  showViewModal.value = true;
};
const closeViewModal = () => showViewModal.value = false;

const saveEmployee = async () => {
  if (!newEmployee.value.firstName || !newEmployee.value.lastName || !newEmployee.value.address || !newEmployee.value.dateOfBirth) {
    Swal.fire('แจ้งเตือน', 'กรุณากรอกข้อมูลให้ครบถ้วนนะคะ', 'warning');
    return;
  }

  try {
    const combinedFullName = `${newEmployee.value.firstName} ${newEmployee.value.lastName}`.trim();

    const payload = {
      fullName: combinedFullName,
      address: newEmployee.value.address,
      dateOfBirth: new Date(newEmployee.value.dateOfBirth).toISOString()
    };
    
    await axios.post(API_URL, payload);
    closeAddModal();
    fetchEmployees(); 
    Swal.fire('สำเร็จ!', 'บันทึกข้อมูลเรียบร้อย', 'success');
  } catch (error) {
    console.error("บันทึกข้อมูลไม่สำเร็จ:", error);
    Swal.fire('ข้อผิดพลาด', 'เกิดข้อผิดพลาดในการบันทึกข้อมูล', 'error');
  }
};
</script>

<template>
  <div class="container">
    <div class="header">
      <h2>ระบบจัดการข้อมูลพนักงาน</h2>
      <button class="btn btn-add" @click="openAddModal">เพิ่มพนักงานใหม่</button>
    </div>
    
    <div class="table-responsive">
      <table>
        <thead>
          <tr>
            <th>ลำดับ</th>
            <th>ชื่อ-นามสกุล</th>
            <th>ที่อยู่</th>
            <th>วันเกิด</th>
            <th>อายุ</th>
            <th>การจัดการ</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(emp, index) in employees" :key="emp.id">
            <td>{{ index + 1 }}</td>
            <td>{{ emp.fullName }}</td>
            <td>{{ emp.address }}</td>
            <td>{{ new Date(emp.dateOfBirth).toLocaleDateString('th-TH') }}</td>
            <td>{{ calculateAgeDetail(emp.dateOfBirth) }}</td>
            <td><button class="btn btn-info" @click="openViewModal(emp)">ดูรายละเอียด</button></td>
          </tr>
          <tr v-if="employees.length === 0">
            <td colspan="6" class="empty-text">ไม่มีข้อมูลพนักงาน</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-if="showAddModal" class="modal-overlay">
      <div class="modal-box">
        <h3>เพิ่มพนักงานใหม่</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label>ชื่อ:</label>
            <input type="text" v-model="newEmployee.firstName" />
          </div>
          <div class="form-group">
            <label>นามสกุล:</label>
            <input type="text" v-model="newEmployee.lastName" />
          </div>
        </div>

        <div class="form-group">
          <label>วันเกิด:</label>
          <input type="date" v-model="newEmployee.dateOfBirth" />
        </div>
        <div class="form-group">
          <label>อายุ :</label>
          <input type="text" :value="calculateNewAge" disabled class="disabled-input" />
        </div>
        <div class="form-group">
          <label>ที่อยู่:</label>
          <textarea v-model="newEmployee.address" rows="3"></textarea>
        </div>
        <div class="modal-actions">
          <button class="btn btn-add" @click="saveEmployee">บันทึก</button>
          <button class="btn btn-cancel" @click="closeAddModal">ยกเลิก</button>
        </div>
      </div>
    </div>

    <div v-if="showViewModal" class="modal-overlay">
      <div class="modal-box">
        <h3>รายละเอียดพนักงาน</h3>
        <div class="form-group">
          <label>ชื่อ-นามสกุล:</label>
          <input type="text" :value="selectedEmployee?.fullName" disabled class="disabled-input" />
        </div>
        <div class="form-group">
          <label>วันเกิด:</label>
          <input type="text" :value="selectedEmployee ? new Date(selectedEmployee.dateOfBirth).toLocaleDateString('th-TH') : ''" disabled class="disabled-input" />
        </div>
        <div class="form-group">
          <label>อายุ:</label>
          <input type="text" :value="selectedEmployee ? calculateAgeDetail(selectedEmployee.dateOfBirth) : ''" disabled class="disabled-input" />
        </div>
        <div class="form-group">
          <label>ที่อยู่:</label>
          <textarea :value="selectedEmployee?.address" rows="3" disabled class="disabled-input"></textarea>
        </div>
        <div class="modal-actions">
          <button class="btn btn-cancel" @click="closeViewModal">ปิด</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  font-size: 16px;
}

.container {
  width: 95%; 
  max-width: 1400px; 
  margin: 0px auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}
.header {
  position: relative;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-bottom: 20px;
}
.header h2 {
  font-size: 24px;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  margin: 0;
}
.table-responsive {
  width: 100%;
  overflow-x: auto;
}
table {
  width: 100%;
  min-width: 800px; 
  border-collapse: collapse;
}
th, td {
  border: 1px solid #ddd;
  padding: 12px 10px; 
  text-align: center;
}
th {
  background-color: #f2f2f2;
}
.empty-text {
  text-align: center;
  color: #888;
  padding: 20px;
}
.btn {
  padding: 8px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  color: #fff;
}
.btn-add { background-color: #28a745; }
.btn-cancel { background-color: #dc3545; }
.btn-info { background-color: #17a2b8; }
.btn:hover { opacity: 0.9; }

.modal-overlay {
  position: fixed;
  top: 0; left: 0; width: 100%; height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal-box {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  width: 90%; 
  max-width: 500px;
}
.modal-box h3 {
  font-size: 20px;
  margin-top: 0;
}
.form-row {
  display: flex;
  gap: 15px; 
}
.form-row .form-group {
  flex: 1; 
  margin-bottom: 15px;
}

.form-group { margin-bottom: 15px; }
.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}
.form-group input, .form-group textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}
.disabled-input {
  background-color: #f9f9f9;
  color: #555;
}
.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 20px;
}
</style>