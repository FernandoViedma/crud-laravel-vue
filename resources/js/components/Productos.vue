<template>
    <div class="container">

        <h3>Añadir productos</h3>
        <form @submit.prevent="añadir()">
            <input type="text" placeholder="Nombre" v-model="producto.nombre" class="form-control mb-2">
            <input type="text" placeholder="Descripción" v-model="producto.descripcion" class="form-control mb-2">
            <input type="text" placeholder="Precio" v-model="producto.precio" class="form-control mb-2">
            <button class="btn btn-primary">Añadir producto</button>
        </form>

        <hr>
        <h3>Lista de productos</h3>
        <ul class="list-group m-y">
            <li class="list-group-item" v-for="(item, index) in productos" :key="index">
                <p>Id: {{item.id}}</p>
                <p>Nombre: {{item.name}}</p>
                <p>Descripción: {{item.description}}</p>
                <p>Precio: {{item.price}}</p>
                <p>Fecha creación: {{item.created_at}}</p>
                <p>Fecha actualización: {{item.updated_at}}</p>
                <button class="btn btn-danger" @click="eliminarProducto(item,index)">Eliminar</button>
                <button class="btn btn-warning" @click="editarFormulario(item)">Editar</button>
            </li>
        </ul>

        <hr>

        <form @submit.prevent="editarProducto(producto)" v-if="editarActivo"> 
            <h3>Editar producto</h3>
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="producto.nombre">
            <input type="text" placeholder="Descripción" class="form-control mb-2" v-model="producto.descripcion">
            <input type="text" placeholder="Precio" class="form-control mb-2" v-model="producto.precio">
            <button type="submit" class="btn btn-success">Guardar</button>
            <button class="btn btn-danger" type="submit" @click="terminarEdicion()">Finalizar edición</button>
        </form>

    </div>
</template>

<script>
    export default {
        data(){
            return {
                productos: [],
                producto: { nombre: '', descripcion: '', precio: ''},
                editarActivo: false
            }
        },
        created(){
            axios.get('/productos').then(res=>{
            this.productos = res.data;
        })
        },
        methods:{
            añadir(){
                if(this.producto.nombre.trim() === '' || this.producto.precio.trim() === ''){
                    alert('Debes completar los campos nombre y precio');
                    return;
                }else{
                    if(isNaN(this.producto.precio))
                    {
                    alert('El precio debe ser un número');
                    return;
                    }
                }
                console.log(this.producto.nombre, this.producto.descripcion, this.producto.precio);
                const params = {nombre: this.producto.nombre, descripcion: this.producto.descripcion, precio: this.producto.precio};
                console.log(params);
                axios.post('/productos', params)
                    .then(res =>{
                        this.productos.push(res.data);  
                })
            },
            eliminarProducto(item,index){
                axios.delete(`/productos/${item.id}`)
                    .then(()=>{
                        this.productos.splice(index, 1);
                })
            },
            editarFormulario(item){
                this.editarActivo = true;
                this.producto.nombre = item.name;
                this.producto.descripcion = item.description;
                this.producto.precio = item.price;
                this.producto.id = item.id;

            },
            editarProducto(producto){
                axios.put(`/productos/${producto.id}`, producto)
                    .then(res=>{
                        const index = this.productos.findIndex(item => item.id === producto.id);
                        this.productos[index] = res.data;
                    })
            },
            terminarEdicion(){
                this.editarActivo = false;
                this.producto = {nombre: '', descripcion: '', precio: ''};
            }
        }
    }
</script>
