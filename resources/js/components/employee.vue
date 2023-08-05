<template>
    <div class="container p-5">

        <!-- Button trigger modal -->
        <button type="button" class="btn btn-primary float-right" data-toggle="modal" data-target="#myModal">
            + Employee
        </button>

        <!-- Modal -->
        <div class="modal fade" id="myModal" tabindex="-1" role="dialog"
            aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLongTitle">Add Employee</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <div class="text-sm text-danger" if="errors != ''">
                            <p v-for="error in errors" :key="error"><small>{{ error }}</small></p>
                        </div>
                        <div class="form-group">
                            <label for="">Name</label>
                            <input type="text" v-model="form.name" class="form-control">
                        </div>
                        <div class="form-group">
                            <label for="">Email</label>
                            <input type="email" v-model="form.email" class="form-control">
                        </div>
                        <div class="form-group">
                            <label for="">Phone</label>
                            <input type="text" v-model="form.phone" class="form-control">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button type="button" @click="storeEmployee" class="btn btn-primary">Save changes</button>
                    </div>
                </div>
            </div>
        </div>

        <table class="table table-border table-hover">
            <thead>
                <th>Name</th>
                <th>Email</th>
                <th>Phone</th>
                <th>Action</th>
            </thead>
            <tbody>
                <template v-for="employee in employees" :key="employee.id">
                    <tr>
                        <td>{{ employee.name }}</td>
                        <td>{{ employee.email }}</td>
                        <td>{{ employee.phone }}</td>
                        <td>Edit</td>
                    </tr>
                </template>
            </tbody>
        </table>
    </div>
</template>
<script>
import { ref, onMounted, reactive } from 'vue';
import axois from 'axios';

export default {
    setup() {
        const employees = ref([]);
        const errors = ref([]);

        const form = reactive({
            name: '',
            email: '',
            phone: ''
        });

        const getEmployees = async () => {
            let res = await axios.get('api/employees');
            employees.value = res.data;
        }

        const storeEmployee = async() => {
            try{
                await axois.post('api/employees', form);
                getEmployees();
                $("#myModal").modal('hide');
            }catch(e){
                if(e.response.status == 422){
                    var data = [];
                    for(const key in e.response.data.errors){
                        data.push(e.response.errors[key][0]);
                    }
                    errors.value = data;
                }
            }
        }

        onMounted(getEmployees());
        return {
            employees,
            form,
            storeEmployee,
            errors
        }
    }
}
</script>
