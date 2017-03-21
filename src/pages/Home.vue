<template>
	<el-row class="container">
		<el-col :span="24" class="header animated slideInDown">
			<el-col :span="10" class="logo" :class="collapsed?'logo-collapse-width':'logo-width'">
				{{collapsed?'G':sysName}}
			</el-col>
			<el-col :span="10">
					<div class="tools" @click.prevent="collapse">
						<i :class="collapsed?'fa fa-indent':'fa fa-dedent'"></i>
					</div>
					<a class="sysCls" href="http://www.baidu.com" target="_blank">子系统一</a><a class="sysCls" href="http://www.baidu.com" target="_blank">子系统二</a><a class="sysCls" href="http://www.baidu.com" target="_blank">子系统二</a>
			</el-col>
			<el-col :span="4" class="userinfo">
				<el-dropdown trigger="click">
					<span class="el-dropdown-link userinfo-inner"><img :src="this.sysUserAvatar" /> {{sysUserName}}</span>
					<el-dropdown-menu slot="dropdown">
						<el-dropdown-item>我的消息</el-dropdown-item>
						<el-dropdown-item>设置</el-dropdown-item>
						<el-dropdown-item divided @click.native="logout">退出登录</el-dropdown-item>
					</el-dropdown-menu>
				</el-dropdown>
			</el-col>
		</el-col>
		<el-col :span="24" class="main">
			<aside :class="collapsed?'menu-collapsed animated slideInLeft':'menu-expanded animated slideInLeft'">
				<!--导航菜单-->
				<el-menu :default-active="$route.path" class="el-menu--vertical" @open="handleopen" @close="handleclose" @select="handleselect"
					 unique-opened router v-show="!collapsed">
					<template v-for="(item,index) in $router.options.routes" v-if="!item.hidden">
						<el-submenu :index="index+''" v-if="!item.leaf">
							<template slot="title"><i :class="item.iconCls"></i>{{item.name}}</template>
							<el-menu-item v-for="child in item.children" :index="child.path" v-if="!child.hidden">{{child.name}}</el-menu-item>
						</el-submenu>
						<el-menu-item v-if="item.leaf&&item.children.length>0" :index="item.children[0].path"><i :class="item.iconCls"></i>{{item.children[0].name}}</el-menu-item>
					</template>
				</el-menu>
				<!--导航菜单-折叠后-->
				<ul class="el-menu el-menu--vertical collapsed" v-show="collapsed" ref="menuCollapsed">
					<li v-for="(item,index) in $router.options.routes" v-if="!item.hidden" class="el-submenu item">
						<template v-if="!item.leaf">
							<div class="el-submenu__title" style="padding-left: 20px;" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)"><i :class="item.iconCls"></i></div>
							<ul class="el-menu submenu" :class="'submenu-hook-'+index" @mouseover="showMenu(index,true)" @mouseout="showMenu(index,false)"> 
								<li v-for="child in item.children" v-if="!child.hidden" class="el-menu-item" style="padding-left: 40px;" :class="$route.path==child.path?'is-active':''" @click="$router.push(child.path)">{{child.name}}</li>
							</ul>
						</template>
						<template v-else>
							<li class="el-submenu">
								<div class="el-menu-item collapsed-item" style="padding-left: 20px;padding: 0 20px;" :class="$route.path==item.children[0].path?'is-active':''" @click="$router.push(item.children[0].path)"><i :class="item.iconCls"></i></div>
							</li>
						</template>
					</li>
				</ul>
			</aside>
			<section class="content-container">
				<div class="grid-content bg-purple-light">
					<el-col :span="24" class="breadcrumb-container">
					<strong class="title">{{$route.name}}</strong>
					<el-breadcrumb separator="/" class="breadcrumb-inner">
						<el-breadcrumb-item v-for="item in $route.matched">
							{{ item.name }}
						</el-breadcrumb-item>
					</el-breadcrumb>
					</el-col>
					<el-col :span="24" class="content-wrapper">
						<transition>
							<router-view></router-view>
						</transition>
					</el-col>
				</div>
			</section>
		</el-col>
	</el-row>
</template>

<script>
	export default {
		data() {
			return {
				sysName:'GMA',
				collapsed:false,
				sysUserName: '',
				sysUserAvatar: '',
				form: {
					name: '',
					region: '',
					date1: '',
					date2: '',
					delivery: false,
					type: [],
					resource: '',
					desc: ''
				}
			}
		},
		methods: {
			onSubmit() {
				console.log('submit!');
			},
			handleopen() {
				//console.log('handleopen');
			},
			handleclose() {
				//console.log('handleclose');
			},
			handleselect: function (a, b) {
			},
			//退出登录
			logout: function () {
				var _this = this;
				this.$confirm('确认退出吗?', '提示', {
					//type: 'warning'
				}).then(() => {
					sessionStorage.removeItem('user');
					_this.$router.push('/login');
				}).catch(() => {

				});
			},
			//折叠导航栏
			collapse:function(){
				this.collapsed=!this.collapsed;
				localStorage.setItem('collapsed',this.collapsed);
			},
			showMenu(i,status){
				this.$refs.menuCollapsed.getElementsByClassName('submenu-hook-'+i)[0].style.display=status?'block':'none';
			},
		},
		mounted() {
			var user = sessionStorage.getItem('user');
			if (user) {
				user = JSON.parse(user);
				this.sysUserName = user.name || '';
				this.sysUserAvatar = user.avatar || '';
			}
			var collapsed = JSON.parse(localStorage.getItem('collapsed'));
			this.collapsed=collapsed;
		}
	}
</script>
<style scoped>
::-webkit-scrollbar {
  width: 5px;
  height: 5px;
}

::-webkit-scrollbar-track {
  background-color: transparent;
}

/* 滚动条的滑轨背景颜色 */

::-webkit-scrollbar-thumb {
  background-color: #324057;
  padding: 0px 2px;
  border-radius: 5px;
}

/* 滑块颜色 */

::-webkit-scrollbar-button {
  background-color: transparent;
  height: 0px;
  width: 0px
}

/* 滑轨两头的监听按钮颜色 */

::-webkit-scrollbar-corner {
  background-color: transparent;
  height: 0px;
  width: 0px;
}
.tools:hover{
	background:rgba(0,0,0,0.2);
}
.tools>i:hover{
	color:#52b5d5;
}
.sysCls{
	display:inline-block;
	padding:0px 23px;
	color:white;
	text-decoration:none;
	border-color: rgba(0, 0, 0, 0.3);
    border-right-width: 1px;
    border-right-style: solid;ht:1px solid 
}
.sysCls:hover{
	background:rgba(0,0,0,0.2);
	color:#52b5d5;
}
.collapsed-item{
	background-color:#42485B;
}
.el-submenu>.collapsed-item:hover{
	background-color:#00C1DE;
}
</style>
<style scoped lang="scss">
	.container {
		position: absolute;
		top: 0px;
		bottom: 0px;
		width: 100%;
		.header {
			height: 50px;
			line-height: 50px;
			background: #373D41;//#20a0ff
			color:#fff;
			.userinfo {
				text-align: right;
				padding-right: 20px;
				float: right;
				.userinfo-inner {
					cursor: pointer;
					color:#fff;
					img {
						width: 30px;
						height: 30px;
						border-radius: 20px;
						margin: 10px 0px 10px 10px;
						float: right;
					}
				}
			}
			.logo {
				//width:180px;
				height:50px;
				font-size: 22px;
				padding-left:20px;
				padding-right:20px;
				border-color: rgba(0,0,0,0.3);
				border-right-width: 1px;
				border-right-style: solid;
				img {
					width: 40px;
					float: left;
					margin: 10px 10px 10px 18px;
				}
				.txt {
					color:#fff;
				}
			}
			.logo-width{
				width:180px;
				text-align:center;
			}
			.logo-collapse-width{
				width:60px
			}
			.tools{
				padding: 0px 23px;
				width:14px;
				height: 50px;
				line-height: 50px;
				cursor: pointer;
				float:left;
				border-color: rgba(0, 0, 0, 0.3);
				border-right-width: 1px;
				border-right-style: solid;
			}
		}
		.main {
			display: flex;
			// background: #324057;
			position: absolute;
			top: 50px;
			bottom: 0px;
			overflow: hidden;
			aside {
				flex:0 0 180px;
				width: 180px;
				z-index:2;
				// position: absolute;
				// top: 0px;
				// bottom: 0px;
				.el-menu{
					height: 100%;
				}
				
				.collapsed{
					width:60px;
					.item{
						position: relative;
						
					}
					.submenu{
						position:absolute;
						top:0px;
						left:60px;
						z-index:99999;
						height:auto;
						display:none;
						.item{
							background:red;
						}
					}
				}
			}
			.menu-collapsed{
				flex:0 0 60px;
				width: 60px;
			}
			.menu-expanded{
				flex:0 0 180px;
				width: 180px;
			}
			.content-container {
				// background: #f1f2f7;
				flex:1;
				// position: absolute;
				// right: 0px;
				// top: 0px;
				// bottom: 0px;
				// left: 180px;
				overflow-y: scroll;
				padding: 20px;
				.breadcrumb-container {
					//margin-bottom: 15px;
					.title {
						width: 200px;
						float: left;
					}
					.breadcrumb-inner {
						float: right;
					}
				}
				.content-wrapper {
					background-color: #fff;
					box-sizing: border-box;
				}
			}
		}
	}
	.animated {
    	animation-duration: .3s;
	}
</style>