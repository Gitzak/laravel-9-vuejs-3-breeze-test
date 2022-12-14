<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Head } from "@inertiajs/inertia-vue3";
</script>

<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Books
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 bg-white border-b border-gray-200">
                        <div
                            class="bg-teal-100 border-t-4 border-teal-500 rounded-b text-teal-900 px-4 py-3 shadow-md my-3"
                            role="alert"
                            v-if="$page.props.flash.message"
                        >
                            <div class="flex">
                                <div>
                                    <p class="text-sm">
                                        {{ $page.props.flash.message }}
                                    </p>
                                </div>
                            </div>
                        </div>
                        <button
                            @click="openForm()"
                            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-3"
                        >
                            + Create New Book
                        </button>
                        <table class="table-fixed w-full">
                            <thead>
                                <tr class="bg-gray-100">
                                    <th class="px-4 py-2 w-20">No.</th>
                                    <th class="px-4 py-2">Title</th>
                                    <th class="px-4 py-2">Author</th>
                                    <th class="px-4 py-2">Image</th>
                                    <th class="px-4 py-2">Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="item in data.data">
                                    <td class="border px-4 py-2 text-center">
                                        {{ item.id }}
                                    </td>
                                    <td class="border px-4 py-2">
                                        {{ item.title }}
                                    </td>
                                    <td class="border px-4 py-2">
                                        {{ item.author }}
                                    </td>
                                    <td class="border px-4 py-2">
                                        <!-- <img
                                            v-if="item.image"
                                            :src="image_path(item.image)"
                                        /> -->
                                        <div class="flex justify-center" v-if="item.image">
                                            <img width="80" style="margin:2px" v-for="img in item.image.split('|')" :src="image_path(img)" />
                                        </div>
                                    </td>
                                    <td class="border px-4 py-2">
                                        <div class="flex justify-center">
                                            <button
                                                @click="openForm(item)"
                                                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded mx-2"
                                            >
                                                Edit
                                            </button>
                                            <button
                                                @click="deleteItem(item)"
                                                class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded mx-2"
                                            >
                                                Delete
                                            </button>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <pagination :links="data.links"></pagination>
                        <book-form
                            :isOpen="isFormOpen"
                            :isEdit="isFormEdit"
                            :form="formObject"
                            @formsave="saveItem"
                            @formclose="closeModal"
                        ></book-form>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>

<script>
const defaultFormObject = {
    title: null,
    author: null,
    image: null,
};

import Pagination from "@/Components/Pagination.vue";
import BookForm from "@/Components/Book/Form.vue";

export default {
    props: ["data"],
    components: {
        Pagination,
        BookForm,
    },
    data() {
        return {
            isFormOpen: false,
            isFormEdit: false,
            formObject: defaultFormObject,
        };
    },
    methods: {
        image_path(image) {
            return "/" + image;
        },
        saveItem(item) {
            let url = "/books";
            if (item.id) {
                url = "/books/" + item.id;
                item._method = "PUT";
            }
            this.$inertia.post(url, item, {
                onError: () => {},
                onSuccess: () => {
                    this.closeModal();
                },
            });
        },
        closeModal() {
            this.isFormOpen = false;
        },
        openForm(item) {
            this.isFormOpen = true;
            this.isFormEdit = !!item;
            this.formObject = item
                ? Object.assign({}, item)
                : defaultFormObject;
            this.$page.props.errors = {};
        },
        deleteItem(item) {
            if (window.confirm("are you sure?")) {
                this.$inertia.post("/books/" + item.id, {
                    _method: "DELETE",
                });
            }
        },
    },
};
</script>
