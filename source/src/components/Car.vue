<template>
    <div>
        <h3>{{ carName }}</h3>
        <p>{{ carYear }}</p>
    </div>
</template>

<script>
    export default {
        props: ["carName", "carYear"],
    }
</script>
