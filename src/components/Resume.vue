<template>
    <table class="table">
        <thead>
            <tr>
                <th scope="col">Total Asistentes</th>
                <th scope="col">Habilitados</th>
                <th scope="col">No Habilitados</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <th>{{ datos.total }}</th>
                <th>{{ datos.countA }}</th>
                <th>{{ datos.countN }}</th>
            </tr> 
        </tbody>
    </table>
</template>

<script>
import { isProxy, toRaw } from 'vue';
import RegisterForm from '../components/RegisterForm.vue';

export default {
    data() {
        return {
            datos: {}
        };
    },
    created() {
        fetch(`https://nice-puce-chick-slip.cyclic.app/asistentes/count`, {
            method: "get",
            headers: new Headers({
                "Content-Type": "application/x-www-form-urlencoded",
            }),
        }).then((response) => {
            return response.json();
        }).then((response2) => {
            // if (isProxy(someData)) {
            //     this.clientes = toRaw(response2)
            // }else{
            //     this.clientes = response2;
            // }
            console.log(response2);
            this.datos = response2;
        }).catch((err) => {
            alert("No se ha podido realizar la petición");
        });
       
    }
}

</script>