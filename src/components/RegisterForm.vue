<template>
    <div class="form">
        <div class="form-group">
            <label for="nombre">Nombre:</label>
            <input type="text" class="form-control" id="nombre" aria-describedby="nombreHelp"
                placeholder="Digite nombres y apellidos" v-model="state.nombre" @input="checkNombre()">
            <small id="nombreHelp" class="form-text" v-if="v$.nombre.$error" style="color: red;">
                {{ v$.nombre.$errors[0].$message }}
            </small>
        </div>
        <div class="form-group">
            <label for="tipoDoc">Tipo de Documento</label>
            <select class="form-select" aria-label="Default select example" v-model="state.tipo_documento">
                <option selected value="C.C">C.C</option>
                <option value="C.E">C.E</option>
                <option value="Pasaporte">Pasaporte</option>
            </select>
        </div>
        <div class="form-group">
            <label for="numeroDoc">Número de Documento:</label>
            <input type="text" class="form-control" id="nombre" aria-describedby="docHelp"
                placeholder="Digite número de documento" v-model="state.documento" @input="checkDocumento()">
            <small id="docHelp" class="form-text" v-if="v$.documento.$error" style="color: red;">
                {{ v$.documento.$errors[0].$message }}
            </small>

        </div>
        <div class="form-group">
            <label for="numeroDoc">Teléfono móvil:</label>
            <input type="number" class="form-control" id="nombre" aria-describedby="telHelp"
                placeholder="Digite Teléfono móvil" v-model="state.telefono" @input="checkTelefono()">
            <small id="telHelp" class="form-text" v-if="v$.telefono.$error" style="color: red;">
                {{ v$.telefono.$errors[0].$message }}
            </small>
        </div>
        <div class="form-group">
            <label for="exampleInputEmail1">Email</label>
            <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp"
                placeholder="Digite email" v-model="state.correo" @input="checkCorreo()">
            <small id="telHelp" class="form-text" v-if="v$.correo.$error" style="color: red;">
                {{ v$.correo.$errors[0].$message }}
            </small>
        </div>

        <div class="form-check">
            <input type="checkbox" class="form-check-input" id="habilitadoCheck" v-model="state.activo">
            <label class="form-check-label" for="habilitadoCheck">Habilitado</label>
        </div>
        <button v-if="!update" class="btn btn-primary" @click="submitForm">Submit</button>
    </div>
</template>

<script>
import useValidate from '@vuelidate/core'
import { required, email, maxLength } from '@vuelidate/validators'
import { reactive, computed } from 'vue'

export default {
    props: {
        id_usuario:{
            type: Number,
            default: 0
        },
        correo: {
            type: String,
            default: ''
        },
        nombre: {
            type: String,
            default: ''
        },
        tipo_documento: {
            type: String,
            default: 'C.C'
        },
        documento: {
            type: String,
            default: ''
        },
        telefono: {
            type: String,
            default: ''
        },
        activo: {
            type: Boolean,
            default: false
        },
        update: {
            type: Boolean,
            default: false
        },
    },
    setup(props) {
        console.log(props)
        const state = reactive({
            correo: props.correo,
            nombre: props.nombre,
            tipo_documento: props.tipo_documento,
            documento: props.documento,
            telefono: props.telefono,
            activo: props.activo
        })

        const rules = computed(() => {
            return {
                correo: { required, email },
                nombre: { required, maxLength: maxLength(100) },
                tipo_documento: { required },
                documento: { required, maxLength: maxLength(30) },
                telefono: { required, maxLength: maxLength(10) }
            }
        })

        const v$ = useValidate(rules, state)
        return { state, v$ }
    },

    methods: {
        submitForm() {
            this.v$.$validate()
            console.log(this.v$)
            if (!this.v$.$error) {
                if (!this.update) {
                    let datos = {
                        nombre: this.state.nombre,
                        tipo_documento: this.state.tipo_documento,
                        documento: this.state.documento,
                        telefono: this.state.telefono,
                        correo: this.state.correo,
                        activo: this.state.activo
                    };

                    const envia = {
                        method: "POST",
                        body: JSON.stringify(datos),
                        headers: new Headers({
                            "Content-Type": "application/json",
                            Accept: "*/*",
                            "Access-Control-Allow-Origin": "*",
                            mode: "no-cors",
                        }),
                        redirect: "follow",
                    };


                    fetch(`https://nice-puce-chick-slip.cyclic.app/asistentes`, envia).then((response) => {
                        if (response.ok) {
                            alert("Usuario registrado correctamente")
                            location.reload()
                        } else {
                            alert("Error al registrar al Usuario")
                        }
                    }).catch((err) => {
                        alert("No se ha podido realizar la petición")
                    });
                } else {
                    let datos = {
                        nombre: this.state.nombre,
                        tipo_documento: this.state.tipo_documento,
                        documento: this.state.documento,
                        telefono: this.state.telefono,
                        correo: this.state.correo,
                        activo: this.state.activo
                    };
                    
                    const envia = {
                        method: "PUT",
                        body: JSON.stringify(datos),
                        headers: new Headers({
                            "Content-Type": "application/json",
                            Accept: "*/*",
                            "Access-Control-Allow-Origin": "*",
                            mode: "no-cors",
                        }),
                        redirect: "follow",
                    };
                    fetch(`https://nice-puce-chick-slip.cyclic.app/asistentes/${this.id_usuario}`, envia).then((response) => {
                        if (response.ok) {
                            alert("Usuario actualizado correctamente")
                            location.reload();
                        } else {
                            alert("Error al actualizar el Usuario")
                        }
                    }).catch((err) => {
                        alert("No se ha podido realizar la petición")
                    });
                }
            } else {
                alert('Form failed validation')
            }
        },
        checkNombre() {
            this.v$.nombre.$touch()
        },
        checkDocumento() {
            this.v$.documento.$touch()
        },
        checkTelefono() {
            this.v$.telefono.$touch()
        },
        checkCorreo() {
            this.v$.correo.$touch()
        }
    },

    validations() {
        return {
            correo: { required, email },
            nombre: { required, maxLength: maxLength(100) },
            tipo_documento: { required },
            documento: { required, maxLength: maxLength(30) },
            telefono: { required, maxLength: maxLength(10) }
        }
    },
}
</script>

<style scoped>
.form {
    padding: 50px;
}

.form-group,
.form-check {
    margin-bottom: 10px;
}
</style>