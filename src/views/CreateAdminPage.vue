<!--회원가입 페이지.-->
<!--가입일을 다는 기능까지 추가되야 할듯하다.-->
<template>
    <v-layout max-width="100%" justify-center="justify-center">
        <v-card-actions class="justify-center">
            <v-card width="1000px">
                <v-layout class="CreateAdminTool">
                    <v-spacer></v-spacer>
                    <v-icon color="yellow" size="24">mdi-star</v-icon>
                    <h2>회원가입</h2>
                    <v-icon color="yellow" size="24">mdi-star</v-icon>
                    <v-spacer></v-spacer>
                </v-layout>
                <v-card-text>
                    <v-form>
                        <v-text-field
                            prepend-icon="mdi-account"
                            name="ID"
                            label="ID"
                            type="text"
                            v-model="admin.id"></v-text-field>
                        <v-text-field
                            prepend-icon="mdi-lock"
                            name="password"
                            label="password"
                            type="password"
                            v-model="admin.password"></v-text-field>
                        <span>비밀번호는 영문, 숫자, 특수문자 조합으로 10~15자리로 만들어주세요! </span>
                        <v-text-field
                            prepend-icon="mdi-lock"
                            name="password check"
                            label="password check"
                            type="password"
                            v-model="passwordCheck"></v-text-field>
                        <v-text-field
                            prepend-icon="mdi-account"
                            name="Name"
                            label="Name"
                            type="text"
                            v-model="admin.name"></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="mainColor" @click="NewAdminCheck">
                        <h3>회원가입</h3>
                    </v-btn>
                    <v-btn color="mainColor" @click="backStartPage">
                        <h3>뒤로가기</h3>
                    </v-btn>
                    <v-spacer></v-spacer>
                </v-card-actions>
            </v-card>
        </v-card-actions>
        <Alert :dialog="true"/>
    </v-layout>
</template>
<script>
    import Alert from "../components/AlertForm.vue"
    export default {
        computed: {
            admin() {
                this
                    .$store
                    .commit("FORMAT_USER_INFO");
                return this.$store.state.admin.TheUser_usedByData;
            }
        },
        data() {
            return {passwordCheck: ""}
        },
        components:{
            Alert
        },
        methods: {
            passwordOverlapCheck(password, passwordCheck) {
                if (password === passwordCheck) {
                    return true;
                } else {
                    return false;
                }
            },
            passwordUniquenessCheck(id, password) {
                !/^(?=.*[a-zA-z])(?=.*[0-9])(?=.*[$`~!@$!%*#^?&\\(\\)\-_=+]).{8,16}$/
                if (!/^(?=.*[a-zA-z])(?=.*[0-9])(?=.*[$`~!@$!%*#^?&\\(\\)\-_=+]).{10,16}$/.test(password)) {
                    this.$store.commit("OPEN_ALERT",'숫자와 영문자, 특수문자 조합으로 10~15자리를 사용해야 합니다.');
                    return false;
                }
                let checkNumber = password.search(/[0-9]/g);
                let checkEnglish = password.search(/[a-z]/ig);
                let checkSpecialText = password.search(/[$`~!@$!%*#^?&\\(\\)\-_=+]/)
                if (checkNumber < 0 || checkEnglish < 0 || checkSpecialText < 0) {
                    this.$store.commit("OPEN_ALERT", "숫자와 영문자, 그리고 특수문자를 혼용하여야 합니다.");
                    return false;
                }
                if (/(\w)\1\1\1/.test(password)) {
                    this.$store.commit("OPEN_ALERT", '444같은 문자를 4번 이상 사용하실 수 없습니다.');
                    return false;
                }
                if (password.search(id) > -1) {
                    this.$store.commit("비밀번호에 아이디가 포함되었습니다.");
                    return false;
                }
                return true;
            },
            NewAdminCheck() {
                if (this.passwordOverlapCheck(this.admin.password, this.passwordCheck)) {
                    if (this.passwordUniquenessCheck(this.admin.id, this.admin.password)) {
                        let today = new Date();  
                        let year = today.getFullYear();
                        let month = ('0' + (today.getMonth() + 1)).slice(-2);
                        let day = ('0' + today.getDate()).slice(-2);
                        let dateString = year + '-' + month  + '-' + day;
                        this.admin.startDay = dateString;
                        this.admin.profileImage = require("../assets/Initial_account.png");
                        //가입일까지 추가시키자.
                        this
                            .$store
                            .commit("ADD_NEW_USER", this.admin);
                        this.$store.commit("OPEN_ALERT_PAGE_OVER_MODE", "회원가입 성공! 가입을 환영합니다.");
                    }
                }
                else{
                    this.$store.commit("OPEN_ALERT", "비밀번호와 비밀번호확인이 일치하지않습니다. 다시 작성해주세요");
                }
            },
            backStartPage() {
                this
                    .$router
                    .push({path: "/", query: {}})
            }
        }
    }
    //중복검사를 요구하며 모든 내용을 기입했는지를 확인해야하고 비밀번호를 양식에 맞게 잘 작성했는지를 확인해야한다.
</script>
<style>
   .CreateAdminTool {
       margin-top: 5%;
   }
</style>