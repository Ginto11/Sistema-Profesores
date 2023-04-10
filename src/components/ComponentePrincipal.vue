<template>

    <header>
        <div class="contenedor-img">
            <img src="../assets/logo.png" alt="">
        </div>
        <h1>Sistema de control Profesores</h1>
    </header>

    <button class="btn-form" :class="(verForm == true) ? 'cancelar' : 'registrar'" @click="verFormulario"> {{ tituloBtn }} </button>

    <form v-if="verForm === true">
        <h2>Formulario de Registro</h2>
        <label for="nombre"> Nombre <br>
            <input type="text" name="nombre" v-model="nombre"> 
        </label>
        <br>
        <label for="apellido"> Apellido <br>
            <input type="text" name="apellido" v-model="apellido"> 
        </label>
        <br>
        <label for="edad"> Edad <br>
            <input type="text" name="edad" v-model="edad"> 
        </label>
        <br>
        <label for="modalidad">
            <select name="modalidad" v-model="modalidad">
                <option value="" selected>Seccione la modalidad</option>
                <option value="Presencial">Presencial</option>
                <option value="Virtual">Virtual</option>
            </select>
        </label>
        <br>
        <label for="materias">
            Conocimientos en: <br>
            <input type="text" name="materias" v-model="materiaNueva"><br>
            <button class="btn-form btn-form-agregar-materia registrar" @click="agregarMateria($event)">Agregar Materia</button> 
        </label>
        <br>
        <label for="lista">
            <ul v-if="materias.length > 0">
                <li v-for="materia in materias" :key="materia">{{ materia }}</li>
            </ul>
        </label>
        <label for="check" style="display: flex; flex-direction: row;">
            <input type="checkbox" name="check" v-model="documentacion"> Entregó la documentación para el registro.
        </label>
        <br>
        <label for="registrar">
            <input class="btn-form registrar" @click="registrarProfesor($event)" type="submit" value="Registrar">
        </label>
        <p :style="validacionCamposColores()">{{ validacionCamposMensaje }}</p>
    </form>

    <h2 v-if="profesoresRegistrados.length > 0">Profesores Registrados</h2>
    <table v-if="profesoresRegistrados.length > 0">
        <thead>
            <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Apellido</th>
                <th>Edad</th>
                <th>Modalidad</th>
                <th>Materias</th>
                <th>Documentación</th>
                <th>Acción</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(profesor) in profesoresRegistrados" :key="profesor.id">
                <td>{{ profesor.id }}</td>
                <td>{{ profesor.nombre }}</td>
                <td>{{ profesor.apellido }}</td>
                <td>{{ profesor.edad }}</td>
                <td>{{ profesor.modalidad }}</td>
                <td>
                    <p v-for="materia in profesor.materias" :key="materia">{{ materia }}</p>
                </td>
                <td :style="validarDocumentacion(profesor)">{{ (profesor.documentacion) ? "Si" : "No" }}</td>
                <td>
                    <button @click="eliminar($event)" :key="profesor.id" class="btn-form" :style="{backgroundColor: '#CD4450'}">Eliminar</button>
                </td>
            </tr>
        </tbody>
    </table>

</template>

<script lang="js">

    import { defineComponent } from "vue";
    import { ref } from "vue";

    class Profesor {

        static id = 0;

        constructor(nombre, apellido, edad, modalidad, materias, documentacion){
            this.nombre = nombre;
            this.apellido = apellido;
            this.edad = edad
            this.materias = materias;
            this.modalidad = modalidad;
            this.documentacion = documentacion;
            this.id = Profesor.id;
            Profesor.id++;
        }
    }

    export default defineComponent({
        name: "ComponentePrincipal",
        setup(){
            return{
                validacionCamposBoolean: ref(false),
                validacionCamposMensaje: ref(""),
                tituloBtn: ref("Registrar Profesor"),
                verForm: ref(false),
                materiaNueva: "",
                nombre: ref(""),
                apellido: ref(""),
                materias: ref([]),
                modalidad: ref(""),
                edad: ref(""),
                documentacion: ref(false),
                registrar: ref(false),
                profesoresRegistrados: ref([ 
                    new Profesor("Carlos", "Gomez", 33, "Virtual", ["Español"], true), 
                    new Profesor("Yilen", "Sarmiento", 40, "Presencial", ["Matematicas", "Ingles"], false), 
                    new Profesor("Karen", "Gonzales", 25, "Presencial", ["Fisica"], true), 
                    new Profesor("Camila", "Herrera", 27, "Virtual", ["Filosofia", "Sociales", "Ciencias"], true), 
                    new Profesor("Andres", "Muñoz", 36, "Presencial", ["Programación"], false) ].reverse())
            }
        },
        methods: {
            verFormulario(){
                if(!this.verForm){
                    this.tituloBtn = "Cancelar Registro"
                    return this.verForm = true;
                }
                this.tituloBtn = "Registrar Profesor"
                this.verForm = false;
            },

            agregarMateria($event){
                $event.preventDefault();

                if(this.materiaNueva == ""){
                    this.validacionCamposBoolean = false;
                    return this.validacionCamposMensaje = "Escribe la materia";
                }
                this.materias.push(this.materiaNueva);
                this.materiaNueva = "";
            },

            registrarProfesor($event){
                $event.preventDefault();
                this.validacionCamposF();
                if(this.validacionCamposBoolean == false) return;

                const profesor = new Profesor(this.nombre, this.apellido, this.edad, this.modalidad, this.materias, this.documentacion)

                this.profesoresRegistrados.unshift(profesor)

                this.materias = [];
                this.nombre = "";
                this.apellido = "";
                this.documentacion = false;
                this.modalidad = "";
                this.edad = "";

            },

            validarDocumentacion(profesor){
                if(profesor.documentacion == true) return {color: "green", fontWeight: "bold"};
                return {color: "red", fontWeight: "bold"};
            },

            validacionCamposF(){
                if(this.nombre == "" || this.apellido == "" || this.materias.length == 0 || this.edad == "" || this.modalidad == ""){
                    this.validacionCamposMensaje = "Faltan datos para el registro.";
                    this.validacionCamposBoolean = false;
                    return;
                }

                this.validacionCamposMensaje = "Registro Existoso";
                this.validacionCamposBoolean = true;

            }, 

            validacionCamposColores(){
                if(this.validacionCamposBoolean == false){
                    return {color: "red", fontWeight: "bold"};
                }

                return {color: "green", fontWeight: "bold"};
            },

            eliminar($event){
                let id = $event.target.parentElement.parentElement.cells[0].textContent;

                this.profesoresRegistrados.forEach((profesor, index) => {
                    if(profesor.id == id){
                        this.profesoresRegistrados.splice(index, 1);
                    }
                })
            }
        }
    })

</script>

<style scoped>
    * {
        font-family: Calibri;
        user-select: none;
    }

    h1{
        text-shadow: 0 0 3px #000;
    }


    form {
        display: flex;
        flex-direction: column;
        align-items: center;
        background-color: #011C27;
        width: 30%;
        display: flex;
        flex-direction: column;
        border-radius: 10px;
        margin-top: 15px;
        box-shadow: 0 0 10px #000;
    }

    form select{
        width: 92%;
        height: 25px;
        border-radius: 5px;
        outline: none;
        border-color: transparent;
        transition: .4s ease all;
        box-shadow: 0 0 5px #000;
        font-weight: bold;
        text-align: center;
        cursor: pointer;
    }

    form label{
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: left;
    }
    form label[for="lista"]{
        display: flex;
        flex-direction: row;
        align-items: flex-start;
        justify-content: space-around;
    }

    form label[for="lista"] ul{
        background-color: #1A5C6C;
        text-align: left;
        width: 80%;
        border-radius: 10px;
    }

    table{
        border: 2px solid #000;
        min-width: 100%;
        text-align: center;
        border-radius: 3px;
        background-color: #011C27;
        box-shadow:  0 0 10px #000;
    }

    table thead th{
        border: 1px solid #000;
        border-radius: 3px;
        background-color: #1A5C6C;
        color: #59EBCB;
    }

    table tbody td{
        border-color: transparent;
        background-color: #34495E;
        border-radius: 3px;
        color: #DADAD9;
    }

    .btn-form{
        height: 33px;
        width: 120px;
        border-radius: 5px;
        border-color: transparent;
        outline: none;
        cursor: pointer;
        transition: .4s ease all;
        border: 3px solid transparent;
        background-color: #008080;
        font-weight: bold;
        box-shadow: 0 0 5px #000;
    }

    .btn-form-agregar-materia{
        height: 30px;
    }

    .btn-form:hover, .btn-form-agregar-materia:hover{
        border-color: #000;
    }

    form input[type="text"]{
        background-color: #fff;
        height: 20px;
        width: 90%;
        border-radius: 5px;
        outline: none;
        border-color: transparent;
        transition: .4s ease all;
        box-shadow: 0 0 5px #000;
        font-weight: bold;
        text-align: center;
    }

    form input[type="text"]:focus{
        border-color: #34495E;
    }

    form input[type="ckeckbox"]{
        cursor: pointer;
    }

    .registrar {
        background-color: #25D366;
    }

    .cancelar{
        background-color: #CD4450;
    }

    header {
        display: flex;
    }
    .contenedor-img{
        width: 70px;
    }

    .contenedor-img img{
        width: 100%;
        filter: drop-shadow(0 0 3px #25D366);
    }

</style>