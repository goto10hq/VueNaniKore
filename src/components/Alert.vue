<template>
  <div role="alert" :class="alertClass" :id="id" v-show="isVisible">
    <button type="button" class="close" aria-label="Close" v-if="closable" @click="closeAlert">
      <span aria-hidden="true"><slot name="close">&times;</slot></span>
    </button>    
    <slot></slot>
    <span v-html="errorMessage"></span>    
  </div>
</template>
<script>    
  export default {
    props: {
        id: {
            type: String,
            default: function () {
                return 'alert-id-' + this._uid
            },
        },
        closable: {
            type: Boolean,
            default: false
        },
        duration: {
            type: Number,
            default: 0
        },
        type: {
            type: String,
            default: 'success'
        },
        className: {
            type: String,
            default: null
        },
        errors: { },        
    },
    data () {
        return {
            timeout: 0
        }
    },
    computed: {
        alertClass () {
            if (this.className != null) {
                return this.className;
            }
            return `alert alert-${this.type}`;
        },
        isVisible() {
            if (this.errorMessage != null) {
                return true;
            }

            if (this.$slots.default != null && this.$slots.default != '') {
                return true;
            }

            return false;
        },
        errorMessage() {               
            if (this.errors == null || this.errors.length == 0) {
                return null;
            }            

            if (this.errors.hasOwnProperty('message') &&
                this.errors.hasOwnProperty('errors'))
            {       
                if ((this.errors.errors == null || this.errors.errors.length == 0) &&
                    (this.errors.message == null || this.errors.message == '')) {
                    return null;
                }

                let result = "";

                if (this.errors.message != null && this.errors.message != '') {
                    result += "<strong>" + this.errors.message + "</strong><br />";
                }

                if (this.errors.errors != null && this.errors.errors.length != 0) {
                    result += this.errors.errors.map((x) => { return x.message }).join('<br />');
                }

                return result; 
            }
            else if (this.errors.hasOwnProperty('errors'))
            {                
                if (this.errors.hasOwnProperty('isValid') &&
                    (this.errors.isValid == undefined || this.errors.isValid != false)) {
                    return null;
                }
                             
                if (this.errors.errors == null || this.errors.errors.length == 0) {
                    return null;
                }

                return this.errors.errors.map((x) => { return x.value }).join('<br />');
            }

            return null;
        }
    },
    methods: {
        closeAlert () {
            clearTimeout(this.timeout);
            this.$emit('close');
        }
    },
    mounted () {
        if (this.duration > 0) {
            this.timeout = setTimeout(this.closeAlert, this.duration);
        }
    },
    destroyed () {
        clearTimeout(this.timeout);
    }
  }
</script>