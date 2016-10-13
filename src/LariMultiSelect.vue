<script type="text/babel">
    import SelectDropdown from './LariDropdown.vue'
    export default {
        props: {
            url: {
                default: '/tags'
            },
            field: {
                default: 'name'
            },
            name: { default: 'tags' },
            return: {
                default: 'id'
            },
            // allow user to create new element if tag does not exist
            withcreate: {
                default: true
            },
            selected : {
                default: false
            },
            labels: {
                default: () => {}
            },
            // You can provide an array of items
            resources: {
                default: false
            },
        },
        components: { SelectDropdown },
        data(){
            return {
                opened: false,
                question: false,
                items: []
            }
        },

        methods: {
            toggle(){
                this.opened = !this.opened

                this.$refs.dropdown.loadData()

                setTimeout( () => {
                    this.$refs.dropdown.$refs.input.focus()
                }, 200 )

            },
            pushSelected(){
                this.selected.forEach(item => {
                    // this.items.push(item)
                    this.$refs.dropdown.push(item)
                })
            },
            remove($item){
                let index = this.items.indexOf($item)
                this.items.splice(index, 1)
            },
            toggleConfirm(){
                this.question = !this.question
            },
            clear(){
                this.items = []
                this.toggleConfirm()
            }
        },
        computed: {
            text(){
                const labels = {
                    add:        "Add tags",
                    clear:      "Clear tags",
                    delete:     "Delete all elements?",
                    yes:        "Yes",
                    no:         "No"
                }

                return Object.assign(labels, this.labels)
            },

            filteredItems(){
                const self = this

                return this.items.filter((el, pos) => {
                    return self.items.indexOf(el) == pos
                })
            },
            count(){
                return this.items.length
            },
            inputName(){
                return `${this.name}[]`
            }
        },
        created(){

            this.$on('ldropdown.item.selected', (item) => {
                if (item) {
                    this.items.push(item)
                }
            })
        },
        mounted(){
            if (this.selected) this.items = this.selected
        }
    }

</script>
<template>
    <div class="is-relative">
        <input type="hidden" :name="inputName" value="" v-if="!count">
        <input type="hidden" v-for="tag in filteredItems" key="tag.id" :name="inputName" :value="tag.id">
        <div class="l-tags">
            <span class="tag" v-for="tag in filteredItems" key="tag.id">{{ tag[field] }} <button class="delete" @click.prevent="remove(tag)"></button></span>
        </div>
        <div v-if="question" class="l-confirm">
            {{ text.delete }} <button class="button is-small is-danger" @click.prevent="clear">{{ text.yes }}</button> <button @click.prevent="question = false" class="button is-small">{{ text.no }}</button>
        </div>
        <button class="button is-success" @click.prevent="toggle">{{ text.add }}</button>
        <button v-if="count" class="button is-link" @click.prevent="toggleConfirm">{{ text.clear }}</button>
        <select-dropdown ref="dropdown"
            :resources="resources"
            v-show="opened"
            :selected.sync="items"
            :field="field"
            :withcreate="withcreate"
            :url="url"
             v-cloak></select-dropdown>
        <div v-if="opened" class="l-overlay" @click="opened = false"></div>
    </div>
</template>
<style lang="sass?indentedSyntax">
    $font-size: 14px
    $grey-darker: #222324
    $grey-dark: #69707a
    $grey: #aeb1b5
    $grey-light: #d3d6db
    $grey-lighter: #f5f7fa

    .l-confirm
        display: flex
        align-items: center
        padding: 20px 0
        transition: all .5s ease
        animation: slideBoxDown .5s

        .button
            margin-left: 10px

    .tag
        background: darken($grey-lighter, 5)
        border-radius: 20px
        font-size: 13px
        display: inline-block
        margin: 0 5px 5px 0
        padding: 4px 10px
        + .tag
            margin-left: 5px
        .delete
            margin-right: -6px
    .delete
        background: $grey
        border-radius: 50%
        border: 0
        cursor: pointer
        height: 16px
        width: 16px
        position: relative
        transition: all .5s
        &:focus
            outline: none
        &:hover
            background: #EB5160
        &:before,
        &:after
            background-color: #fff
            content: ""
            display: block
            height: 2px
            left: 50%
            margin-left: -25%
            margin-top: -1px
            position: absolute
            top: 50%
            width: 50%
        &:before
            transform: rotate(45deg)
        &:after
            transform: rotate(-45deg)

    .l-tags
        padding: 20px 0

    .is-relative
        position: relative
    .l-overlay
        position: fixed
        top: 0
        left: 0
        right: 0
        bottom: 0
        z-index: 1
    .l-select
        background: $grey-lighter
        border: 1px solid $grey-light
        border-radius: 4px
        color: $grey-dark
        font-weight: 500
        display: inline-block
        position: relative;
        padding: 8px 50px 8px 10px
        margin-bottom: 5px
        cursor: pointer
        transition: all .5s
        &.is-opened,
        &:hover
            box-shadow: inset 0px 0px 8px 0px $grey;

        i
            position: absolute;
            top: 50%
            transform: translateY(-50%)
            right: 10px
            width: 0
            height: 0
            border-style: solid
            border-width: 4px 4px 0 4px
            border-color: $grey-dark transparent transparent transparent

        &.is-opened
            i
                transform: rotate(90deg) translateX(-30%)


    .button
        align-items: center
        border: 1px solid #d3d6db
        border-radius: 3px
        display: inline-flex
        font-size: 14px
        height: 32px
        cursor: pointer
        justify-content: flex-start
        line-height: 24px
        padding-left: 8px
        padding-right: 8px
        position: relative
        vertical-align: top
        justify-content: center
        background: #f5f7fa
        color: #51545D
        font-weight: 500
        &:focus
            outline: none
        &.is-success
            background-color: #61D095
            border-color: transparent
            color: white

    @keyframes slideBoxDown
        0%
            transform: translateY(-100%)
            opacity: 0
        100%
            transform: translateY(0)
            opacity:

</style>
