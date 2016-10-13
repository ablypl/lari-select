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
            name: {},
            return: {
                default: 'id'
            },
            resources: {
                default: false
            },
            selected : {},
            labels: {},
            withcreate:{
                default: true
            }
        },
        components: { SelectDropdown },
        data(){
            return {
                opened: false,
                item: false
            }
        },

        methods: {
            toggle(){
                this.opened = !this.opened

                this.$refs.dropdown.loadData()

                setTimeout( () => {
                    this.$refs.dropdown.$refs.input.focus()
                }, 200 )

            }
        },
        computed: {
            id(){
                if (this.current) {
                    return this.current.id
                }
            },
            label(){
                const labels = {
                    placeholder: "Select element"
                }
                return Object.assign(labels, this.labels)
            },
            text(){
                if (!this.current) {
                    return this.label.placeholder
                }
                return this.current[this.field]
            },
            current(){
                if (this.selected && !this.item) {
                    return this.selected
                }
                return this.item
            }
        },
        created(){
            this.$on('ldropdown.item.selected', (item) => {
                this.opened = false
                this.item = item
            })
        }

    }

</script>
<template>
    <div class="is-relative">
        <input type="hidden" :name="name" :value="id">
        <div class="l-select" @click="toggle" :class="{ 'is-opened' : opened }">
             <span>{{ text }}</span><i></i>
        </div>
        <select-dropdown
            v-show="opened"
            :field="field"
            :withcreate="withcreate"
            :resources="resources"
            ref="dropdown" v-cloak></select-dropdown>
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



</style>
