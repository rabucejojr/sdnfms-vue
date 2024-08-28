<template>
    <div v-for="(tag, index) in tags" :key="tag">
        {{ tag }}
        <a @click.prevent="removeTag(index)" href="#">&times;</a>
    </div>
    <input type="text" 
    v-model.trim="newTag"
    @keydown.enter="addNewTag"
    @keydown.delete="removeLastTag"
    @keydown.tab.prevent="addNewTag"
    :class="{'tags-exists' : isTagExists}"
    >
</template>

<script>
export default {
    data() {
        return {
            tags: ["vue","react","angular"],
            newTag:""
        }
    },
    watch:{
        newTag(newVal,oldVal){
            console.log(newVal,oldVal)
        }
    },
    computed:{
        isTagExists(){
           return this.tags.includes(this.newTag)
        }
    },
    methods:{
        addNewTag(){
            if(this.newTag && !this.isTagExists){
                this.tags.push(this.newTag);
                this.newTag = ""
            }
        },
        removeTag(index){
            this.tags.splice(index,1);
        },
        removeLastTag(){
            if(this.newTag.length ===0){
                this.removeTag(this.tags.length -1)
            }
        }
    }
}
</script>

<style scoped>
.tags-exists{
    color:red;
    text-decoration:line-through;
}
</style>