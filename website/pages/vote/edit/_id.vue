<template>
    <div>
        <app-header :user="user" />
        <div class="vote-box">
            <div class="vote-main">
                <ul class="vote-nav">
                    <li><a href="/"><span>主页</span></a></li>
                    <li class="vote-nav-sep"><span>/</span></li>
                    <li><span class="publish-label">发布话题</span></li>
                </ul>
                <div class="vote-box-wrap">
                    <div class="vote-area">
                        <vote-editor :vote="vote" :id="id" :user="user" />
                    </div>
                </div>
            </div>
        </div>
        <app-footer></app-footer>
    </div>
</template>

<script>
    import Header from '~/components/Header'
    import Footer from '~/components/Footer'
    import VoteEditor from '~/components/vote/save'
    import request from '~/net/request'

    export default {
        data () {
            return {}
        },
        validate ({ params }) {
            var hasId = !!params.id
            return hasId
        },
        middleware: 'userRequired',
        asyncData (context) {
            return request.getVote({
                client: context.req,
                query: {
                    f: 'md'
                },
                params: {
                    id: context.params.id
                }
            }).then(res => {
                return {
                    user: context.user,
                    vote: res.data,
                    id: context.params.id
                }
            }).catch(err => {
                console.log(err)
                context.error({ statusCode: 404, message: 'Page not found' })
            })
        },
        head () {
            return {
                title: this.vote.name,
                link: [
                    { rel: 'stylesheet', href: '/styles/editor/simplemde.min.css' }
                ]
            }
        },
        mounted () {
            console.log(this.vote)
        },
        components: {
            'app-header': Header,
            'app-footer': Footer,
            'vote-editor': VoteEditor
        }
    }
</script>

<style>
     @import '../../../assets/styles/vote/create.css';
</style>
