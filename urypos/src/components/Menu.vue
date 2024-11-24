<template>
    <!-- <orderInfo /> -->
    <!-- <Search /> -->
    <div class="grid grid-cols-2 gap-4 mt-2">
        <div class="grid grid-cols-2 gap-4">
            <div class="relative" ref="container">
                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
                    <svg
                    aria-hidden="true"
                    class="h-5 w-5 text-gray-500 dark:text-gray-400"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                    xmlns="http://www.w3.org/2000/svg"
                    >
                    <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                    ></path>
                    </svg>
                </div>
                <input
                    type="search"
                    class="block w-full rounded-lg border border-gray-300 bg-gray-50 p-2.5 pl-10 text-sm text-gray-900 focus:border-blue-500 focus:ring-blue-500 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400 dark:focus:border-blue-500 dark:focus:ring-blue-500"
                    placeholder="Search Customers"
                    v-model="this.customers.search"
                    @input="this.customers.handleSearchInput"
                    @click="this.customers.searchCustomer()"
                    :disabled="this.menu.selectedOrderType === 'Aggregators' || this.recentOrders.previousOrderdCustomer !== ''"
                    required
                />
                <div
                    v-if="this.customers.showCustomers && this.customers.showAddNewCustomer"
                    class="absolute left-0 top-full z-10 max-h-64 w-full overflow-y-scroll rounded bg-white shadow"
                    ref="dropdown"
                >
                    <div
                    class="h-16 rounded-lg p-4 hover:bg-gray-100"
                    v-for="(customer, index) in this.customers.customer"
                    :key="index"
                    @click="this.customers.selectCustomer(customer)"
                    >
                    <h1 class="text-base font-semibold leading-normal">
                        {{ customer.name }}
                    </h1>
                    <h2 class="text-sm leading-normal">
                        {{ customer.name }}
                        {{ customer.content ? this.customers.extractName(customer.content) : "" }}
                    </h2>
                    </div>
                    <div v-if="this.customers.showAddNewCustomer">
                        <div class="flex justify-end">
                            <span class="sr-only">Close</span>
                            <svg
                            xmlns="http://www.w3.org/2000/svg"
                            class="mt-2 mr-2 h-4 w-4"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                            @click="this.customers.showAddNewCustomer = false"
                            >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M6 18L18 6M6 6l12 12"
                            />
                            </svg>
                        </div>
                        <a
                            href="#"
                            class="mt-1 lg:mt-0 inline-flex items-center text-blue-600 hover:underline"
                            @click.prevent="this.customers.newCustomerData(this.customers.search)"
                        >
                            <svg
                            fill="none"
                            stroke="currentColor"
                            class="h-8 w-8 font-extrabold"
                            viewBox="0 0 25 25"
                            xmlns="http://www.w3.org/2000/svg"
                            aria-hidden="true"
                            >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M12 6v12m6-6H6"
                            ></path>
                            </svg>
                            Create New Customer
                        </a>
                    </div>
                </div>
            </div>
            <div class="relative">
                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
                    <svg
                    class="h-6 w-6 text-gray-500 group-hover:text-blue-600 dark:text-gray-400 dark:group-hover:text-blue-500"
                    fill="currentColor"
                    viewBox="0 0 20 20"
                    xmlns="http://www.w3.org/2000/svg"
                    aria-hidden="true"
                    >
                    <path
                        fill-rule="evenodd"
                        d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z"
                        clip-rule="evenodd"
                    ></path>
                    </svg>
                </div>
                <input
                    type="number"
                    id="numberOfPax"
                    class="block w-full rounded-lg border border-gray-300 bg-gray-50 p-2.5 pl-10 text-sm text-gray-900 focus:border-blue-500 focus:ring-blue-500 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400 dark:focus:border-blue-500 dark:focus:ring-blue-500"
                    placeholder="Pax"
                    required
                    v-model="this.customers.numberOfPax"
                    @input="this.customers.validateInput"
                />
            </div>
        </div>
        <div></div>
    </div>

    <nav class="mb-3 mt-2 flex" aria-label="Breadcrumb">
        <ol class="inline-flex items-center space-x-1 rtl:space-x-reverse md:space-x-2">
            <li>
                <div class="flex items-center">
                    <span class="text-base font-medium text-gray-700 dark:text-gray-400">Order</span>
                </div>
            </li>
            <li aria-current="page">
                <div class="flex items-center">
                    <svg class="mx-1 h-3 w-3 text-gray-400 rtl:rotate-180" aria-hidden="true"
                        xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 6 10">
                        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="m1 9 4-4-4-4" />
                    </svg>
                    <span class="ms-1 text-base font-medium text-gray-500 dark:text-gray-400 md:ms-2">{{
                    this.recentOrders.orderNumber }}</span>
                </div>
            </li>
        </ol>
    </nav>
    <div class="grid grid-cols-12 gap-2 mt-4 border-t">
        <div class="col-span-10 pr-2 mb-10">
            <div class="container mx-auto" v-if="this.menu.paginatedItems.length > 0">
                <div class="mt-4 grid xl:grid-cols-6 lg:grid-cols-6 md:grid-cols-4 grid-cols-3 gap-2">
                    <div class="rounded border px-2 py-2 text-left shadow" v-for="item in this.menu.paginatedItems"
                        :key="item.item">
                        <div class="w-100" v-if="this.auth.viewItemImage">
                            <div v-if="item.item_imgae">
                                <img :src="this.menu.getFullImagePath(item.item_imgae)" alt="Item Image"
                                    class="min-h-26 rounded" />
                            </div>
                            <div v-else class="relative">
                                <img :src="`https://dummyimage.com/640x640/f9fafa/fff&text=+`" alt="Item Image"
                                    class="min-h-26 rounded" />
                                <div class="absolute inset-0 flex items-center justify-center">
                                    <span class="text-3xl text-gray-400">{{
                    this.menu.itemNameExtract(item.item_name)
                }}</span>
                                </div>
                            </div>
                        </div>

                        <h2 class="mt-0" :class="{
                        'text-md overflow-hidden whitespace-nowrap text-gray-600':
                            this.auth.viewItemImage,
                        'mb-2 text-center text-lg font-normal leading-normal':
                            !this.auth.viewItemImage,
                    }">
                            {{ item.item_name }}
                        </h2>
                        <h2 class="mt-0" :class="{
                    'text-sm font-bold': this.auth.viewItemImage,
                    'mb-2 mt-0 text-center text-lg font-normal leading-normal':
                        !this.auth.viewItemImage,
                }">
                            {{ this.invoiceData.currency }} {{ item.rate }}
                        </h2>
                        <div v-if="!item.qty" class="text-center">
                            <button @click="
                    item.showInput = true;
                this.menu.addToCart(item);
                " class="rounded border px-10 pb-2 pt-2.5 text-xs font-medium leading-normal">
                                ADD +
                            </button>
                        </div>
                        <div v-if="item.qty" class="flex rounded-md text-center">
                            <button type="button"
                                class="inline-flex items-center justify-center gap-2 border bg-white px-4 py-3 align-middle text-sm font-medium shadow-sm transition-all focus:outline-none dark:border-gray-700"
                                :class="{
                    'text-gray-700':
                        this.recentOrders.editPrintedInvoice === 0 ||
                        this.auth.removeTableOrderItem === 1,
                    'text-gray-300':
                        this.recentOrders.editPrintedInvoice === 1 ||
                        this.auth.removeTableOrderItem === 0,
                }" :disabled="this.recentOrders.restaurantTable" @click="
                    (this.recentOrders.editPrintedInvoice === 0 ||
                        this.auth.removeTableOrderItem === 1) &&
                    this.menu.decrementItemQuantity(item)
                    ">
                                -
                            </button>
                            <input type="number" id="qty_input" name="qty_input"
                                class="block w-full border border-gray-200 text-center text-sm shadow-sm"
                                :value="item.qty" @input="item.qty = $event.target.value" readonly
                                @click="this.menu.showModal(item)" />
                            <button type="button"
                                class="-ml-px inline-flex items-center justify-center gap-2 border bg-white px-4 py-3 align-middle text-sm font-medium text-gray-700 shadow-sm transition-all focus:outline-none dark:border-gray-700"
                                @click="this.menu.incrementItemQuantity(item)">
                                +
                            </button>
                        </div>
                    </div>
                    <div v-if="menu.showDialog" class="fixed inset-0 z-10 mt-20 overflow-y-auto bg-gray-100">
                        <div class="mt-10 flex items-center justify-center">
                            <div class="w-full rounded-lg bg-white p-6 shadow-lg md:max-w-md">
                                <div class="flex justify-end">
                                    <span class="sr-only">Close</span>
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none"
                                        viewBox="0 0 24 24" stroke="currentColor" @click="menu.showDialog = false">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M6 18L18 6M6 6l12 12" />
                                    </svg>
                                </div>

                                <h2 class="mt-1 block text-left text-xl font-medium text-gray-900 dark:text-white">
                                    Enter Details
                                </h2>
                                <div class="relative">
                                    <label for="quantity" class="mt-6 block text-left text-gray-900 dark:text-white">
                                        Quantity
                                    </label>
                                    <input type="number" id="quantity"
                                        class="mt-4 w-full appearance-none rounded border p-2 leading-tight text-gray-900 shadow focus:outline-none"
                                        v-model="this.menu.quantity" v-bind:readonly="this.recentOrders.editPrintedInvoice === 1 &&
                    this.auth.removeTableOrderItem === 0
                    " :disabled="this.recentOrders.restaurantTable" />
                                    <label for="comments" class="mt-6 block text-left text-gray-900 dark:text-white">
                                        Comments
                                    </label>
                                    <input type="text" id="Comments"
                                        class="mt-4 w-full rounded border p-2 leading-tight text-gray-900 shadow focus:outline-none"
                                        v-model="this.menu.itemComments" />
                                </div>
                                <div class="flex justify-end">
                                    <button @click="this.menu.addToCartAndUpdateQty(item)"
                                        class="mt-8 rounded bg-blue-500 px-3 py-2 text-white hover:bg-blue-600">
                                        Add
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div v-else>
                <div v-if="this.menu.items.length === 0" class="flex h-screen items-center justify-center">
                    <div class="text-center">
                        Items Not Found. Please select a table or set an active menu.
                    </div>
                </div>
                <div v-else class="flex h-screen items-center justify-center">
                    <div class="text-center">Items Not Found.</div>
                </div>
            </div>
        </div>
        <div class="col-span-2 border-l p-2 mb-10">
            <div class="grid grid-cols-12 mt-2">
                <div class="relative w-full col-span-12">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center mx-2">
                        <svg aria-hidden="true" class="h-5 w-5 text-gray-500 dark:text-gray-400" fill="none"
                            stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                        </svg>
                    </div>
                    <input type="search" id="default-search"
                        class="block w-full rounded border border-gray-300 bg-gray-50 px-10 pb-2 pt-2.5 text-sm font-medium text-gray-900 focus:border-blue-500 focus:ring-blue-500 dark:border-gray-600 dark:bg-gray-700 dark:text-white dark:placeholder-gray-400 dark:focus:border-blue-500 dark:focus:ring-blue-500"
                        placeholder="Item Search" v-model="this.menu.searchTerm" @input="this.menu.handleSearchInput;"
                        @click="this.menu.clearSearch" autocapitalize="none" />
                </div>

                <div class="grid grid-cols-12 col-span-12 mt-4">
                    <button
                        class="xl:col-span-6 lg:col-span-6 col-span-12 focus:shadow-outline mx-2 my-2 rounded-md bg-blue-700 px-4 py-2 text-center font-semibold text-white hover:bg-blue-800 focus:outline-none focus:ring focus:ring-blue-300"
                        type="button" :class="{ 'bg-blue-900': this.menu.priority }"
                        @click="this.menu.showSpecialItems">
                        Priority
                    </button>
                    <button
                        class="xl:col-span-6 lg:col-span-6 col-span-12 focus:shadow-outline mx-2 my-2 rounded-md bg-blue-700 px-4 py-2 text-center font-semibold text-white hover:bg-blue-800 focus:outline-none focus:ring focus:ring-blue-300"
                        type="button" :class="{ 'bg-blue-900': this.menu.displayAll }" @click="this.menu.showAllItems">
                        All
                    </button>
                    <hr class="col-span-12 border-t border-gray-300 my-2">
                    <button v-for="(course, index) in menu.course" :key="index"
                        class="xl:col-span-6 lg:col-span-6 col-span-12 focus:shadow-outline mx-2 my-2 rounded-md bg-blue-700 px-2 py-2 text-center font-medium text-white hover:bg-blue-800 focus:outline-none focus:ring focus:ring-blue-300"
                        type="button" :class="{ 'bg-blue-900': this.menu.selectedCourse === course.name }"
                        @click="this.menu.displayAll = false; this.menu.selectedCourse = course.name">
                        {{ course.name }}
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="fixed left-0 right-0 bottom-16 p-1 flex justify-center bg-white border-t"
        v-if="this.menu.paginatedItems.length > 0">
        <button :class="{ hidden: this.menu.currentPage === 1 }" :disabled="this.menu.currentPage === 1"
            @click="this.menu.currentPage -= 1" class="mr-2 rounded-md border px-2 py-1">
            Previous
        </button>
        <div v-for="pageNumber in this.menu.pageNumbers">
            <button v-if="pageNumber === this.menu.currentPage ||
                    Math.abs(pageNumber - this.menu.currentPage) <= 2
                    " :key="pageNumber" @click="this.menu.currentPage = pageNumber"
                :class="{ 'bg-gray-200': pageNumber === this.menu.currentPage }"
                class="mr-2 rounded-md border px-2 py-1">
                {{ pageNumber }}
            </button>
            <span v-else-if="this.menu.pageNumbers.indexOf(pageNumber) === 0 ||
                    this.menu.pageNumbers.indexOf(pageNumber) ===
                    this.menu.pageNumbers.length - 1
                    ">
                ...
            </span>
        </div>
        <button :disabled="this.menu.currentPage === this.menu.totalPages" @click="this.menu.currentPage += 1"
            :class="{ hidden: this.menu.currentPage === this.menu.totalPages }" class="rounded-md border px-2 py-1">
            Next
        </button>
    </div>
</template>

<script>
import Search from "./Search.vue";
import orderInfo from "./orderInfo.vue";
import frappe from "@/stores/frappeSdk.js";
import { useMenuStore } from "@/stores/Menu.js";
import { useAuthStore } from "@/stores/Auth.js";
import { usetoggleRecentOrder } from "@/stores/recentOrder.js";
import { useInvoiceDataStore } from "@/stores/invoiceData.js";
import { useCustomerStore } from "@/stores/Customer.js";


export default {
    data() {
        return {
            frappe: frappe,
        };
    },
    setup() {
        const menu = useMenuStore();
        const auth = useAuthStore();
        const recentOrders = usetoggleRecentOrder();
        const invoiceData = useInvoiceDataStore();
        const customers = useCustomerStore();
        return { menu, auth, recentOrders, invoiceData, customers };
    },
    name: "Menu",
    components: {
        Search,
        orderInfo,
    },
    mounted() {
        window.scrollTo(0, 0);
    },
};
</script>
<style>
.bg-gray-100 {
    background-color: rgba(0, 0, 0, 0.2);
}
</style>