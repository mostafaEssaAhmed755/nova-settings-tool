<template>
    <div class="nova-settings-tool">
        <Head :title="title" />
        <template v-for="(keys, panel) in panels">
            <Heading class="mb-6 title">{{ __(panelName(panel)) }}</Heading>
            <Card class="mb-8">
                <Component
                    v-for="(setting, index) in keys"
                    :index="index"
                    :key="settings[setting].key"
                    :is="`${settings[setting].type}Setting`"
                    :setting="settings[setting]"
                    @update="updateSetting"
                    class="input-style"
                />
            </Card>
        </template>
        <div class="flex">
            <button
                class="save-button border inline-flex items-center justify-center appearance-none cursor-pointer rounded text-sm font-bold focus:outline-none focus:ring ring-primary-200 dark:ring-gray-600 relative disabled:cursor-not-allowed shadow h-9 px-3 bg-primary-500 border-primary-500 hover:enabled:bg-primary-400 hover:enabled:border-primary-400 text-white dark:text-gray-900 ml-auto"
                @click="saveSettings"
                :processing="saving"
            >
                {{ __('Save') }}
            </button>
        </div>
    </div>
</template>

<script>
import mapValues from 'lodash/mapValues';
import CodeSetting from './CodeSetting.vue';
import NumberSetting from './NumberSetting.vue';
import SelectSetting from './SelectSetting.vue';
import TextSetting from './TextSetting.vue';
import TextareaSetting from './TextareaSetting.vue';
import ToggleSetting from './ToggleSetting.vue';
import TimeSetting from './TimeSetting.vue';

export default {
    components: {
        CodeSetting,
        NumberSetting,
        SelectSetting,
        TextSetting,
        TextareaSetting,
        ToggleSetting,
        TimeSetting,
    },
    data: () => ({
        saving: false,
        title: '',
        settings: {},
        panels: {},
    }),
    mounted() {
        Nova.request()
            .get('/nova-vendor/nova-settings-tool')
            .then(({ data }) => {
                this.title = this.__(data.title);
                this.settings = data.settings;
                this.panels = data.panels;
            });
    },
    methods: {
        updateSetting(data) {
            this.settings[data.key].value = data.value;
        },
        saveSettings() {
            this.saving = true;
            let settings = mapValues(this.settings, 'value');
            Nova.request()
                .post('/nova-vendor/nova-settings-tool', settings)
                .then(() => Nova.success(this.__('Settings saved!')))
                .catch(() => Nova.error(this.__('Failed to save settings!')))
                .finally(() => (this.saving = false));
        },
        panelName(value) {
            return value === '_default' ? (Object.keys(this.panels).length > 1 ? 'Other' : 'Settings') : value;
        },
    },
};
</script>
