<!-- 项目查询 -->
<template>
	<view>
		<mescroll-uni :down="downOption" @emptyclick="downCallback" @down="downCallback" :up="upOption" @up="upCallback"
		 :fixed="false">
			<view class="mycard">
				<view v-for="item in cardList" :key="item.id" class="card-item">
					<uni-card is-full :title="item.projectType" is-shadow :extra="item.createTime" :thumbnail="`/static/img/project/${formatProjectType(item.projectType)}`"
					 @tapHeader="clickCard(item)">
						<view class="audit-card-content">
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">项目名称</view>
								<view class="flex-item-80">{{item.projectName}}</view>
							</view>
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">项目类型</view>
								<view class="flex-item-80">{{item.projectType}}</view>
							</view>
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">所在区域</view>
								<view class="flex-item-80">{{item.area}}</view>
							</view>
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">项目地址</view>
								<view class="flex-item-80">{{item.addr}}</view>
							</view>
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">立项人</view>
								<view class="flex-item-80">{{item.createUser}}</view>
							</view>
							<view class="uni-flex uni-row" @tap="clickCard(item)">
								<view class="flex-item-20">审核状态</view>
								<view class="flex-item-80">
									<uni-tag size="small" :text="formatAuditStatus(item.auditStatus).text" :type="formatAuditStatus(item.auditStatus).color"
									 :circle="true"></uni-tag>
								</view>
							</view>
							<view class="uni-flex uni-row">
								<view class="flex-item-20">附件</view>
								<view class="flex-item-80">
									<view v-for="atta in item.attachment" :key="atta.url" @tap="filePreview(atta)">
										<uni-icons color="#007aff" type="paperclip" size="22"></uni-icons><text>{{atta.name}}</text>
									</view>
								</view>
							</view>
						</view>
					</uni-card>
				</view>
			</view>
		</mescroll-uni>
	</view>
</template>

<script>
	import MescrollUni from "@/components/mescroll-uni/mescroll-uni.vue"
	import uniCard from "@/components/uni-card/uni-card"
	import uniTag from '@/components/uni-tag/uni-tag.vue'
	import uniIcons from '@/components/uni-icons/uni-icons.vue'
	import {
		mapGetters
	} from 'vuex'
	import {
		getRandomArrayElements,
		filePreview,
		formatAuditStatus,
		formatProjectType
	} from '@/utils/index.js'
	export default {
		components: {
			MescrollUni,
			uniCard,
			uniTag,
			uniIcons
		},
		computed: mapGetters(['user']),
		data() {
			return {
				mescroll: null,
				isPass: false,
				selectedProject: {},
				// 下拉刷新的常用配置
				downOption: {
					use: true, // 是否启用下拉刷新; 默认true
					auto: true, // 是否在初始化完毕之后自动执行下拉刷新的回调; 默认true
				},
				// 上拉加载的常用配置
				upOption: {
					use: true, // 是否启用上拉加载; 默认true
					auto: true, // 是否在初始化完毕之后自动执行上拉加载的回调; 默认true
					page: {
						num: 0, // 当前页码,默认0,回调之前会加1,即callback(page)会从1开始
						size: 10 // 每页数据的数量,默认10
					},
					noMoreSize: 5, // 配置列表的总数量要大于等于5条才显示'-- END --'的提示
					empty: {
						tip: '暂无相关数据'
					},
					toTop: {
						src: '/static/img/backtop.png'
					},
					textNoMore: '没有更多数据了'
				},
				// 列表数据
				cardList: [{
						id: 1,
						projectName: '培训考试信息管理系统项目',
						projectType: '软件研发',
						area: '省内',
						addr: '四川省',
						createUser: '张三',
						auditStatus: '2',
						createTime: '2019-10-21 17:38',
						attachment: [{
							name: '项目申请',
							url: '1.pdf'
						}]
					},
					{
						id: 2,
						projectName: '国旗护卫队服装采购项目',
						projectType: '采购',
						area: '省外',
						addr: '云南省',
						createUser: '李四',
						auditStatus: '2',
						createTime: '2019-10-19 17:00',
						attachment: [{
								name: '附件1',
								url: '2.pdf'
							},
							{
								name: '附件2',
								url: '3.pdf'
							}
						]
					},
					{
						id: 3,
						projectName: '超声高频外科集成系统（超声刀）项目',
						projectType: '系统集成',
						area: '省外',
						addr: '广东省',
						createUser: '王五',
						auditStatus: '2',
						createTime: '2019-09-21 13:00',
						attachment: [{
								name: '附件3',
								url: '4.pdf'
							},
							{
								name: '附件4',
								url: '5.pdf'
							}
						]
					}
				]
			}
		},
		methods: {
			filePreview,
			formatAuditStatus,
			formatProjectType,
			// mescroll组件初始化的回调,可获取到mescroll对象
			mescrollInit(mescroll) {
				// 如果this.mescroll对象没有使用到,则mescrollInit可以不用配置
				this.mescroll = mescroll
			},
			/*下拉刷新的回调, 有三种处理方式: */
			downCallback(mescroll) {
				// 第2种: 下拉刷新和上拉加载调同样的接口, 那以上请求可删, 直接用mescroll.resetUpScroll()代替
				mescroll.resetUpScroll() // 重置列表为第一页 (自动执行 page.num=1, 再触发upCallback方法 )
			},
			/*上拉加载的回调*/
			upCallback(mescroll) {
				if (!this.user) {
					mescroll.endErr()
					return
				}

				/**
				 * todo: api请求分页查询项目待办
				 */
				setTimeout(() => {
					this.cardList = getRandomArrayElements(this.cardList, 2)
					// 成功隐藏下拉加载状态
					// 方法一(推荐): 后台接口有返回列表的总页数 totalPage
					mescroll.endByPage(this.cardList.length, 3)
					this.$nextTick(() => {
						mescroll.endSuccess(this.cardList.length)
					})
				}, 2000)
			},
			clickCard(item) {
				uni.navigateTo({
					url: '/pages/project/project-detail?data=' + JSON.stringify(item)
				})
			},
			queryByName(name) {
				uni.showLoading({
					title: '正在查询数据...'
				})
				/**
				 * todo: api请求查询项目待办
				 */
				setTimeout(() => {
					this.cardList = getRandomArrayElements(this.cardList, 1)
					uni.hideLoading()
				}, 3000)
			},
			updateQuery() {
				this.queryByName('')
			}
		}
	}
</script>

<style>
	.mescroll-totop {
		opacity: 1 !important;
	}
</style>
