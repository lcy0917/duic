<style lang="less">
    @import "./main.less";
</style>
<template>
    <div class="main" :class="{'main-hide-text': shrink}">
        <div class="sidebar-menu-con" :style="{width: shrink?'60px':'200px', overflow: shrink ? 'visible' : 'auto'}">
            <shrinkable-menu :shrink="shrink" :menu-list="menuList">
                <div slot="top" class="logo-con">
                    <img v-show="!shrink" src="../images/duic200x60.png"/>
                    <img v-show="shrink" src="../images/duic60x60.png"/>
                </div>
            </shrinkable-menu>
        </div>
        <div class="main-header-con" :style="{paddingLeft: shrink?'60px':'200px'}">
            <div class="main-header">
                <div class="navicon-con">
                    <i-button :style="{transform: 'rotateZ(' + (this.shrink ? '-90' : '0') + 'deg)'}" type="text"
                              @click="toggleClick">
                        <icon type="navicon" size="32"></icon>
                    </i-button>
                </div>
                <div class="header-avator-con">
                    <div class="user-dropdown-menu-con">
                        <row type="flex" justify="end" align="middle" class="user-dropdown-innercon">
                            <dropdown transfer trigger="click" @on-click="handleClickUserDropdown">
                                <a href="javascript:void(0)">
                                    <span class="main-user-name">{{ email }}</span>
                                    <icon type="arrow-down-b"></icon>
                                </a>
                                <dropdown-menu slot="list">
                                    <dropdown-item name="update-password">修改密码</dropdown-item>
                                    <dropdown-item name="logout" divided>退出登录</dropdown-item>
                                </dropdown-menu>
                            </dropdown>
                            <avatar icon="person" style="background: #619fe7;margin-left:10px;"></avatar>
                        </row>
                    </div>
                </div>
            </div>
        </div>
        <div class="single-page-con" :style="{left: shrink?'60px':'200px'}">
            <div class="single-page">
                <router-view></router-view>
            </div>
        </div>
    </div>
</template>
<script>
    import axios from 'axios';
    import Cookies from 'js-cookie';
    import shrinkableMenu from './main-components/shrinkable-menu/shrinkable-menu.vue';

    export default {
        components: {
            shrinkableMenu
        },
        data() {
            return {
                shrink: false
            };
        },
        computed: {
            email() {
                return Cookies.get('email');
            },
            menuList() {
                return this.$store.state.app.menuList;
            }
        },
        methods: {
            toggleClick() {
                this.shrink = !this.shrink;
            },
            handleClickUserDropdown(name) {
                if (name === 'logout') {
                    this.logout()
                } else if (name === 'update-password') {
                    this.$router.push({name: 'user-update-password'});
                }
            },
            logout() {
                Cookies.remove('token');
                Cookies.remove('email');
                this.$router.push({name: 'login'});
            }
        },
        mounted() {
            var token = Cookies.get('token');
            var email = Cookies.get('email');
            if (!token || !email) {
                this.$router.push({name: 'login'});
                return
            }

            axios.get(`/api/admins/user/root`).then(response => {
                this.$store.commit('updateAuthUser', {email: email});
                this.$store.commit('updateMenulist', email === response.data.root);
            });
        }
    };
</script>
