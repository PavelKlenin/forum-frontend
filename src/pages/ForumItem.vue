<template>
    <div class="content row">
      <div class="main-content col-xs-12 col-md-6">
        <div class="post-content row">

          <router-link to="/" class="arrow-home col-xs-12 start-xs"> <!-- сделать размер 15px -->
            <i class='icon-arrow-back'></i>
            Главная
          </router-link>

          <h1 class="postName col-xs-12">{{post.title}}</h1>

          <div class="tags row col-xs-12" v-if="post.tags && post.tags.length">
            <i class='icon-label'></i>
            <a href="#" class="postTag" v-for="(tag, i) in post.tags" :key="i">{{tag}}</a>
          </div>

          <div class="post row" >
            <div class="userImg">U</div>

            <div class="postBody col-xs-12 col-sm">
              <div class="postProps row between-xs">
                <a href="#" class="postUserName">{{userName}}</a>
                <div class="postTime" v-if="post.created_at && !null">{{[post.created_at, "YYYY-MM-DD HH:mm:ss"] | moment("from") }}</div>
              </div>
              <div class="postText">{{post.content}}</div>
              <div class="deletePost row end-xs">
                <button class="button button-main" v-if="post.user_id === my.id"  @click="delConfirm">Удалить</button>
              </div>
            </div>
          </div>

        </div>

        <div class="post-content">
          <h2 class="commentsCount">
            Комментарии ({{commentsCount}})
          </h2>
          <div class="commentBlock" v-for="comment in userComments" :key="comment.id">
            <div class="post row">

              <div class="userImg">U</div>

              <div class="postBody row col-xs-12 col-sm">
                <a href="#" class="postUserName">{{comment.name}}</a>

                <div class="postText">{{comment.body}}</div>

                <!-- Кнопки "ответить" и "спасибо" '.invisible', если пользователь не зарегистрирован  -->
                <div class="commentProps row" :id="`comment-props-${comment.id}`">
                  <p class="answer" @click="openAnswer(comment.id, comment.name)">Ответить</p>
                  <div class="likeBody row">
                    <p class="like">Спасибо</p>
                    <div class="likeIcon">#</div>
                    <div class="likeCounter">0</div>
                  </div>
                </div>
                <!-- -->
              </div>
            </div>

            <div class="my-answer invisible row" :id="`my-answer-${comment.id}`">
              <div class="userImg">I</div>
              <div class="row col-xs-12 col-sm">
              <textarea
                @keyup.enter="addComment"
                :id="`textarea-${comment.id}`"
                v-model="newComment"
                type="text"
                class="postText col-xs-12">
              </textarea>
                <!-- Кнопки '.invisible', пока нет фокуса на textarea -->
                <div class="btnWrap row col-xs-12 center-xs between-sm">
                  <!-- Кнопка не нажмается, пока в textarea нет текста -->
                  <button class="button button-main">Отправить</button>
                  <!-- -->
                  <button class="button button-main esc-btn" @click="closeAnswer(comment.id)">Х</button>
                </div>
                <!-- -->
              </div>
            </div>

          </div>

          <h2>Оставить комментарий</h2>
          <div class="authMessage">
            <a href="#" class="auth">Войдите</a>
            или
            <a href="#" class="auth">зарегистрируйтесь</a>
            чтобы оставить комментарий
          </div>
        </div>

        <!-- div скрывается, если не зарегистрирован :class="{invisible : loggedIn}" -->
        <div  class="post-content" >

          <h2>Оставить комментарий</h2>

          <div  class="myCommentBlock row">
            <div class="userImg">I</div>
            <div class="userCommentBody row col-xs-12 col-sm">
            <textarea
              type="text"
              v-model="newComment"
              class="postText col-xs-12"
              id="comment">
            </textarea>
              <!-- Кнопки '.invisible', пока нет фокуса на textarea -->
              <div class="btnWrap row col-xs-12 center-xs start-sm">
                <!-- Кнопка не нажмается, пока в textarea нет текста -->
                <button class="button button-main" @click="addComment">Отправить</button>
                <!-- -->
              </div>
              <!-- -->
            </div>
          </div>
          <!-- -->
        </div>

        <div class="sameRequest">
          <h2 class="sameRequestTitle">Похожие запросы</h2>
          <div class="sameRequestBody">
            <h2>Как вы разрабатываете REST API?</h2>
            <div class="sameRequestProps row start-xs">
              <div class="#">
                <i class='icon-label'></i>
                <span>RESTFUL API</span>
                <i class='icon-clock'></i>
                <span>5 минут назад</span>
              </div>
              <div class="#">
                <i class='icon-eye'></i>
                <span>25 просмотров</span>
                <a>
                  <i class='icon-speak'></i>
                  Ответить
                </a>
              </div>
            </div>
          </div>
        </div>

      </div>
      <div class="left-content col-md-2 first-md">
        <div class="side-bar">
        </div>
      </div>
      <div class="right-content col-xs-12 col-md-4">
        <div class="new-topic-block">
          <h2>Новые темы</h2>
          <div class="new-topic row">
            <div class="userImg">U</div>
            <a href="#" class="new-topic-name col-xs">Как вы разрабатываете REST API?</a>
            <div class="new-topic-props col-xs-2">3<br/>ответа</div>
          </div>
        </div>
      </div>
      <div class="del-confirm invisible row center-xs col-xs-12 around-sm col-sm-6">
        <p class="col-xs-12">Вы уверены, что хотите удалить пост? Все комментарии тоже будут удалены.</p>
        <button class="button button-main col-xs-12 col-sm-5" @click="delPost">Удалить</button>
        <button class="button button-main col-xs-12 col-sm-5" @click="clsConfirm">Отмена</button>
      </div>
      <div class="after-del-confirm invisible row center-xs col-xs-12 around-sm col-sm-6">
        <p class="col-xs-12">Пост удалён.</p>
        <router-link class="col-xs-12 col-sm-7" to="/forum">
          <button class="button button-main confirm-link">Ок</button>
        </router-link>
      </div>
      <div class="shadow invisible"></div>
    </div>
</template>

<script>

  export default {
    name: 'ForumItem',
    props: ['postId'],
    data: function() {
      return {
        newComment: '',
        comments:[{
          textComment: '',
        }
        ],
        post: '',
        user: '',
        userId: '',
        commentsCount: '',
        userName: '',
        userComments: [],
       }
    },
    async mounted() {
      await this.axios
        .get('posts/' + this.postId)
        .then((post) => {
          console.log(post.data);
          this.post = post.data;
          this.userId = post.data.username.id;
          this.commentsCount = post.data.comments.length;
          this.userName = post.data.username.name;
        })
        .catch(error => console.log(error));
      await this.axios
        .get(`users/${this.userId}`)
        .then((user) => {
          this.user = user.data;
        })
        .catch(error => console.log(error));
      await this.axios
        .get(`posts/${this.userId}/comments`)
        .then((comment) => {
          this.userComments = comment.data;
          // console.log(this.userComments);
        })
        .catch(error => console.log(error));
    },
    computed:{
      my() {
        return this.$store.getters.profile;
      }
    },
    methods: {
      async delPost () {
        await this.axios
          .delete(
            `posts/${this.post.id}`,
          )
          .then((response) => {
            document.querySelector('.del-confirm').classList.add('invisible');
            document.querySelector('.after-del-confirm').classList.remove('invisible');
            return response;
          })
          .catch(error => console.log(error));
      },
      delConfirm () {
        document.querySelector('.del-confirm').classList.remove('invisible');
        document.querySelector('.shadow').classList.remove('invisible');
      },
      clsConfirm () {
        document.querySelector('.del-confirm').classList.add('invisible');
        document.querySelector('.shadow').classList.add('invisible');
      },
      openAnswer(id, name) {
        // this.newComment[id] += `${name}, `;
        this.newComment = '';
        document.querySelector(`#my-answer-${id}`).classList.remove('invisible');
        document.querySelector(`#comment-props-${id}`).classList.add('invisible');
        this.newComment += `${name}, `;
        document.querySelector('#comment').focus()
        // console.log(this.newComment);
        // document.querySelector(`#textarea-${id}`).focus();

      },
      closeAnswer(id) {
        document.querySelector(`#my-answer-${id}`).classList.add('invisible');
        document.querySelector(`#comment-props-${id}`).classList.remove('invisible');
        // this.newComment[id] = '';
      },
      addComment: function () {
        this.comments.push({
          textComment: this.newComment,

        });
        this.newComment = "";
      },
     },
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="sass" scoped>
  @import "../assets/variables"

  .content
    h2
      font-size: $h2_font_size
      font-weight: bolder
      line-height: 23px
      margin-bottom: 15px
    .post
      width: 100%

  h3
    color: #b90015

  .main-content
    margin-top: 38px
    *
      margin: 0
      padding: 0
      &:not(button)
        background-color: $text_background_color
    .userImg // заменить на фото
      height: 32px
      border-radius: 50%
      width: 32px
      background-color: grey
      text-align: center
      line-height: 32px
      margin-right: 16px
      margin-bottom: 8px
    .postUserName
      line-height: 14px
      text-decoration: none
      margin-bottom: 9px
      &:hover
        opacity: 0.5
    .postText
      margin-bottom: 12px
      padding: 13px 16px 15px
      border-radius: 4px
      background-color: $comment_background_color
      word-wrap: break-word
    textarea
      min-height: 88px
      border: none
      resize: none // переделать через js
      overflow: auto
      &:focus
        outline: none

  .post-content
    padding: 10px 28px 24px 16px
    margin-bottom: 17px
    .arrow-home
      margin-top: -48px
      margin-bottom: 10px
      line-height: 38px
      text-decoration: none
      background-color: $background-color
      &:hover
        opacity: 0.5
      .icon-arrow-back
        background-color: $background-color
        margin-right: 5px
    h1
      font-size: $h1_font_size
      line-height: 33px
      font-weight: bold
      margin-bottom: 14px
    .tags
      margin-bottom: 16px
      a
        display: inline-block
        margin-right: 4px
        font-size: $tags_font_size
        line-height: 15px
        color: $base_font_color
        opacity: 0.5
        text-decoration: none
        &:hover
          opacity: 1
      .icon-label
        color: $base_font_color
        opacity: 0.5
        margin-right: 4px

  .postProps
    font-size: $tags_font_size
    .postTime
      line-height: 14px
      margin-bottom: 9px

  .my-answer
    margin-top: 28px
    margin-bottom: 42px

  .button
    margin-bottom: 5px

  .esc-btn
    margin-left: 5px

  .commentProps
    margin-bottom: 28px
    font-size: $tags_font_size
    line-height: 14px
    cursor: pointer
    .answer
      margin-right: 27px
      &:hover
        opacity: 0.5
    .likeBody
      &:hover
        opacity: 0.5
      .like
        margin-right: 10px
      .likeCounter
        margin-left: 6px

  .authMessage
    font-size: $medium_font_size
    line-height: 16px
    .auth
      text-decoration: underline
      &:hover
        opacity: 0.5

  .sameRequest
    padding: 13px 14px 24px
    .sameRequestTitle
      margin-bottom: 15px
    .sameRequestBody
      &:not(:last-child)
        margin-bottom: 24px
      .sameRequestProps
        span, i, a
          font-size: $tags_font_size
        span, i
          color: $dark_background_color
        span
          margin-right: 16px
        i
          margin-right: 4px
        a
          cursor: pointer
          &:hover
            opacity: 0.5
        .icon-speak
          color: $base_font_color
  .right-content
    padding-top: 38px
    *
      margin: 0
      padding: 0
      background-color: $aside_background_color
    .new-topic-block
      padding: 13px 12px 30px
      background-color: $aside_background_color
    div, a
      font-size: $medium_font_size
      font-weight: 500
    .new-topic:not(:last-child)
      margin-bottom: 24px
    .userImg // заменить на фото
      height: 32px
      border-radius: 50%
      width: 32px
      background-color: $light_background_color
      text-align: center
      line-height: 32px
      margin-right: 6px
      margin-bottom: 8px
    .new-topic-name
      padding-top: 5px
      text-decoration: none
      &:hover
        opacity: 0.5
    .new-topic-props
      text-align: center

  .left-content
    display: none
    padding: 0
    @media (min-width: 1024px) // не придумал, как скрыть блок через flexboxgrid
      display: block
    .side-bar
      background-color: $aside_background_color
      height: 100%

  .del-confirm, .after-del-confirm
    padding-top: 15px
    padding-bottom: 15px
    position: fixed
    top: 50%
    left: 50%
    transform: translate(-50%, -50%)
    background: $text_background_color
    border-radius: 4px
    min-width: 300px
    z-index: 1100
    max-height: 100%
  .after-del-confirm
    a
      padding: 0
    .confirm-link
      width: 100%
      height: 100%
      margin: 0

  .shadow
    position: fixed
    top: 0
    left: 0
    height: 100%
    width: 100%
    z-index: 1000
    background: $comment_background_color
    opacity: 0.5

  .invisible
    display: none
    margin-bottom: 0

</style>
