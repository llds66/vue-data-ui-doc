<script setup>
import { ref, onMounted, computed } from "vue";
import { Menu2Icon, XIcon } from "vue-tabler-icons";
import { useRouter } from "vue-router";
import { SunFilledIcon, MoonStarsIcon, LanguageIcon } from "vue-tabler-icons";
import { useMainStore } from "../stores";
import ChartMaker from "./ChartMaker.vue";
import releases from "../../public/releases.json";
import HeaderDropdownItem from "./HeaderDropdownItem.vue";

const router = useRouter();
const store = useMainStore();

const isOpen = ref(false);
const translations = computed(() => {
    return store.translations;
});

function useMenu() {
    isOpen.value = !isOpen.value;
}

const lastVersion = computed(() => {
    console.log(`
    
\\ \\      /\\
 \\ \\    /  \\
  \\ \\  / /\\ \\
   \\ \\/ /
    \\  /
     \\/ ${releases[0].version}
     

Vue Data UI is an open source library.
Feel free to make it better with us ^^
     `);
    return releases[0].version.replace("v", "").split("").slice(1);
});

const digitsConfigVersion = computed(() => {
    return {
        backgroundColor: "transparent",
        digits: {
            color: isDarkMode.value ? "#42d392" : "#1A1A1A",
            skeletonColor: isDarkMode.value ? "#FFFFFF10" : "#1A1A1A16",
        },
    };
});

const chartMkr = ref(null);

function openChartMaker() {
    chartMkr.value.openDialog();
}

function changeTheme() {
    if (localStorage.theme === "dark") {
        localStorage.theme = "light";
        document.documentElement.classList.remove("dark");
        store.isDarkMode = false;
    } else {
        localStorage.theme = "dark";
        document.documentElement.classList.add("dark");
        store.isDarkMode = true;
    }
}

const currentRoute = computed(() => {
    return router.currentRoute.value.path;
});

function isSelected(route) {
    return currentRoute.value === route;
}

onMounted(() => {
    if (localStorage.vueDataUiLang) {
        store.lang = localStorage.vueDataUiLang;
    } else {
        store.lang = "en";
    }
    document.addEventListener("click", (e) => {
        const target = e.target;
        const isMenu = target.closest("#mainDropdown");
        const isButton = target.closest("#mainDropdownButton");
        if (isButton) return;
        if (isOpen.value === true && isMenu === null) {
            isOpen.value = false;
        }
    });
    if (localStorage.theme) {
        store.isDarkMode = localStorage.theme === "dark";
    } else {
        if (window.matchMedia("(prefers-color-scheme: dark)").matches) {
            document.documentElement.classList.add("dark");
            store.isDarkMode = true;
            localStorage.setItem("theme", "dark");
        } else {
            store.isDarkMode = false;
            localStorage.setItem("theme", "light");
        }
    }
});

const isDarkMode = computed(() => store.isDarkMode);

const languageOptions = ref([
    { value: "en", text: "English" },
    { value: "fr", text: "Français" },
    { value: "de", text: "Deutsch" },
    { value: "es", text: "Español" },
    { value: "pt", text: "Portugues" },
    { value: "zh", text: "中文" },
    { value: "jp", text: "日本語" },
    { value: "ko", text: "한국인" },
    { value: "ar", text: "عربي"}
]);

const selectedLanguage = computed({
    get() {
        return store.lang;
    },
    set(val) {
        localStorage.setItem("vueDataUiLang", val);
        store.lang = val;
        return val;
    },
});

const dropdownItems = computed(() => {
    return [
        { link: '/installation', title: translations.value.menu.installation[store.lang] },
        { link: '/docs', title: translations.value.menu.docs[store.lang] },
        { link: '/chart-builder', title: translations.value.menu.chartBuilder[store.lang] },
        { link: '/examples', title: translations.value.menu.examples[store.lang] },
        { link: '/customization', title: translations.value.menu.customization[store.lang] },
        { link: '/versions', title: translations.value.menu.versions[store.lang] },
        { link: '/about', title: translations.value.menu.about[store.lang] },
    ]
})

const isHome = computed(() => {
    return currentRoute.value === '/'
})

</script>

<template>
    <ChartMaker ref="chartMkr" />
    <header data-cy="app-header"
        class="z-[2147483647] sticky top-0 w-full font-satoshi bg-gray-200 dark:bg-black text-gray-800 dark:text-slate-300 border-b dark:border-[#2A2A2A]">
        <div class="mx-auto w-5/6 py-3 flex justify-between place-items-center">
            <router-link data-cy="link-home" to="/" v-if="!isHome">
                <div class="flex flex-row gap-3">
                    <div data-cy="header-app-name" class="flex flex-row gap-3 w-full whitespace-nowrap">
                        <img data-cy="header-logo" src="../assets/logo.png" class="h-5" />
                        <span>Vue Data UI</span>
                    </div>
                    <div class="h-[20px] flex flex-row align-center sm:ml-4">
                        <svg style="height:100%" viewBox="-6 -6 22 22">
                            <path d="M 0,4.5 5,12.5 10,4.5" :stroke="isDarkMode ? '#42d392' : '#1A1A1A'" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
                        </svg>
                        <VueUiDigits v-for="d in lastVersion" :config="digitsConfigVersion"
                            :dataset="d === '.' ? '.' : +d" :class="d === '.' ? '-mr-[0.5rem]' : ''" />
                    </div>
                </div>
            </router-link>
            <div v-else/>

            <nav class="hidden xl:flex flex-row gap-1 justify-end w-full place-items-center">
                <!-- <button @click="openChartMaker">
                    <ToolIcon/>
                </button> -->

                <router-link data-cy="link-installation" to="/installation">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/installation')
                                ? 'text-[#277753] dark:text-app-green hover:cursor-default bg-[#42d39233] shadow-md'
                                : 'text-gray-800 dark:text-app-green dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.installation[store.lang] }}
                    </span>
                </router-link>
                <router-link data-cy="link-docs" to="/docs">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/docs')
                                ? 'text-app-blue hover:cursor-default bg-[#5f8bee33] shadow-md'
                                : 'text-gray-800 dark:text-app-blue dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.docs[store.lang] }}
                    </span>
                </router-link>
                <router-link data-cy="link-docs" to="/chart-builder">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/chart-builder')
                                ? 'text-black dark:text-[#ffe596] hover:cursor-default bg-[#FFFFFF33] shadow-md'
                                : 'text-gray-800 dark:text-[#ffe596] dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.chartBuilder[store.lang] }}
                    </span>
                </router-link>
                <router-link to="/examples">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/examples')
                                ? 'text-app-blue hover:cursor-default bg-[#5f8bee33] shadow-sm'
                                : 'text-gray-800 dark:text-gray-400 dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.examples[store.lang] }}
                    </span>
                </router-link>
                <router-link data-cy="link-customization" to="/customization">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/customization')
                                ? 'text-app-blue hover:cursor-default bg-[#5f8bee33] shadow-sm'
                                : 'text-gray-800 dark:text-gray-400 dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.customization[store.lang] }}
                    </span>
                </router-link>
                <router-link data-cy="link-versions" to="/versions">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/versions')
                                ? 'text-app-blue hover:cursor-default bg-[#5f8bee33] shadow-sm'
                                : 'text-gray-800 dark:text-gray-400 dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.versions[store.lang] }}
                    </span>
                </router-link>
                <router-link data-cy="link-about" to="/about">
                    <span :class="`py-1 px-2 rounded-xl ${isSelected('/about')
                                ? 'text-app-blue hover:cursor-default bg-[#5f8bee33] shadow-sm'
                                : 'text-gray-800 dark:text-gray-400 dark:hover:bg-[#FFFFFF10] hover:bg-gray-300'
                            }`">
                        {{ translations.menu.about[store.lang] }}
                    </span>
                </router-link>
                <button data-cy="btn-mode" @click="changeTheme" id="themeToggle"
                    class="dark:hover:bg-[#fdd66320] hover:bg-[#5f8bee30] hover:text-[#5f8bee] rounded-full p-1">
                    <SunFilledIcon v-if="isDarkMode" class="text-[#fdd663]" />
                    <MoonStarsIcon v-else />
                </button>
                <div class="z-10 flex flex-row place-items-center gap-2">
                    <LanguageIcon />
                    <select data-cy="header-select" v-model="selectedLanguage" class="h-[24px] px-2">
                        <option v-for="option in languageOptions" :value="option.value">
                            {{ option.text }}
                        </option>
                    </select>
                </div>
            </nav>
            <div class="relative xl:hidden">
                <button id="mainDropdownButton" v-if="isOpen" @click="useMenu" type="button">
                    <XIcon />
                </button>
                <button id="mainDropdownButton" v-else @click="useMenu" type="button">
                    <Menu2Icon />
                </button>
                <div id="mainDropdown"
                    class="absolute top-full mt-2 right-0 bg-white dark:bg-black rounded-lg border border-gray-700 shadow-2xl px-2 pl-3 py-3 w-[180px] text-right"
                    v-if="isOpen">
                    <ul>
                        <HeaderDropdownItem
                            v-for="item in dropdownItems"
                            v-bind="item"
                            @click="isOpen = false"
                        />

                        <button @click="changeTheme" id="themeToggle"
                            class="flex place-items-center place-content-end w-full py-1 pr-4 text-center">
                            <SunFilledIcon v-if="store.isDarkMode" class="text-[#fdd663]" />
                            <MoonStarsIcon v-else />
                        </button>
                        <div class="z-10 flex flex-row place-items-center gap-2 mt-4">
                            <div class="w-fit">
                                <LanguageIcon class="dark:text-app-green" />
                            </div>
                            <select v-model="selectedLanguage" class="h-[24px] px-2">
                                <option v-for="option in languageOptions" :value="option.value">
                                    {{ option.text }}
                                </option>
                            </select>
                        </div>
                    </ul>
                </div>
            </div>
        </div>
    </header>
</template>

<style>
#mainDropdown {
    animation: openDD 0.2s ease-in-out forwards;
    transform: scale(1, 0.5);
    transform-origin: top;
}

@keyframes openDD {
    from {
        opacity: 0;
        transform: scale(1, 0.5);
    }

    to {
        transform: scale(1, 1);
        opacity: 1;
    }
}

.link-disabled {
    pointer-events: none;
    cursor: default;
}
</style>
