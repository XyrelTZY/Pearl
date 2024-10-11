<script setup>
import { FilterMatchMode } from '@primevue/core/api';
import { useToast } from 'primevue/usetoast';
import { onMounted, ref } from 'vue';
import { StudentService } from '@/service/Student.js'

onMounted(() => {
    StudentService.getStudents().then((data) => (students.value = data));
});

const toast = useToast();
const dt = ref();
const students = ref();
const studentDialog = ref(false);
const deleteStudentDialog = ref(false);
const deleteStudentsDialog = ref(false);
const student = ref({});
const selectedStudents = ref();
const filters = ref({
    global: { value: null, matchMode: FilterMatchMode.CONTAINS }
});
const submitted = ref(false);

function formatAge(value) {
    return value ? value : 'N/A';
}

function openNew() {
    student.value = {};
    submitted.value = false;
    studentDialog.value = true;
}

function hideDialog() {
    studentDialog.value = false;
    submitted.value = false;
}

function saveStudent() {
    submitted.value = true;

    if (student?.value.name?.trim()) {
        if (student.value.id) {
            students.value[findIndexById(student.value.id)] = student.value;
            toast.add({ severity: 'success', summary: 'Successful', detail: 'Student Updated', life: 3000 });
        } else {
            student.value.id = createId();
            students.value.push(student.value);
            toast.add({ severity: 'success', summary: 'Successful', detail: 'Student Created', life: 3000 });
        }

        studentDialog.value = false;
        student.value = {};
    }
}

function editStudent(std) {
    student.value = { ...std };
    studentDialog.value = true;
}

function confirmDeleteStudent(std) {
    student.value = std;
    deleteStudentDialog.value = true;
}

function deleteStudent() {
    students.value = students.value.filter((val) => val.id !== student.value.id);
    deleteStudentDialog.value = false;
    student.value = {};
    toast.add({ severity: 'success', summary: 'Successful', detail: 'Student Deleted', life: 3000 });
}

function findIndexById(id) {
    return students.value.findIndex(student => student.id === id);
}

function createId() {
    let id = '';
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    for (let i = 0; i < 5; i++) {
        id += chars.charAt(Math.floor(Math.random() * chars.length));
    }
    return id;
}

function confirmDeleteSelected() {
    deleteStudentsDialog.value = true;
}

function deleteSelectedStudents() {
    students.value = students.value.filter((val) => !selectedStudents.value.includes(val));
    deleteStudentsDialog.value = false;
    selectedStudents.value = null;
    toast.add({ severity: 'success', summary: 'Successful', detail: 'Students Deleted', life: 3000 });
}
</script>

<template>
    <div>
        <div class="card">
            <Toolbar class="mb-6">
                <template #start>
                    <Button label="New" icon="pi pi-plus" severity="secondary" class="mr-2" @click="openNew" />
                    <Button label="Delete" icon="pi pi-trash" severity="secondary" @click="confirmDeleteSelected" :disabled="!selectedStudents || !selectedStudents.length" />
                </template>
            </Toolbar>

            <DataTable
                ref="dt"
                v-model:selection="selectedStudents"
                :value="students"
                dataKey="id"
                :paginator="true"
                :rows="10"
                :filters="filters"
                paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                :rowsPerPageOptions="[5, 10, 25]"
                currentPageReportTemplate="Showing {first} to {last} of {totalRecords} students"
            >
            <template #empty> No Student Found. </template>
            <template #loading> Loading Student data. Please wait. </template>
                <template #header>
                    <div class="flex flex-wrap gap-2 items-center justify-between">
                        <h4 class="m-0">Manage Students</h4>
                        <InputText v-model="filters['global'].value" placeholder="Search..." />
                    </div>
                </template>
                <Column selectionMode="multiple" style="width: 3rem" :exportable="false"></Column>
                <Column field="name" header="Name" sortable style="min-width: 16rem"></Column>
                <Column field="age" header="Age" sortable style="min-width: 8rem">
                    <template #body="slotProps">
                        {{ formatAge(slotProps.data.age) }}
                    </template>
                </Column>
                <Column field="grade" header="Grade" sortable style="min-width: 10rem"></Column>
                <!-- Status -->
                <Column field="Status" header="Status" dataType="boolean" bodyClass="text-center" style="min-width: 8rem">
                <template #body="{ data }">
                    <i class="pi" :class="{ 'pi-check-circle text-green-500 ': data.status, 'pi-times-circle text-red-500': !data.status }"></i>
                </template>
            </Column>
                <Column :exportable="false" style="min-width: 12rem">
                    <template #body="slotProps">
                        <Button icon="pi pi-pencil" outlined rounded class="mr-2" @click="editStudent(slotProps.data)" />
                        <Button icon="pi pi-trash" outlined rounded severity="danger" @click="confirmDeleteStudent(slotProps.data)" />
                    </template>
                </Column>
            </DataTable>
        </div>

        <Dialog v-model:visible="studentDialog" :style="{ width: '450px' }" header="Student Details" :modal="true">
            <div class="flex flex-col gap-6">
                <div>
                    <label for="name" class="block font-bold mb-3">Name</label>
                    <InputText id="name" v-model.trim="student.name" required="true" autofocus :invalid="submitted && !student.name" fluid />
                    <small v-if="submitted && !student.name" class="text-red-500">Name is required.</small>
                </div>
                <div>
                    <label for="age" class="block font-bold mb-3">Age</label>
                    <InputNumber id="age" v-model="student.age" integeronly fluid />
                </div>
                <div>
                    <label for="grade" class="block font-bold mb-3">Grade</label>
                    <InputText id="grade" v-model="student.grade" required="true" fluid />
                </div>
            <div>
            <label for="status" class="block font-bold mb-3">Status</label>
                <Select id="status" :options="statuses" optionLabel="label" placeholder="Select a Status" fluid>
                    <option value="{{ true }}" >Active</option>
                    <option value=""></option>
                </Select>
                </div>
            </div>
            <template #footer>
                <Button label="Cancel" icon="pi pi-times" text @click="hideDialog" />
                <Button label="Save" icon="pi pi-check" @click="saveStudent" />
            </template>
        </Dialog>

        <Dialog v-model:visible="deleteStudentDialog" :style="{ width: '450px' }" header="Confirm" :modal="true">
            <div class="flex items-center gap-4">
                <i class="pi pi-exclamation-triangle !text-3xl" />
                <span v-if="student">Are you sure you want to delete <b>{{ student.name }}</b>?</span>
            </div>
            <template #footer>
                <Button label="No" icon="pi pi-times" text @click="deleteStudentDialog = false" />
                <Button label="Yes" icon="pi pi-check" @click="deleteStudent" />
            </template>
        </Dialog>

        <Dialog v-model:visible="deleteStudentsDialog" :style="{ width: '450px' }" header="Confirm" :modal="true">
            <div class="flex items-center gap-4">
                <i class="pi pi-exclamation-triangle !text-3xl" />
                <span>Are you sure you want to delete the selected students?</span>
            </div>
            <template #footer>
                <Button label="No" icon="pi pi-times" text @click="deleteStudentsDialog = false" />
                <Button label="Yes" icon="pi pi-check" text @click="deleteSelectedStudents" />
            </template>
        </Dialog>
    </div>
</template>
