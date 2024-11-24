<template>
    <Header />

    <div class="container mx-auto p-4 mb-16">
        <router-view></router-view>
    </div>

    <Tabs />
</template>

<script>
import { useAuthStore } from "@/stores/Auth.js";
import { useRestaurantSystemSettings } from "@/stores/RestaurantSystemSettings.js";
import Tabs from "./components/bottomTabs.vue";
import Header from "./components/Header.vue";

export default {
    name: "App",
    components: {
        Tabs,
        Header,
    },
    setup() {
        const auth = useAuthStore();
        const restaurantSystemSettingsStore = useRestaurantSystemSettings();
        
        return { auth, restaurantSystemSettingsStore };
    },
    async mounted() {
        this.auth.fetchUserDetails();
        await this.restaurantSystemSettingsStore.fetch_restaurant_system_settings();
    },
    computed: {
        isLoginPage() {
            return this.$route.path === "/";
        },
    },
};
</script>
