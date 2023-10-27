<template>
    <DefaultSetting :setting="setting" :errors="errors || []">
        <VueTimepicker
        v-model="selectedTime"
        :id="setting.key"
        :format="setting.format"
        :placeholder="setting.placeholder"
        ></VueTimepicker>
    </DefaultSetting>
</template>

<script>
import DefaultSetting from './DefaultSetting.vue';
import VueTimepicker from 'vue3-timepicker'

export default {
    components: {
        DefaultSetting,
        VueTimepicker
    },
    props: {
        setting: Object,
        errors: Array,
    },
    emits: ['update'],
    data() {
        return {
        selectedTime: this.setting.value, // Initialize with setting.value
        };
    },
    watch: {
        selectedTime(newTime) {
            this.updateTime(newTime);
        },
    },
    methods: {
        updateTime(value) {
            this.$emit('update', {
                key: this.setting.key,
                value,
            });
        },
    },
};
</script>
<style>
/* Import the CSS styles for vue3-datetime-picker */
@import '~vue3-timepicker/dist/VueTimepicker.css';
/* Your custom styles or other CSS imports can go here */
</style>