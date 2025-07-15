<template>
    <div class="container mt-5">
      <div class="row">
        <div class="col-sm-8 offset-sm-2 col-md-8 offset-md-2">
          <h1 class="text-center">User Information Form</h1>
          <form @submit.prevent="submitForm">
            <div class="row mb-3">
                <div class="col-sm-6 col-md-6">
                <label for="username" class="form-label">Username</label>
                <input
                  type="text"
                  class="form-control"
                  id="username"
                  @blur="()=>validateName(true)"
                  @input="()=>validateName(false)"
                  v-model="formData.username"
                >
                <div v-if="errors.username" class="text-danger">
                  {{ errors.username }}
                </div>
              </div>
              <div class="col-sm-6 col-md-6">
                <label for="password" class="form-label">Password</label>
                <input
                  type="password"
                  class="form-control"
                  id="password"
                  @blur="()=>validatePassword(true)"
                  @input="()=>validatePassword(false)"
                  v-model="formData.password"
                >
                <div v-if="errors.password" class="text-danger">
                  {{ errors.password }}
                </div>
              </div>
            </div>

            <div class="mb-3">
              <label for="email" class="form-label">Email</label>
              <input
                type="email"
                class="form-control"
                id="email"
                @blur="()=>validateEmail(true)"
                @input="()=>validateEmail(false)"
                v-model="formData.email"
              >
              <div v-if="errors.email" class="text-danger">
                {{ errors.email }}
              </div>
            </div>
  
            <div class="row mb-3">
                <div class="col-sm-6 col-md-6">
                <div class="form-check">
                  <input
                    type="checkbox"
                    class="form-check-input"
                    id="isAustralian"
                    @change="()=>validateResident(true)"
                    v-model="formData.isAustralian"
                  >
                  <label class="form-check-label" for="isAustralian">
                    Australian Resident?
                  </label>
                </div>
                <div v-if="errors.resident" class="text-danger">
                  {{ errors.resident }}
                </div>
              </div>
              <div class="col-sm-6 col-md-6">
                <label for="gender" class="form-label">Gender</label>
                <select
                  class="form-select"
                  id="gender"
                  @blur="()=>validateGender(true)"
                  @change="()=>validateGender(false)"
                  v-model="formData.gender"
                >
                  <option value="">Please select</option>
                  <option value="male">Male</option>
                  <option value="female">Female</option>
                  <option value="other">Other</option>
                </select>
                <div v-if="errors.gender" class="text-danger">
                  {{ errors.gender }}
                </div>
              </div>
            </div>
  
            <div class="mb-3">
              <label for="reason" class="form-label">Reason for joining</label>
              <textarea
                class="form-control"
                id="reason"
                rows="3"
                @blur="()=>validateReason(true)"
                @input="()=>validateReason(false)"
                v-model="formData.reason"
              ></textarea>
              <div v-if="errors.reason" class="text-danger">
                {{ errors.reason }}
              </div>
            </div>
  
            <div class="text-center">
              <button type="submit" class="btn btn-primary me-2">Submit</button>
              <button
                type="button"
                class="btn btn-secondary"
                @click="clearForm"
              >
                Clear
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
<div class="row mt-5" v-if="submittedCards.length">
  <div class="col-12">
    <h3 class="text-center mb-4">Submitted User Information</h3>
    <DataTable :value="submittedCards" tableStyle="min-width: 50rem" class="p-datatable-striped">
      <Column field="username" header="Username"></Column>
      <Column field="password" header="Password"></Column>
      <Column field="email" header="Email"></Column>
      <Column field="isAustralian" header="Australian Resident">
        <template #body="slotProps">
          {{ slotProps.data.isAustralian ? 'Yes' : 'No' }}
        </template>
      </Column>
      <Column field="gender" header="Gender"></Column>
      <Column field="reason" header="Reason"></Column>
    </DataTable>
  </div>
</div>
  </template>
  

<script setup>
// Our logic will go here
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
  
const formData = ref({
    username: '',
    password: '',
    email: '',
    isAustralian: false,
    reason: '',
    gender: ''
});

const submittedCards = ref([]);

const submitForm = () => {
  validateName(true);
  validatePassword(true);
  validateEmail(true);
  validateResident(true);
  validateGender(true);
  validateReason(true);
  if (!errors.value.username && !errors.value.password && !errors.value.email && !errors.value.resident && !errors.value.gender && !errors.value.reason){
    submittedCards.value.push({
      ...formData.value
    });
    clearForm();
  }
};

const clearForm = () => {
  formData.value.username     = '';
  formData.value.password     = '';
  formData.value.email        = '';
  formData.value.isAustralian = false;
  formData.value.reason       = '';
  formData.value.gender       = '';

};

const errors = ref(
  {
    username: null,
    password: null,
    email: null,
    resident: null,
    reason: null,
    gender: null
  }
)

const validateName = (blur) => {
  if (formData.value.username.length < 3){
    if (blur) errors.value.username = "Name must be at least 3 characters";

  }else{
    errors.value.username = null;
  }
}

const validatePassword = (blur) => {
  const password = formData.value.password;
  const minLength = 8;
  const hasUppercase = /[A-Z]/.test(password);
  const hasLowercase = /[a-z]/.test(password);
  const hasNumber = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);

  if (password.length < minLength) {
    if (blur) errors.value.password = `Password must be at least ${minLength} characters long.`;
  } else if (!hasUppercase) {
    if (blur) errors.value.password = "Password must contain at least one uppercase letter.";
  } else if (!hasLowercase) {
    if (blur) errors.value.password = "Password must contain at least one lowercase letter.";
  } else if (!hasNumber) {
    if (blur) errors.value.password = "Password must contain at least one number.";
  } else if (!hasSpecialChar) {
    if (blur) errors.value.password = "Password must contain at least one special character.";
  } else {
    errors.value.password = null;
  }
};

const validateResident = (blur) => {
  if (!formData.value.isAustralian) {
    if (blur) errors.value.resident = "You must be an Australian resident to proceed.";
  } else {
    errors.value.resident = null;
  }
};

const validateGender = (blur) => {
  if (!formData.value.gender) {
    if (blur) errors.value.gender = "Please select a gender.";
  } else {
    errors.value.gender = null;
  }
};

const validateEmail = (blur) => {
  const email = formData.value.email;
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  
  if (!email) {
    if (blur) errors.value.email = "Email is required.";
  } else if (!emailRegex.test(email)) {
    if (blur) errors.value.email = "Please enter a valid email address.";
  } else {
    errors.value.email = null;
  }
};

const validateReason = (blur) => {
  if (formData.value.reason.length < 10) {
    if (blur) errors.value.reason = "Reason must be at least 10 characters long.";
  } else {
    errors.value.reason = null;
  }
};
</script>

<style scoped>
   .card {
   border: 1px solid #ccc;
   border-radius: 10px;
   box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   .card-header {
   background-color: #275FDA;
   color: white;
   padding: 10px;
   border-radius: 10px 10px 0 0;
   }
   .list-group-item {
   padding: 10px;
   }

   :deep(.p-datatable) {
     background-color: white !important;
     color: #000 !important;
   }

   :deep(.p-datatable .p-datatable-header) {
     background-color: #f8f9fa !important;
     color: #000 !important;
     border: 1px solid #dee2e6 !important;
   }

   :deep(.p-datatable .p-datatable-thead > tr > th) {
     background-color: #e9ecef !important;
     color: #000 !important;
     border: 1px solid #dee2e6 !important;
     font-weight: 600;
   }

   :deep(.p-datatable .p-datatable-tbody > tr) {
     background-color: white !important;
     color: #000 !important;
   }

   :deep(.p-datatable .p-datatable-tbody > tr:nth-child(even)) {
     background-color: #f8f9fa !important;
   }

   :deep(.p-datatable .p-datatable-tbody > tr > td) {
     border: 1px solid #dee2e6 !important;
     color: #000 !important;
   }

   :deep(.p-datatable .p-datatable-tbody > tr:hover) {
     background-color: #e9ecef !important;
   }
</style>