<template>
    <div>
    
    <p>Hello {{ username }} Whats going on?</p>
    
    </div>
</template>

<script>
    export default {
    props: [
        "username"
    ],
        name:"Home"
    }
</script>