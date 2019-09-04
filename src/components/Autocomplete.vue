<template>
    <div class="autocomplete" :id="'autocomplete-'+idAutocomplete">
        <div class="input" @click="toggleVisible" v-text="selectedItem ? selectedItem[filterby] : ''" ></div>
        <div class="placeholder" v-if="selectedItem == null" v-text="title"></div>
        <button class="close" @click="selectedItem = null" v-if="selectedItem"><i class="fas fa-times-circle"></i></button>
        <div v-show="visible" class="popover">
            <b-form-input size="sm" ref="input" type="text" v-model="query" @keydown.up="up" 
            @keydown.down="down" @keydown.enter="selectItem" placeholder="Digite algo..."></b-form-input>
            <div class="options" ref="optionsList">
                <ul>
                    <li :class="{'selected': (selected == index)}" v-for="(match, index) in matches" :key="index" v-text="match[filterby]" @click="itemClicked(index)">

                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    // props: ['items','filterby', 'title', 'shouldReset'],
    props: {
        items: {
            default: [],
            type: Array
        },
        filterby: {
            type: String
        },
        title: {
            default: 'Selecione...',
            type: String
        },
        shouldReset: {
            type: Boolean,
            default: true
        },
        idAutocomplete: {
            type: Number
        }
    },
    name: 'Autocomplete',
    data(){
        return {
            itemHeight: 39,
            query: '',
            visible: false,
            selected: 0,
            selectedItem: null,
        }
    },
    mounted(){
        this.$nextTick(() => {
            window.addEventListener("click", (e) => {
                const elements = e.path.map(e => e.id);
                if(elements.includes('autocomplete-'+this.idAutocomplete)){
                    return;
                }
                this.visible = false;
            });
        });
    },
    methods: {
        toggleVisible(){
            this.visible = !this.visible;
            setTimeout(() => {
                this.$refs.input.focus();
            }, 50);
        },
        itemClicked(index){
            this.selected = index;
            this.selectItem();
        },
        selectItem(){
            if(!this.matches.length){
                return;
            }
            this.selectedItem = this.matches[this.selected];
            this.visible = false;

            if(this.shouldReset){
                this.query = '';
                this.selected = 0;
            }
            this.$emit('selected', this.selectedItem);
        },
        up(){
            if(this.selected == 0){
                return;
            }
            this.selected -= 1;
            this.scrollToItem();
        },
        down(){
            if(this.selected > this.matches.length -2){
                return;
            }
            this.selected += 1;
            this.scrollToItem();
        },
        scrollToItem(){
            this.$refs.optionsList.scrollTop = this.selected * this.itemHeight;
        },
    },
    computed: {
        matches() {
            this.$emit('change', this.query);
            if(this.query == ''){
                return [];
            }
            return this.items.filter((item) => item[this.filterby].toLowerCase().includes(this.query));
        }
    }
}
</script>

<style scoped>
    .autocomplete{
        width: 100%;
        position: relative;
    }

    .input{
        height: 35px;
        border-radius: 3px;
        border: 2px solid lightgray;
        box-shadow: 0 0 10px #eceaea;
        font-size: 22px;
        padding-left: 10px;
        cursor: text;
    }

    .close {
        font-size:22px;
        color:#e74c3c;
        position: absolute;
        right: 2px;
        top: 7px;
        background: none;
        border: none;
        cursor: pointer;
    }
    
    .close i:hover{
        color: #c0392b;
    }

    

    .placeholder{
        position: absolute;
        top:2px;
        left: 11px;
        font-size:20px;
        color: #d0d0d0;
        pointer-events: none;
    }

    .popover{
        min-height: 50px;
        border: 2px solid lightgray;
        position: absolute;
        top:34px;
        left: 0;
        right: 0;
        max-width: 100%;
        background: #fff;
        border-radius: 3px;
        text-align: center;
    }

    .popover input {
        width: 98%;
        margin-top: 5px;
        font-size: 16px;
        border-radius: 3px;
        border: 1px solid lightgray;
        margin: 8px auto;
    }
    
    .options {
        max-height: 150px;
        overflow-y: scroll;
        margin-top: 5px;
    }

    .options ul {
        list-style-type: none;
        text-align: left;
        padding-left: 0;
    }

    .options ul li {
        border-bottom: 1px solid lightgray;
        padding: 10px;
        cursor: pointer;
        background: #f1f1f1;
    }

    .options ul li:first-child {
        border-top: 2px solid #d6d6d6;
    }
    .options ul li:not(.selected):hover {
        background: #8c8c8c;
        color: #fff;
    }

    .options ul li.selected {
        background: #3498db;
        color: #fff;
        font-weight: 600;
    }


    ::-webkit-scrollbar {
    width: 8px;
    }
    ::-webkit-scrollbar-thumb {
    -webkit-border-radius: 10px;
    border-radius: 10px;
    background-color: #2980b9;
    }
</style>