<template>
    <div>
        <header>
            <div>
                <h2>Product Add</h2>
            </div>
             <div class="btns-header">                
                <button class="btn" @click="addProduct()" type="Submit">Save</button>

                <button class="btn" @click="back()">Cancel</button>
            </div>
        </header>
        <hr>
        <div class="errors">
            <span v-for="error in errors" :key="error.id">{{error}}<br></span>
        </div>
        <div class="form" >
            <form action="" id="product_form" @submit.prevent="addProduct()">
                <div>
                    <div class="inp_form">
                        <label for="sku">SKU</label>
                        <input type="text" id="sku"  placeholder="#sku" v-model="sku" >
                    </div>
                    <div class="inp_form">
                        <label for="name">Name</label>
                        <input type="text" id="name" placeholder="#name" v-model="name" >
                    </div>
                    <div class="inp_form">
                        <label for="price">Price ($)</label>
                        <input type="text" id="price" placeholder="#price" v-model="price" >
                    </div>
                </div>


                <div>
                    <select v-model="type" name="type" id="productType" @change="getAttributes($event)" >
                            <option disabled selected>type</option>
                            <option :key="item.id" v-for="item in types" :value="item.id" :id="item.name" >{{item.name}}</option>
                    </select>
                </div>

                <div  class="inp_form" v-for="attribute in attributes" :key="attribute.id" :id="attribute.id">
                    <label :for="attribute.name" >{{attribute.name}} ({{attribute.unit}}) </label> 
                    <input :id="attribute.name"  class="input-field" name="field1" type="text"   v-model="attribute.value">
                </div>
                <p>{{type.description}}</p>

            </form>
        </div>
    </div>
</template>



<script>
import axios from 'axios';

export default {
    name: "AddProduct",
    data(){
        return {
            name:'',
            sku:'',
            price: '',
           
            type: 'type',
            errors: [],
            attributes: [],
            type_value: '',
            types: [],
            err: []
            }
            
        },
    methods: { 
            back(){
                    this.$router.push("/");
                },
            addProduct(){
                    var data = new FormData();
                    data.append("sku", this.sku);
                    data.append("name", this.name);
                    data.append("price", this.price);
                    data.append("type", this.type_value);
                    data.append("attributes", JSON.stringify(this.attributes));
                    for (const key in this.attributes) {
                            data.append(this.attributes[key].name,this.attributes[key].value);
                        }
                    if(this.attributes.length == 0){
                        this.errors = [];
                         this.errors.push("pleace select type");
                    }else
                    axios
                    .post('https://tight-laced-damages.000webhostapp.com/back_end/public/home/addProduct/',data)
                    .then(res => { 
                        console.log(res.data.errors)
                        if(res.data.errors){
                            this.errors = res.data.errors;
                            //comment
                        }else{
                                this.back();
                        }
                         
                    })
                    .catch(err => (this.err = err.data))
                   
 

            }
            ,getAttributes(event){
                this.type_value = event.target.options[event.target.options.selectedIndex].text;
                let type_id = event.target.value;
                axios
                .get('https://tight-laced-damages.000webhostapp.com/back_end/public/home/getAttributes/?type_id='+type_id)
                .then(res =>{
                    (this.attributes = res.data)
                    })
                .catch(err => {console.log("Error", err);});
                    
            }
          

    },mounted()
    {        
                axios
                .get('https://tight-laced-damages.000webhostapp.com/back_end/public/home/getTypes')
                .then(res => {
                    this.types = res.data;
                })
                .catch(err => {console.log("Error", err);});

          
        

    }
}
</script>



<style scoped>

header{
    margin-top: 50px;
    height: 40px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}
.btns-header {
    margin-right: 50px;
    width: 20%;
    display: flex;
    justify-content: space-around;
}
header h2 {
    margin-left: 20px; 
}
.btn {
    color: #1e3141;
    border: 2px solid black;
    background-color: #fff;
    padding: 5px;
    border-radius: 4px;
    box-shadow: 1px 1px black;
    transition-duration: .3s;
    transition-property: all;
    transition-timing-function: ease-in-out;
    cursor: pointer;
}
.btn a {
    text-decoration: none;
    color: #fff;
}

form {
    display: flex;
    align-items: center;
    flex-direction: column;
    
}
form div {
    margin: 20px 0;
    display: flex;
    flex-direction: column;
}
form input,select {
    margin-bottom: 5px;
    padding: 4px;
    border-radius: 5px;
    border: 1px solid  #1e3141;
}

.errors {
    position:absolute;
    margin: 25px 160px;
    color: red;
} 
.inp_form {
    display: flex;
    flex-direction: row;
    align-items: center;
    /* background-color: green; */
    justify-content: space-between;
}



</style>