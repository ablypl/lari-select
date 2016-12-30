<script type="text/babel">
    export default {
        props: {
            // default resource name
            name: {
                default: 'tag'
            },
            // which filed should be displayed
            field: {
                default: 'name'
            },
            // url for items list
            url: {
                default: '/tags'
            },
            // You can provide an array of items
            resources: {
                default: false
            },
            // allow user to create new element if tag does not exist
            withcreate: {
                default: true
            },
            // select on init
            selected: {
                default: () => []
            },
            labels: {
                default: () => []
            }
        },
        data(){
            return {
                search: '',
                index: 0,
                loading: false,
                cached: false,
                loaded: [],
                items: []
            }
        },
        watch: {
            search(){
                // reset index if new search was performed
                this.index = 0
            }
        },
        methods: {
            // load remote data if no local data provided
            loadData(){
                if (!this.cached && !this.resources) {
                    this.loading = true
                    this.$http.get(this.url).then(response => {
                        this.items = response.data;
                        this.loading = false;
                        this.cached = true;
                    })
                }
                if (this.resources) {
                    this.items = this.resources
                }
            },
            // handle move down action
            down(){
                if (this.index < this.count) {
                    this.index++
                }
            },
            // handle move up action
            up(){
                if (this.index > 0) {
                    this.index--
                }
            },
            // create new resource
            createNew(){
                const self = this
                if (this.search && this.withcreate) {
                    this.loading = true

                    this.$http.post(this.url, { [this.field]: this.search }).then(response => {
                        if (response.ok) {
                            self.push(response.data)
                            this.items.push(response.data)
                            self.loading = false
                            this.search = ''
                        }
                    })
                }
            },

            // select on click
            select(i){
                if (i == "create" && this.withcreate) {
                    return this.createNew()
                }
                this.push(this.list[i])
            },
            // handle escape key when list is opened
            escape(){
                this.search = ''
                this.$parent.opened = false
            },
            // select item using enter key
            enter(){
                if (this.index == this.count && this.withcreate) {
                    return this.createNew()
                }else{
                    this.push(this.list[this.index])
                }
            },
            // select item when we hover it
            over(i){
                this.index = i
            },
            push($item){
                this.$parent.$emit('ldropdown.item.selected', $item)
            }
        },
        computed: {
            // labels
            text(){
                const labels = {
                    typeText:       "Type element name",
                    create:         "Create",
                    no_resuts:      "No results",
                    search:         "Search"
                }
                return Object.assign(labels, this.labels)
            },


            // filtered list
            list(){
                let selected = []
                this.selected.forEach(item => {
                    selected.push(item.id)
                })
                return this.items.filter(item => {
                    return !selected.includes(item.id)
                }).filter((item) => {
                    let filter = this.search.toUpperCase()
                    return item.name.toUpperCase().indexOf(filter) > -1
                }).slice(0, 10)
            },
            // count filterd items
            count(){
                return this.list.length
            }
        },

    }
</script>

<template>
    <div class="l-dropdown">
        <p class="l-control">
            <svg class="addon" width="16" height="16" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path d="M20.003 18.586L18.59 20l-4.244-4.242 1.414-1.415 4.243 4.243zM8.003 14c-3.308 0-6-2.69-6-6 0-3.308 2.692-6 6-6s6 2.692 6 6c0 3.31-2.692 6-6 6zm0-14c-4.418 0-8 3.582-8 8s3.582 8 8 8 8-3.582 8-8-3.582-8-8-8z" fill="#aeb1b5" fill-rule="evenodd"/></svg>
            <input type="text"
                id="l-search"
                v-model="search"
                @keydown.esc="escape"
                @keydown.down.prevent="down"
                @keydown.up.prevent="up"
                @keydown.enter.prevent="enter"
                :placeholder="text.search"
                ref="input"
                >
            <span class="addon l-loading" v-show="loading"></span>
        </p>
        <ul>
            <li v-for="(item, i) in list" :class="{ 'is-active' : index == i }" key="item.id"  @click="select(i)" v-on:mouseover="over(i)">
                <div>{{ item[field] }}</div>
            </li>
            <li v-if="!count">
                <div class="no-results">
                    {{ text.no_resuts }}
                </div>
            </li>

            <li class="create" v-show="!search">
                <div>{{ text.typeText }}</div>
            </li>
            <li class="create" v-show="search && withcreate" :class="{ 'is-active' : index == count }" @click="select('create')">
                <div class="">
                    {{ text.create }} {{ name }} <strong>{{ search }}</strong>
                </div>
            </li>
        </ul>
    </div>
</template>
<style lang="sass?indentedSyntax">
    $font-size: 14px
    $grey-darker: #222324
    $grey-dark: #69707a
    $grey: #aeb1b5
    $grey-light: #d3d6db
    $grey-lighter: #f5f7fa
    $min-width: 50px
    $max-width: 300px

    $base-line-height: 16px;
    $line-width: 4px
    $white: rgb(0,0,0);
    $off-white: rgba($white, 0.5);
    $spin-duration: 1s;
    $pulse-duration: 750ms;

    .l-control
        border: 1px solid $grey-light
        display: flex
        align-items: center
        position: relative
        margin: 4px
        .addon
            margin: 0 5px
            color: $grey
        input
            border: 0
            color: $grey-dark
            font-size: $font-size
            flex: 1
            padding: 5px 10px 5px 5px
            &:focus
                outline: none

    .l-dropdown
        background: #fff
        position: absolute
        z-index: 10
        left: 0
        top: 100%
        display: inline-block
        min-width: $min-width
        max-width: $max-width
        border: 1px solid $grey-light
        border-radius: 2px
        box-shadow: 0 0 4px $grey-light
        animation: slideBoxDown .5s
        transform: translateY(0)
        opacity: 1
        transition: all .5s ease


        ul
            font-size: $font-size
            list-style-type: none
            padding: 0
            margin: 0

            li
                cursor: pointer
                line-height: 1.5
                padding: 5px 10px

                &.no-results
                    color: $grey-light
                &.create
                    border-top: 1px solid $grey-lighter
                &.is-active
                    background: $grey-lighter
                    div
                        font-weight: 500



    .l-loading
        position: absolute
        right: $base-line-height / 2
        width: ($base-line-height / 4)
        height: $base-line-height
        background: $grey-light
        animation: pulse $pulse-duration infinite
        animation-delay: ($pulse-duration / 3)
        &:before, &:after
            content: ''
            position: absolute
            display: block
            height: ($base-line-height / 1.5)
            width: ($base-line-height / 4)
            background: $grey-light
            top: 50%
            transform: translateY(-50%)
            animation: pulse $pulse-duration infinite

        &:before
            left: -($base-line-height / 2)

        &:after
            left: ($base-line-height / 2)
            animation-delay: ($pulse-duration / 1.5)

    @keyframes pulse
        50%
            background: $grey-lighter

    @keyframes slideBoxDown
        0%
            transform: translateY(-100%)
            opacity: 0
        100%
            transform: translateY(0)
            opacity: 1


</style>
