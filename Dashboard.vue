<script setup>
import { useLayout } from '@/layout/composables/layout';
import { StudentService } from '@/service/Enrollment';
import { onMounted, ref, watch } from 'vue';

const { getPrimary, getSurface, isDarkTheme } = useLayout();

const products = ref(null);
const chartData = ref(null);
const chartOptions = ref(null);

const items = ref([
    { 
        label: 'Add New', 
        icon: 'pi pi-fw pi-plus' 
    },
    { 
        label: 'Remove', 
        icon: 'pi pi-fw pi-trash' 
    }
]);

const students = ref([]);

onMounted(() => {
    students.value = StudentService.getStudentsData(); // Student Dummy Data
    chartData.value = setChartData(); //ChartData
    chartOptions.value = setChartOptions(); //Chart Options
});

// Chart
function setChartData() {
    const documentStyle = getComputedStyle(document.documentElement);

    return {
        labels: ['2021', '2022', '2023', '2024'],
        datasets: [
            {
                type: 'bar',
                label: 'Enrolled',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-400'),
                data: [342, 620, 490, 740],
                barThickness: 32
            },
            {
                type: 'bar',
                label: 'Transfer',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-300'),
                data: [11, 8, 2, 9],
                barThickness: 32
            },
            {
                type: 'bar',
                label: 'Dropout',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-200'),
                data: [10, 5, 13, 4],
                borderRadius: {
                    topLeft: 8,
                    topRight: 8
                },
                borderSkipped: true,
                barThickness: 32
            }
        ]
    };
}
//Chart Options
function setChartOptions() {
    const documentStyle = getComputedStyle(document.documentElement);
    const borderColor = documentStyle.getPropertyValue('--surface-border');
    const textMutedColor = documentStyle.getPropertyValue('--text-color-secondary');

    return {
        maintainAspectRatio: false,
        aspectRatio: 0.8,
        scales: {
            x: {
                stacked: true,
                ticks: {
                    color: textMutedColor
                },
                grid: {
                    color: 'transparent',
                    borderColor: 'transparent'
                }
            },
            y: {
                stacked: true,
                ticks: {
                    color: textMutedColor
                },
                grid: {
                    color: borderColor,
                    borderColor: 'transparent',
                    drawTicks: false
                }
            }
        }
    };
}

// Declare that the format is 12 to set it as the LRN
const formatLRN = (value) => {
    return String(value).padStart(12, '0'); 
};

// Update chart data and options when theme or colors change
watch([getPrimary, getSurface, isDarkTheme], () => {
    chartData.value = setChartData();
    chartOptions.value = setChartOptions();
});
</script>

<template>
    <div class="grid grid-cols-12 gap-8">
        <div class="col-span-12 lg:col-span-6 xl:col-span-3">
            <div class="card mb-0">
                <div class="flex justify-between mb-4">
                    <div>
                        <span class="block text-muted-color font-medium mb-4">Total Students</span>
                        <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">2,450</div>
                    </div>
                    <div class="flex items-center justify-center bg-blue-100 dark:bg-blue-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-users text-cyan-500 !text-xl"></i>
                    </div>
                </div>
                <span class="text-primary font-medium">300 new </span>
                <span class="text-muted-color">since last semester</span>
            </div>
        </div>
        <div class="col-span-12 lg:col-span-6 xl:col-span-3">
            <div class="card mb-0">
                <div class="flex justify-between mb-4">
                    <div>
                        <span class="block text-muted-color font-medium mb-4">Average Attendance Rate</span>
                        <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">94%</div>
                    </div>
                    <div class="flex items-center justify-center bg-orange-100 dark:bg-orange-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-dollar text-orange-500 !text-xl"></i>
                    </div>
                </div>
                <span class="text-primary font-medium mr-1">+3%</span>
                <span class="text-muted-color">since last month</span>
            </div>
        </div>
        <div class="col-span-12 lg:col-span-6 xl:col-span-3">
            <div class="card mb-0">
                <div class="flex justify-between mb-4">
                    <div>
                        <span class="block text-muted-color font-medium mb-4">Recent Enrollments</span>
                        <div class="text-surface-900 dark:text-surface-0 font-medium text-xl mr-1">300</div>
                    </div>
                    <div class="flex items-center justify-center bg-cyan-100 dark:bg-cyan-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-users text-cyan-500 !text-xl"></i>
                    </div>
                </div>
                <span class="text-primary font-medium mr-1">80</span>
                <span class="text-muted-color">this week</span>
            </div>
        </div>
        <div class="col-span-12 lg:col-span-6 xl:col-span-3">
            <div class="card mb-0">
                <div class="flex justify-between mb-4">
                    <div>
                        <span class="block text-muted-color font-medium mb-4">Unread Messages</span>
                        <div class="text-surface-900 dark:text-surface-0 font-medium text-xl">152 Unread Messages</div>
                    </div>
                    <div class="flex items-center justify-center bg-purple-100 dark:bg-purple-400/10 rounded-border" style="width: 2.5rem; height: 2.5rem">
                        <i class="pi pi-comment text-purple-500 !text-xl"></i>
                    </div>
                </div>
                <span class="text-primary font-medium mr-1">85</span>
                <span class="text-muted-color">responded</span>
            </div>
        </div>
        <!-- Recenet Enrollments -->
            <div class="col-span-12 xl:col-span-6">
                <div class="card">
                    <div class="font-semibold text-xl mb-4">Recent Enrollments</div>
                    <DataTable :value="students" :rows="5" :paginator="true" responsiveLayout="scroll">
                        <Column field="profilePicture" header="Profile" style="width: 15%">
                            <template #body="slotProps">
                                <img :src="slotProps.data.profilePicture" :alt="slotProps.data.name" width="50" class="shadow rounded-full" />
                            </template>
                        </Column>
                        <Column field="name" header="Name" :sortable="true" style="width: 25%"></Column>
                        <Column field="lrn" header="LRN" :sortable="true" style="width: 25%">
                            <template #body="slotProps">
                                {{ formatLRN(slotProps.data.lrn) }}
                            </template>
                        </Column>
                        <Column field="enrollmentDate" header="Enrollment Date" :sortable="true" style="width: 25%">
                            <template #body="slotProps">
                                {{ new Date(slotProps.data.enrollmentDate).toLocaleDateString() }}
                            </template>
                        </Column>
                </DataTable>
          </div>
        <!-- ZSNHS Tracks -->
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <div class="font-semibold text-xl">ZSNHS Senior High School Tracks Enrolled</div>
                <div>
                    <Button icon="pi pi-ellipsis-v" class="p-button-text p-button-plain p-button-rounded" @click="$refs.menu2.toggle($event)"></Button>
                    <Menu ref="menu2" :popup="true" :model="items" class="!min-w-40"></Menu>
                </div>
            </div>
            <ul class="list-none p-0 m-0">
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Technical-Vocational-Livelihood (TVL)</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-orange-500 h-full" style="width: 50%"></div>
                        </div>
                        <span class="text-orange-500 ml-4 font-medium">50 Enrolled</span>
                    </div>
                </li>
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Science, Technology, Engineering, and Mathematics (STEM)</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-cyan-500 h-full" style="width: 16%"></div>
                        </div>
                        <span class="text-cyan-500 ml-4 font-medium">16 Enrolled</span>
                    </div>
                </li>
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Humanities and Social Sciences (HUMMS)</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-pink-500 h-full" style="width: 67%"></div>
                        </div>
                        <span class="text-pink-500 ml-4 font-medium">67 Enrolled</span>
                    </div>
                </li>
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Accountancy, Business, and Management (ABM)</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-green-500 h-full" style="width: 35%"></div>
                        </div>
                        <span class="text-primary ml-4 font-medium">35 Enrolled</span>
                    </div>
                </li>
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Sports</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-purple-500 h-full" style="width: 75%"></div>
                        </div>
                        <span class="text-purple-500 ml-4 font-medium">75 Enrolled</span>
                    </div>
                </li>
                <li class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                    <div>
                        <span class="text-surface-900 dark:text-surface-0 font-medium mr-2 mb-1 md:mb-0">Arts and Design</span>
                    </div>
                    <div class="mt-2 md:mt-0 flex items-center">
                        <div class="bg-surface-300 dark:bg-surface-500 rounded-border overflow-hidden w-40 lg:w-24" style="height: 8px">
                            <div class="bg-teal-500 h-full" style="width: 40%"></div>
                        </div>
                        <span class="text-teal-500 ml-4 font-medium">40 Enrolled</span>
                    </div>
                </li>
            </ul>
        </div>
    </div>
        <div class="col-span-12 xl:col-span-6">
        <!-- Overview Chart -->
            <div class="card">
                <div class="font-semibold text-xl mb-4">Overview</div>
                <Chart type="bar" :data="chartData" :options="chartOptions" class="h-80" />
            </div>
            <!-- Recent Activities -->
        <div class="card">
            <div class="flex items-center justify-between mb-6">
                <div class="font-semibold text-xl">Notifications</div>
                <div>
                    <Button icon="pi pi-ellipsis-v" class="p-button-text p-button-plain p-button-rounded" @click="$refs.menu1.toggle($event)"></Button>
                    <Menu ref="menu1" :popup="true" :model="items" class="!min-w-40"></Menu>
                </div>
            </div>
            <!-- Today -->
            <span class="block text-muted-color font-medium mb-4">TODAY</span>
            <ul class="p-0 mx-0 mt-0 mb-6 list-none">
                <li class="flex items-center py-2 border-b border-surface">
                    <div class="w-12 h-12 flex items-center justify-center bg-blue-100 dark:bg-blue-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-user !text-xl text-blue-500"></i>
                    </div>
                    <span class="text-surface-900 dark:text-surface-0 leading-normal">
                        Mr. Santos
                        <span class="text-surface-700 dark:text-surface-100">has posted new assignments for <span class="text-primary font-bold hover:underline cursor-pointer">Statistics and Probability</span></span>
                    </span>
                </li>
                <li class="flex items-center py-2">
                    <div class="w-12 h-12 flex items-center justify-center bg-orange-100 dark:bg-orange-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-bell !text-xl text-orange-500"></i>
                    </div>
                    <span class="text-surface-700 dark:text-surface-100 leading-normal">The deadline for the <span class="text-primary font-bold hover:underline cursor-pointer">Art Project</span> has been extended to <span class="text-primary font-bold hover:underline cursor-pointer">Friday</span>.</span>
                </li>
            </ul>
            <!-- Yesterday -->
            <span class="block text-muted-color font-medium mb-4">YESTERDAY</span>
            <ul class="p-0 m-0 list-none mb-6">
                <li class="flex items-center py-2 border-b border-surface">
                    <div class="w-12 h-12 flex items-center justify-center bg-green-100 dark:bg-green-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-check !text-xl text-green-500"></i>
                    </div>
                    <span class="text-surface-900 dark:text-surface-0 leading-normal">
                        Mr. Santos request for the <span class="text-primary font-bold hover:underline cursor-pointer">Field Trip</span> has been approved.
                    </span>
                </li>
                <li class="flex items-center py-2 border-b border-surface">
                    <div class="w-12 h-12 flex items-center justify-center bg-pink-100 dark:bg-pink-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-info-circle !text-xl text-pink-500"></i>
                    </div>
                    <span class="text-surface-900 dark:text-surface-0 leading-normal">
                        Reminder: Parent-Teacher Meeting on <span class="text-primary font-bold hover:underline cursor-pointer">Wednesday</span> at <span class="text-primary font-bold hover:underline cursor-pointer">6 PM</span>.
                    </span>
                </li>
            </ul>
            <!-- Last Week -->
            <span class="block text-muted-color font-medium mb-4">LAST WEEK</span>
            <ul class="p-0 m-0 list-none">
                <li class="flex items-center py-2 border-b border-surface">
                    <div class="w-12 h-12 flex items-center justify-center bg-purple-100 dark:bg-purple-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-star !text-xl text-purple-500"></i>
                    </div>
                    <span class="text-surface-900 dark:text-surface-0 leading-normal">
                        <span class="text-primary font-bold hover:underline cursor-pointer">10</span> students have received awards for academic excellence.
                    </span>
                </li>
                <li class="flex items-center py-2 border-b border-surface">
                    <div class="w-12 h-12 flex items-center justify-center bg-yellow-100 dark:bg-yellow-400/10 rounded-full mr-4 shrink-0">
                        <i class="pi pi-chart-bar !text-xl text-yellow-500"></i>
                    </div>
                    <span class="text-surface-900 dark:text-surface-0 leading-normal">
                        The results for the <span class="text-primary font-bold hover:underline cursor-pointer">Science Fair</span> have been announced. Check your email for details!
                    </span>
                </li>
            </ul>
        </div>
    </div>
</div>
</template>
