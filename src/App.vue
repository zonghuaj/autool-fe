<template>
    <a-locale-provider :locale="locale">
        <div>
            <a-layout id="components-layout-demo-top-side">
                <a-layout-header class="header">
                    <img
                        alt="site logo"
                        class="logo"
                        src="@/assets/images/logo.png"
                    />
                    <a-menu
                        theme="dark"
                        mode="horizontal"
                        :defaultSelectedKeys="defaultSelectedKeys"
                        class="top-menu"
                        @click="handleMenuClick"
                        :key="headerKey"
                    >
                        <!-- 默认key值一定要与url路径xxx/key/xxxx相同 -->
                        <a-menu-item key="home">首页</a-menu-item>
                        <a-menu-item key="cd">持续交付</a-menu-item>
                        <a-menu-item key="apitest">接口测试</a-menu-item>
                        <a-menu-item key="perftest">性能测试</a-menu-item>
                        <a-menu-item key="toolset">工具集</a-menu-item>
                        <a-menu-item key="resume">我的简历</a-menu-item>
                        <!--<a-menu-item key="uitest">UI自动化</a-menu-item>
                        <a-menu-item key="CODESCAN">源码扫描</a-menu-item>
                        <a-menu-item key="SECTEST">安全测试</a-menu-item>-->
                    </a-menu>
                    <span class="top-right">
                        <a-icon type="bell" class="top-bell" />
                        <a-icon type="user" class="top-user" />
                        <span class="top-login">
                            <!-- todo 1:接入用户中心 -->
                            <!-- todo 2:登陆了显示登陆人名字，否则显示登陆按钮 -->
                            <a-dropdown :trigger="['click']">
                                <a class="ant-dropdown-link" href>
                                    <!--yiming.tang-->
                                    <a-icon type="down" />
                                </a>
                                <a-menu slot="overlay">
                                    <a-menu-item key="0">
                                        <a href>登陆</a>
                                    </a-menu-item>
                                </a-menu>
                            </a-dropdown>
                        </span>
                    </span>
                </a-layout-header>
            </a-layout>
            <div>
                <router-view />
            </div>
        </div>
    </a-locale-provider>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import zhCN from 'ant-design-vue/lib/locale-provider/zh_CN'

export default {
    name: 'app',
    data() {
        return {
            locale: zhCN,
            defaultSelectedKeys: ['home'],
            activeHeader: '',
            activeSider: '',
            openKey: '',
            headerKey: 1
        }
    },

    computed: {
        ...mapState({
            menuList: state => state.menuList,
            siderKey: state => state.siderKey
        })
    },

    watch: {
        menuList: function(newval) {
            this.changeOpenkey(newval)
        },
        $route: function() {
            this.initMenu()
            this.headerKey += 1
        }
    },
    beforeMount() {
        this.initMenu()
    },

    methods: {
        ...mapActions([
            'setActiveHeader',
            'setActiveSider',
            'setOpenKey',
            'setSiderKey'
        ]),

        changeOpenkey: function(val) {
            let path
            this.openKey = ''
            path = window.location.pathname.substring(1)
            val.forEach(item => {
                !item.hasOwnProperty('children')
                    ? null
                    : item.children.forEach(it => {
                          if (it.path.substring(1) === path)
                              this.openKey = item.key
                      })
            })
            this.setOpenKey(this.openKey)
        },

        //根据路由设置选中头部菜单,侧边菜单
        initMenu() {
            let paths
            if (window.location.pathname !== '/') {
                paths = window.location.pathname.substring(1).split('/')
                this.defaultSelectedKeys[0] = paths[0]
                this.activeHeader = paths[0]
                this.activeSider = paths[1]
            } else {
                this.defaultSelectedKeys[0] = 'home'
            }
            this.setActiveHeader(this.activeHeader)
            this.setActiveSider(this.activeSider)
            this.changeOpenkey(this.menuList)
            this.setSiderKey(this.siderKey + 1)
        },

        handleMenuClick(val) {
            switch (val.key) {
                case 'home':
                    this.$router.push({ path: '/' })
                    break
                case 'perftest':
                    //默认转到该页第一个菜单上
                    this.$router.push({ path: '/perftest/dashboard' })
                    break
                case 'apitest':
                    this.$router.push({ path: '/apitest' })
                    break
                case 'cd':
                    this.$router.push({ path: '/cd' })
                    break
                case 'toolset':
                    this.$router.push({ path: '/toolset/testassertmanage' })
                    break
                case 'resume':
                    this.$router.push({ path: '/resume' })
                    break
                default:
                    this.$router.push({ path: '/404' })
            }
        }
    }
}
</script>

<style lang="less" scoped>
#components-layout-demo-top-side {
    .logo {
        width: 40px;
        margin-top: 13px;
        margin-left: 25px;
        float: left;
    }
    .top-menu {
        line-height: 63px;
        position: absolute;
        left: 250px;
    }
    .top-right {
        position: absolute;
        right: 30px;
        color: @icon-color;
    }
    .top-bell {
        font-size: 20px;
        color: @icon-color;
        vertical-align: middle;
    }
    .top-user {
        font-size: 20px;
        color: @icon-color;
        vertical-align: middle;
        margin-left: 10px;
    }
    .top-login {
        font-size: 14px;
        color: @icon-color;
        margin-left: 10px;
    }
}

.header {
    width: 100%;
}
</style>
