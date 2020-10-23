<template>
    <div class="selector">

        <!-- Title -->
        <div>
            <p class="selector__title">
                {{ title }}:
            </p>
        </div>

        <p>selected: {{ selected }}</p>

        <!-- Selected list -->
        <div>
            Selected:
            <span v-if="!selected || !selected.length">nothing</span>

            <div v-if="!multiple && selected" class="selector__selected-list">
                <span class="selector__selected-item" @click="deselectItem(selected)">
                    {{ selected.title }}
                </span>
            </div>

            <div v-if="multiple && selected.length" class="selector__selected-list">
                <span class="selector__selected-item" v-for="item in selected" :key="item.id" @click="deselectItem(item)">
                    {{ item.title }}
                </span>
            </div>

        </div>

        <!-- Filter -->
        <div>
            <input class="selector__filter" type="search" v-model="filter">
        </div>

        <!-- Items list -->
        <div class="selector__items">
            <ul class="selector__items-list">
                <li
                class="selector__item"
                v-for="item in itemsFiltered"
                :class="{'selected': isSelected(item)}"
                :key="item.id">
                    <span @click="selectItem(item)" class="left">{{ item.title }}</span>
                    <span @click="selectItem(item)" class="right">{{ item.summary }}</span>
                    <a href="https://yandex.ru" target="_blank">Open</a>
                </li>
            </ul>
        </div>

    </div>
</template>


<script>

export default {
    props: {
        title: {
            type: String,
            default: "Выбрано"
        },
        items: {
            type: Array,
            required: true
        },
        preSelected: {
            default: null
        },
        multiple: {
            type: Boolean,
            default: false
        },
    },
    data () {
        return {
            selected: null, // Number | Array
            filter: "",
        }
    },
    computed: {
        itemsFiltered() {
            if ( this.filter == '*' ) {
                return this.items
            }
            if( !this.filter.length ) {
                return []
            } else {
                return this.items.filter(item => item.title.toLowerCase().indexOf(this.filter.toLowerCase()) != -1)
            }
        },
    },
    methods: {
        selectItem(selectedItem) {
            if( selectedItem && !this.multiple ) {
                this.selected = (this.selected == selectedItem) ? null : selectedItem
            }

            if( selectedItem && this.multiple ) {
                if( this.selected.includes(selectedItem) ) {
                    let index = this.selected.findIndex(item => item == selectedItem)
                    this.selected.splice(index, 1)
                } else {
                    this.selected.push(selectedItem)
                }
            }

            this.$emit('select', this.selected)
        },
        deselectItem(selectedItem) {
            if( this.multiple ) {
                let index = this.selected.findIndex(item => item == selectedItem)
                this.selected.splice(index, 1)
            } else {
                this.selected = null
            }

            this.$emit('select', this.selected)
        },
        isSelected(selectedItem) {
            if( !this.multiple ) {
                return this.selected == selectedItem
            } else {
                return this.selected.includes(selectedItem)
            }
        },
    },
    created() {
        if( this.multiple ) {
            this.selected = []
        }
    }
}
</script>

<style>

.selector {
    width: 800px;
    margin: 20px auto;
    padding: 10px;
    background: #ddd;
}

.selector__title {
    font-size: 18px;
    font-weight: 700;
}

.selector__selected {
    background: #fbb;
}

.selector__selected-list {
}

.selector__selected-item {
    display: inline-block;
    padding: 5px 10px;
    margin: 1px;
    cursor: pointer;
    filter: brightness(1.1);
    background: #f77;
}

.selector__filter {
    border: none;
    background: #aaa;
    padding: 10px;
    margin: 5px;
}

.selector__items {
    margin-top: 10px;
    background: #999;
}

.selector__items-list {
    list-style: none;
    margin: 0;
    padding: 0;
}

.selector__item {
    display: grid;
    grid-template-columns: 1fr 1fr 60px;
    background: #aaa;
    cursor: pointer;
    padding: 5px;
}

.selector__item span, .selector__item a {
    padding: 5px;
}

.selector__item .left {
    text-align: left;
}
.selector__item .right {
    text-align: right;
}

.selector__item:hover {
    filter: brightness(1.1);
}

.selected {
    background: green;
}

</style>
