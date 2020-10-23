<template>
<div class="hello">

    <Selector title="Single tem" :items="heroes" v-on:select="selectOne"></Selector>
    <Selector title="Multiple items" :items="heroes" v-on:select="selectMany" multiple></Selector>

</div>
</template>

<script>

import Selector from './selector.vue'

export default {
    name: 'HelloWorld',
    components: {
        Selector,
    },
    data() {
        return {
            heroes: [
                { id: 1, title: "AAA", summary: "Lorem ipsum dolor sit amet." },
                { id: 2, title: "ABC", summary: "Lorem ipsum dolor sit amet." },
                { id: 3, title: "ABCDE", summary: "Lorem ipsum dolor sit amet." },
            ]
        }
    },
    methods: {
        selectOne(selected) {
            console.log("selectOneHero")
            console.log(selected ? selected.id : null)
        },
        selectMany(selected) {
            console.log("selectHeroes")
            console.log(selected.map(item => item.id))
        },
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
    margin: 40px 0 0;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}
</style>
