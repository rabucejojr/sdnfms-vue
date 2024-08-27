<template>
    <div v-for="(tag, index) in tags" :key="tag">
        {{ index + ' : ' + tag }}
    </div>
    <div>
        {{ tags.length }}
    </div>
    <input type="text" :value="newTag" v-on:keydown.enter="tags.push($event.target.value  )">
</template>

<script>
export default {
    data() {
        return {
            tags: ["vue","react","angular"],
            newTag:"preact"
        }
    },
}
</script>