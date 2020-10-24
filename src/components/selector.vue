<template>
    <div class="selector">

        <!-- Title -->
        <div>
            <p class="selector__title">
                {{ title }}:
            </p>
        </div>

        <!-- Selected list -->
        <div>
            Выбрано:
            <span v-if="!selected || !selected.length">пусто</span>

            <div v-if="!multiple && selected" class="selector__selected-list">
                <span class="selector__selected-list__item" @click="deselectItem(selected)">
                    {{ selectedToData[0].title }}
                </span>
            </div>

            <div v-if="multiple && selected.length" class="selector__selected-list">
                <span class="selector__selected-list__item" v-for="item in selectedToData" :key="item.id" @click="deselectItem(item.id)">
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
                class="selector__items-list__item"
                v-for="item in itemsFiltered"
                :class="{'selected': isSelected(item)}"
                :key="item.id">
                    <span @click="selectItem(item)" class="left">{{ item.title }}</span>
                    <span @click="selectItem(item)" class="right">{{ item.summary }}</span>
                    <a :href="`/${type}/${item.id}`" target="_blank">Open</a>
                </li>
            </ul>
        </div>

    </div>
</template>


<script>
export default {
    props: {
        type: {
            type: String,
            required: true
        },
        title: {
            type: String,
            default: "Выбрано"
        },
        items: {
            type: Array,
            required: true
        },
        modelValue: {
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
        selectedToData() {
            if( !this.multiple ) {
                return this.items.filter(item => item.id == this.selected)
            } else {
                return this.items.filter(item => this.selected.includes(item.id))
            }
        },
        selectedIdsToData() {
            return this.items.filter(item => [this.selected].includes(item.id))
        },
        itemsFiltered() {
            if ( this.filter == '*' ) {
                return this.items
            }
            if( !this.filter.length ) {
                return []
            } else {
                return this.items.filter(item =>
                    item.title.toLowerCase().indexOf(this.filter.toLowerCase()) != -1
                )
            }
        },
    },
    methods: {
        selectItem(selectedItem) {
            if( selectedItem && !this.multiple ) {
                this.selected = (this.selected == selectedItem.id) ? null : selectedItem.id
                this.$emit('update:modelValue', this.selected)
            }
            if( selectedItem && this.multiple ) {
                if( this.selected.includes(selectedItem.id) ) {
                    let index = this.selected.findIndex(item => item == selectedItem.id)
                    this.selected.splice(index, 1)
                } else {
                    this.selected.push(selectedItem.id)
                }
                this.$emit('update:modelValue', this.selected)
            }
        },
        deselectItem(selectedItem) {
            if( this.multiple ) {
                const index = this.selected.findIndex(item => item == selectedItem)
                this.selected.splice(index, 1)
                this.$emit('update:modelValue', this.selected)
            } else {
                this.selected = null
                this.$emit('update:modelValue', this.selected)
            }
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
        if( this.modelValue ) {
            this.selected = this.modelValue
        }
    }
}
</script>

<style lang="scss">

$transparent: rgba(180,180,255,0.05);
$transparent_light: rgba(180,180,255,0.25);

.selector {
    // width: 800px;
    margin: 20px auto;
    padding: 10px;
    &__title {
        font-size: 18px;
        font-weight: 700;
        margin-bottom: 10px;
    }
    &__selected {
        background: #fbb;
    }
    &__selected-list {
        &__item {
            display: inline-block;
            padding: 5px 10px;
            margin: 1px;
            cursor: pointer;
            filter: brightness(1.1);
            color: white;
            // border: 1px dashed red;
        }
    }
    &__filter {
        border: none;
        background: #aaa;
        padding: 10px;
        margin: 5px;
    }
    &__items {
        margin-top: 10px;
    }
    &__items-list {
        list-style: none;
        margin: 0;
        padding: 0;
        span, a {
            padding: 10px;
        }
        &__item {
            display: grid;
            grid-template-columns: 1fr 1fr 60px;
            background: $transparent;
            cursor: pointer;
            &:hover {
                background: $transparent_light;
                filter: brightness(1.1);
            }
            &.selected {
                background: $transparent_light;
            }
            .left {
                text-align: left;
            }
            .right {
                text-align: right;
            }
        }
    }
}
</style>
