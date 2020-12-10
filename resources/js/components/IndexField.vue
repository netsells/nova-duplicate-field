<template>
    <button
        @click.prevent="onClick"
        class="cursor-pointer text-70 hover:text-primary no-underline flex items-center"
        :disabled="states.loading"
        v-tooltip.click="__('Duplicate')"
    >
        <svg
            v-if="!states.loading"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            width="24"
            height="24"
            class="fill-current"
        >
            <path
                d="M17 7h2.25c.97 0 1.75.78 1.75 1.75v10.5c0 .97-.78 1.75-1.75 1.75H8.75C7.78 21 7 20.22 7 19.25V17H4.75C3.78 17 3 16.22 3 15.25V4.75C3 3.78 3.78 3 4.75 3h10.5c.97 0 1.75.78 1.75 1.75V7zm-2 0V5H5v10h2V8.75C7 7.78 7.78 7 8.75 7H15zM9 9v10h10V9H9z"
            />
        </svg>
        <div
            v-else
            class="spinner"
            v-tooltip.click="__('Copying...')"
        >
           <div class="dot dot-1" />
           <div class="dot dot-2" />
           <div class="dot dot-3" />
        </div>

        <span v-if="this.field.showText">- Duplicate</span>
    </button>
</template>

<script>
import axios from 'axios';

export default {
    props: ["resourceName", "field"],

    data() {
        return {
            states: {
                loading: false,
            },
        };
    },

    mounted() {
        const parentElement = this.$el.parentElement;
        parentElement.classList.add('td-fit');
        parentElement.style.paddingRight = "0px";
    },

    methods: {
        async onClick() {
            this.states.loading = true;

            try {
                const { data } = await axios.post('/nova-vendor/jackabox/nova-duplicate', {
                    model: this.field.model ? this.field.model : '',
                    id: this.field.id ? this.field.id : '',
                    resource: this.field.resource ? this.field.resource : '',
                    relations: this.field.relations ? this.field.relations : '',
                    except: this.field.except ? this.field.except : '',
                    override: this.field.override ? this.field.override : '',
                });

                window.location.replace(data.destination);
            } catch (error) {
                console.log(error);
            } finally {
                this.states.loading = false;
            }
        },
    },
};
</script>

<style lang="scss" scoped>
    .spinner {
        margin: 0 auto;
        width: 24px;
        text-align: center;
    }

    .dot {
        width: 6px;
        height: 6px;
        background-color: rgb(27, 42, 94);
        border-radius: 50%;
        display: inline-block;
        animation: bounce 1.4s infinite ease-in-out both;
    }

    .dot-1 {
        animation-delay: -0.32s;
    }

    .dot-2 {
        animation-delay: 0.16s;
    }

    @keyframes bounce {
        0%, 80%, 100% {
            transform: scale(0);
        } 40% {
              -webkit-transform: scale(1.0);
        }
    }
</style>