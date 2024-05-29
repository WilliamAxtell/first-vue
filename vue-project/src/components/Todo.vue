<script setup>

import {ref, onMounted} from 'vue';

const loading = ref(true);
let staffList = ref();
let holidayStaff = ref();
let wfhStaff = ref();

const vCardColour = {
    mounted: (el, binding) => {
        switch (binding.value) {
            case 'Operations':
            case 'Director':
            case 'Sales':
            case 'Field Sales':
                el.style.backgroundColor = '#009CA6';
                break;
            case 'Paid Media':
                el.style.backgroundColor = '#433696';
                break;
            case 'SEO':
                el.style.backgroundColor = '#913fd9';
                break;
            case 'Data & Engineering':
            case 'Design':
            case 'Technical':
                el.style.backgroundColor = '#e05cb7';
                break;                                                 
        }
    }
};

onMounted(async () => {
    staffList.value = await fetchStaff();
    console.log(staffList.value);
    staffList.value = staffList.value
    .filter(a => a.holiday || a.wfh === true)
    .map((a) => {
        if (a.preferredName) {
            return {
                ...a,
                firstName: a.preferredName,
            };
        } else {
            return {
                ...a
            };
        }
    })
    .sort((a, b) => {
        if (a.firstName < b.firstName) {
            return -1;
        }

        if (a.firstName == b.firstName) {
            if (a.lastName < b.lastName) {
                return -1;
            }
        }
    });
    holidayStaff.value = staffList.value.filter(a => a.holiday === true);
    wfhStaff.value = staffList.value.filter(a => a.wfh === true);
    loading.value = false;
});


const fetchStaff = async () => {
    try {
        const token = import.meta.env.VITE_APP_BAMBOONINE_CLIENT_TOKEN;
        return await fetch('https://europe-west2-bamboo-nine-internal-tools.cloudfunctions.net/staff', {
        headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`
        },
        method: 'POST'
        }).then((response) => response.json());
    } catch (err) {
        console.log(err);
    }
};

</script>

<template>
    <slot name="holiday"></slot>
    <div v-if="loading" class="loading">Loading</div>
    <div v-else class="grid">
        <div class="card" v-card-colour="staff.deparment" v-for="staff in holidayStaff" :key="staff.id">
        <div class="card-content">  
            <h2 class="card-title">{{ staff.firstName }} {{ staff.lastName }}</h2>
            <p class="card-subtitle">{{ staff.jobTitle }}</p>
        </div>
        </div>
    </div>
    <slot name="home"></slot>
    <div v-if="loading" class="loading">Loading</div>
    <div v-else class="grid">
        <div class="card" v-card-colour="staff.deparment" v-for="staff in wfhStaff" :key="staff.id">
        <div class="card-content">  
            <h2 class="card-title">{{ staff.firstName }} {{ staff.lastName }}</h2>
            <p class="card-subtitle">{{ staff.jobTitle }}</p>
        </div>
        </div>
    </div>    
</template>

<style scoped>
    .grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
        gap: 1rem;
        margin: 1rem;
    }
    .loading {
        font-size: 24px;
        margin: 1rem;
    }
    .card {
        border: 0.2rem solid black;
        padding: 1rem;
        color: white;
        border-radius: 1rem;
        padding: 0;
    }

    .card * {
        margin-block-start: 0;
        margin-block-end: 0;
    }

    .card-content {
        margin-block-start: 0;
        margin-block-end: 0;
        margin: 1rem;
    }

    .card-title {
        font-size: 24px;
    }

    .card-subtitle {
        font-size: 18px;
        font-style: italic;
    }
    
</style>
