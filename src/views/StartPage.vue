<!--맨처음 시작페이지.-->
<template justify-center="justify-center">
    <v-list justify-center="justify-center">
        <v-layout class="createArticleBtn">
            <v-spacer></v-spacer>
            <!--정렬방법 찾아냄!!! https://electronic-moongchi.tistory.com/21-->
            <v-btn @click="goCreateArticlePage()">
                <v-icon color="black">
                    mdi-pencil
                </v-icon>
            </v-btn>
        </v-layout>
        <h1 id="textTitleEffect">
        </h1>
        <v-carousel
            class="carouselStyle"
            cycle="cycle"
            hide-delimiter-background="hide-delimiter-background"
            show-arrows-on-hover="show-arrows-on-hover">
            <template v-slot:prev="{ on, attrs }">
                <v-btn color="success" v-bind="attrs" v-on="on">Previous slide</v-btn>
            </template>
            <template v-slot:next="{ on, attrs }">
                <v-btn color="info" v-bind="attrs" v-on="on">Next slide</v-btn>
            </template>
            <!--페이지에 대한 안내를 담은 부분. carousel-item을 활용해서 보여지도록 하였다.-->
            <v-carousel-item v-for="carousel in carouselArr" :key="carousel.id">
                <v-sheet :color="carousel.color" height="100%">
                    <v-row class="fill-height" align="center" justify="center">
                        <v-img :src="carousel.imageURL" max-width="400"></v-img>
                        <div class="text-h2">
                            {{
                                carousel.text
                            }}
                        </div>
                    </v-row>
                </v-sheet>
            </v-carousel-item>
        </v-carousel>
        <v-layout max-width="100%" justify-center="justify-center">
            <v-spacer></v-spacer>
            <v-icon color="yellow">mdi-star</v-icon>
            <h3>다른 사람이 작성한 글들</h3>
            <v-icon color="yellow">mdi-star</v-icon>
            <v-spacer></v-spacer>
        </v-layout>
        <v-layout max-width="100%" justify-center="justify-center">
            <div class="searchDiv">
                <div class="searchBar">
                    <v-text-field
                        @input="searchArticle"
                        label="검색할 내용"
                        type="text"
                        v-model="searchInfo"></v-text-field>
                </div>
                <v-icon color="black">
                    mdi-magnify
                </v-icon>
            </div>
        </v-layout>
        <v-container>
            <v-row
                v-for="n in 1"
                :key="n"
                :class="n === 1 ? 'mb-6' : ''"
                no-gutters="no-gutters">
                <Article
                    class="ArticleMargin"
                    v-for="Article in DataProcessAllarticles"
                    :key="Article.id"
                    v-bind:childVaule="Article.id"
                    @click.native="goThisArticlePage(Article.id)"/>
            </v-row>
        </v-container>
        <!--이 컨텐트를 클릭했을때 Article만 따로 보이는 페이지로 넘어가도록 설정해야함. 대신 그때에는 Article 고유의 id를 하나만
        넘기던가 해서 페이지를 열게끔.-->
        <!--parentVaule값으로 Article고유 id값을 전달해보는건 어떨까...?-->
        <!--여기에 v-for로 랜덤으로 저장되어있는 값들중 무작위로 가져와서 글을 보여주도록 한다. 아니면 최근올린 글 순으로 하던지...
        https://developerjournal.tistory.com/4 << 이 페이지 참조바람.-->
    </v-list>
</template>
<script>

    import saveArticle_Info from "../assets/saveArticle_Info_By_JSON.json"
    import Article from "../components/Article.vue";
    export default {
        mounted() {
            //for Test
            if (!this.$store.state.admin.AllUsersInfo.map(u => u.id).includes("hostid")) {
                this.pushUserData();
            }
            if (!this.$store.state.articles.AllArticles.map(u => u.id).includes(0)) {
                this.pushArticleData();
            }
            this
                .$store
                .commit("MAKE_RANDOM_ARTICLES");
            setInterval(this.typing, 200);
        },
        components: {
            Article
        },
        data() {
            return {
                TextCounter: 0,
                DataProcessAllarticles: this.$store.state.articles.AllArticles,
                searchInfo: '',
                ArticleImageArr: [
                    {
                        image: require("../assets/default_Images/자리바꾸기.jpg")
                    }, {
                        image: require("../assets/default_Images/캘린더.jpg")
                    }, {
                        image: require("../assets/default_Images/Kanye.jpg")
                    }, {
                        image: require("../assets/default_Images/Kanye.jpg")
                    }, {
                        image: require("../assets/default_Images/Kanye.jpg")
                    }
                ],
                carouselArr: [
                    {
                        id: 0,
                        color: 'primary',
                        text: '이제 너를 마음껏 표현해봐',
                        imageURL: require("../assets/carousel_Image_1.png")
                    }, {
                        id: 1,
                        color: 'secondary',
                        text: '다른 사람과 자유롭게 소통해봐!',
                        imageURL: require("../assets/carousel_Image_2.png")
                    }, {
                        id: 2,
                        color: 'yellow darken-2',
                        text: '지금 너의 상태를 언제든지!',
                        imageURL: require("../assets/logo.png")
                    }
                ],
                saveArticles: saveArticle_Info.articles
            };
        },
        methods: {
            typing(){
                const content = "Welcome HoCGtagram.";
                const text = document.getElementById("textTitleEffect");
                if (this.TextCounter < content.length) {
                    let txt = content.charAt(this.TextCounter);
                    text.innerHTML += txt;
                    this.TextCounter++;
                }
            },
            goCreateArticlePage() {
                //로그인이 됐나 안됐나 확인할 필요가 전무함.
                if (this.$store.state.admin.LoginMode) {
                    this
                        .$router
                        .push({path: '/CreateArticlePage'})
                        .catch(() => {});
                    this
                        .$store
                        .commit("FORMAT_DATA_ARTICLE");
                } else {
                    this
                        .$router
                        .push({path: '/LoginPage'})
                        .catch(() => {})
                    }
            },
            pushArticleData() {
                let ImageCount = 0;
                for (let Article of this.saveArticles) {
                    this.$store.state.articles.currentArticle.id = parseInt(Article.id);
                    this.$store.state.articles.currentArticle.userID = String(Article.userID);
                    this.$store.state.articles.currentArticle.title = String(Article.title);
                    this.$store.state.articles.currentArticle.content = String(Article.content);
                    this.$store.state.articles.currentArticle.image = this
                        .ArticleImageArr[ImageCount]
                        .image;
                    let today = new Date();
                    let hours = ('0' + today.getHours()).slice(-2);
                    let minutes = ('0' + today.getMinutes()).slice(-2);
                    let seconds = ('0' + today.getSeconds()).slice(-2);
                    let timeString = hours + ':' + minutes + ':' + seconds;
                    let year = today.getFullYear();
                    let month = ('0' + (
                        today.getMonth() + 1
                    )).slice(-2);
                    let day = ('0' + today.getDate()).slice(-2);
                    let dateString = year + '-' + month + '-' + day;
                    this.$store.state.articles.currentArticle.startDate = dateString;
                    this.$store.state.articles.currentArticle.startTime = timeString;
                    this
                        .$store
                        .commit("ADD_ARTICLE", this.$store.state.articles.currentArticle);
                    ImageCount++;
                }
            },
            pushUserData() {
                this.$store.state.admin.currentUser.id = "hostid"
                this.$store.state.admin.currentUser.password = "1234"
                this.$store.state.admin.currentUser.userName = "관리자"
                this.$store.state.admin.currentUser.userBirthDay = "2020-12-12"
                this.$store.state.admin.currentUser.startDay = "0000-00-00"
                this.$store.state.admin.currentUser.profileImage = require(
                    "../assets/Initial_account.png"
                );
                this
                    .$store
                    .commit("ADD_NEW_USER", this.$store.state.admin.currentUser);
                this.$store.state.admin.currentUser.id = "hostid2"
                this.$store.state.admin.currentUser.password = "1234"
                this.$store.state.admin.currentUser.userName = "관리자2"
                this.$store.state.admin.currentUser.userBirthDay = "2020-12-12"
                this.$store.state.admin.currentUser.startDay = "0000-00-00"
                this.$store.state.admin.currentUser.profileImage = require(
                    "../assets/Initial_account.png"
                );
                this
                    .$store
                    .commit("ADD_NEW_USER", this.$store.state.admin.currentUser);
            },
            goThisArticlePage(ArticleID) {
                //ID전달을 쿼리가 아닌, vuex로 처리하는거지.
                this
                    .$store
                    .commit("FIND_ARTICLE", ArticleID);
                this
                    .$store
                    .commit("FIND_ARTICLE_COMMENTS", ArticleID);
                this
                    .$router
                    .push({path: '/ArticleDetailPage'})
                    .catch(() => {})
                },
            searchArticle() {
                this.DataProcessAllarticles = [];
                for (let Article of this.$store.state.articles.AllArticles) {
                    if (Article.title.includes(this.searchInfo)) {
                        this
                            .DataProcessAllarticles
                            .push(Article);
                    } else if (Article.content.includes(this.searchInfo)) {
                        this
                            .DataProcessAllarticles
                            .push(Article);
                    }
                }
                if (this.searchInfo.length === 0) {
                    this.DataProcessAllarticles = this.$store.state.articles.AllArticles
                }
            }
        }
    }
</script>
<style>
    .searchDiv {
        float: left;
    }
    .searchBar {
        width: 300px;
        display: inline-block;
    }
    .searchBtn {
        display: inline-block;
    }
    .mainImage {
        width: 20%;
    }
    .MainFrame {
        margin-left: 3%;
        margin-right: 3%;
        margin-bottom: 3%;
    }
    .text-h2 {
        font-weight: 700 !important;
    }
    .ArticleMargin {
        width: 47% !important;
        cursor: pointer;
        margin-left: 1.5%;
        margin-right: 1.5%;
        margin-bottom: 0.5%;
    }
    h1 {
        /*글자에 그라디에이션 주는 효과*/
        text-align: center;
        font-size: 60px;
        background: linear-gradient(to right top, #861657, #ffa69e);
        color: transparent;
        -webkit-background-clip: text;
    }
    .createArticleBtn {
        float: right;
    }
</style>