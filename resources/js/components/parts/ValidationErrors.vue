<template>
    <div>
        <div v-if="validationErrors" class="alert alert-danger">
            <ul>
                <li v-for="(value, index) in validationErrors"
                    :key="index">
                    {{ value }}
                </li>
            </ul>
        </div>
    </div>
</template>

<!--by Aziz Rozmetov => Gringo 2021-02-28, Sun, 4:6-->

<script>
    export default {
        props:{
            errors: Object,
        },
        computed:{
            validationErrors(){
                let errors = Object.values(this.errors)
                errors = errors.flat()

                return errors
            }
        },
    }

</script>

<style scoped>

</style>

<!--fileName => ValidationErrors.vue path => resources\js\components\parts\ValidationErrors.vue -->
