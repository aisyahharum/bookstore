<template>
    <div>
        <v-subheader>Your Profile</v-subheader>
        <v-card flat>
            <v-container>
                <v-simple-table>
                    <tbody>
                        <tr v-for="(value,key) in user" :key="key">
                            <td>{{ key }}</td>
                            <td>{{ value }}</td>
                        </tr>
                    </tbody>
                </v-simple-table>
            </v-container>
        </v-card>

        <template class="mb-5">
            <v-row>
                <v-col cols="auto">
                <v-dialog
                    transition="dialog-bottom-transition"
                    max-width="600"
                    v-model="dialogModal"
                >
                    <template v-slot:activator="{ on, attrs }">
                    <v-btn
                        color="primary"
                        v-bind="attrs"
                        v-on="on"
                        @click="dialogModal = true"
                        class="mb-5 ml-5"
                    >Update Profile</v-btn>
                    </template>

                    <template v-slot:default="dialog">
                    <v-card>
                        <v-toolbar
                        color="dark"
                        dark
                        >Update Profile</v-toolbar>
                        <v-card-text>

                        <div class="">
                            <template>
                            <v-form
                                ref="form"
                                lazy-validation
                            >
                                
                                <v-text-field v-model="oldname" :rules="nameRules" :counter="255" label="Name" required append-icon="person"></v-text-field>
                                <v-text-field v-model="email" :rules="emailRules" label="E-mail" required append-icon="email"></v-text-field>
                                <v-text-field v-model="password" :append-icon="showPassword ? 'visibility' : 'visibility_off'" :rules="passwordRules" :type="showPassword ? 'text' : 'password'" label="Masukan Password Anda" hint="At least 6 characters" counter @click:append="showPassword = !showPassword"></v-text-field>
                            
                               
                            </v-form>
                            </template>
                        </div>

                        </v-card-text>
                        <v-card-actions class="justify-end">
                        <v-btn
                            text
                            @click="dialog.value = false"
                        >Close</v-btn>

                        <v-btn
                            text
                            @click="submit"
                        >Submit</v-btn>

                        </v-card-actions>
                    </v-card>
                    </template>
                </v-dialog>
                </v-col>
            </v-row>
        </template>
    </div>
    
    
</template>
<script>
import { mapGetters, mapActions } from 'vuex'
export default {
    data() {
        return {
            id: '',
            oldname: '',
            oldemail: '',
            dialogModal: false,
            valid: true,
            name: '',
            nameRules: [
                v => (v && v.length <= 255) || 'Name must be less than 255 characters'
            ],
            showPassword: false,
            password: '',
            passwordRules: [
                v => (v && v.length >= 6) || 'Min 6 characters'
            ],

            email: '',
            emailRules: [
                v => /([a-zA-Z0-9_]{1,})(@)([a-zA-Z0-9_]{2,})+/.test(v) || 'E-mail must be valid'
            ],
        }
    },
    computed: {
        ...mapGetters({
            'user': 'auth/user'
        })
    },
    methods: {
        ...mapActions({
            setAuth: 'auth/set',
            setAlert: 'alert/set'
        }),
        submit() {
            console.log(this.oldemail)
            console.log(this.oldname)

            if(this.$refs.form.validate()) {
                let formData = new FormData()
                formData.set('name', this.oldname)
                formData.set('email', this.email)
                formData.set('password', this.password)
                let config = {
                    headers: {
                        'Authorization' : 'Bearer ' + this.user.api_token,
                    },
                }
                this.axios.post(`/update-profile/${this.id}`, formData, config)
                .then((response) => {
                    // console.log(response.data)
                    let data_user = response.data.data
                    this.setAuth(data_user)
                    this.setAlert({
                        status: true,
                        text : 'Update success',
                        type: 'success'
                    })
                })
                .catch((error) => {
                    console.log(error)
                    // let responses = error.response
                    this.setAlert({
                        status : true,
                        text : "Gagal Di update",
                        type: 'error'
                    })
                })
            }
        }
    },
    created() {
            this.id = this.user.id
            this.oldname = this.user.name
            this.email = this.user.email
    },
    
}
</script>