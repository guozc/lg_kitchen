<template>
<transition name="move" @after-leave="afterLeave" @after-enter="afterEnter">
  <div class="order" v-show="visible">
      <cube-scroll ref="scroll">
          <div class="order-content">
                <div class="order-header">
                    <div class="back-btn" @click="hide">
                        <i class="icon-lg-aleft"></i>
                    </div>
                    <div class="status">
                        {{order.status === 1 ? '主厨未确认菜单 >' : '主厨已确认菜单 >'}}
                    </div>
                </div>
                <div class="box-wrapper">
                    <div class="order-info">
                        <div class="date-wrapper">
                            <span>用餐日期：</span>
                            <span class="date">{{order.date}}</span>
                        </div>
                        <div class="interval-wrapper">
                            <span>用餐时段：</span>
                            <span class="interval">{{order.interval == 0 ? '午餐' : '晚餐'}}</span>
                        </div>
                    </div>
                </div>
                <div class="box-wrapper">
                    <div class="dishes-info">
                        <div class="dishes-info-head">
                            <div>最终确认菜单</div>
                        </div>
                        <div class="dishes-wrapper" v-if="order.dishes.length > 0">
                            <ul>
                                <li class="dish-info border-bottom-1px" v-for="(dish, index) in order.dishes" :key="index">
                                    <div class="dish-image">
                                        <img :src="dish.imageUrl" width="40" height="40" alt="">
                                    </div>
                                    <div class="dish-content">
                                        <div class="name">{{dish.name}}</div>
                                        <div class="icon">
                                            <lg-icon :size=16 :type=2 :subType=dish.dtype></lg-icon>
                                            <lg-icon :size=16 :type=0 :subType=dish.ismeat></lg-icon>
                                            <lg-icon :size=16 :type=1 :subType=dish.spicy></lg-icon>
                                        </div>
                                    </div>
                                </li>
                            </ul>
                        </div>
                        <div class="none-wrapper" v-if="!order.dishes.length">
                            主厨未确认菜单
                        </div>
                    </div>
                </div>
                <div class="box-wrapper">
                    <div class="bills-info">
                        <div class="bills-info-head">
                            <div>用户点单信息</div>
                        </div>
                        <div class="bills-wrapper">
                            <ul>
                                <li class="bill-info border-bottom-1px" v-for="(bill, index) in recombineBills" :key="index">
                                    <div class="user-wrapper">
                                        <div class="user-avatar">
                                            <img :src="bill.avatar" width="25" height="25" alt="">
                                        </div>
                                        <div class="text">的点单：</div>
                                    </div>
                                    <div class="dish-wrapper">
                                        <ul>
                                            <li class="dish" v-for="(dish,index) in bill.dishes" :key="index">
                                                <div class="dish-pic">
                                                    <img :src="dish.imageUrl" width="50" height="50" alt="">
                                                </div>
                                                <div class="text">
                                                    {{dish.name}}
                                                </div>
                                            </li>
                                        </ul>
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
          </div>
      </cube-scroll>
  </div>
  </transition>
</template>

<script>
import popupMixin from 'common/mixin/popup'
import lgIcon from 'components/lg-icon/lg-icon'

const EVENT_LEAVE = 'leave'
const EVENT_ADD = 'add'

export default {
    name: 'order',
    mixins: [popupMixin],
    components: {
        lgIcon
    },
    data() {
        return {
         }
    },
    props: {
        order: {
            type: Object,
            default() {
                return {}
            }
        }
    },
    created() {},
    computed: {
        recombineBills() {
            const ret = []
            let userIds = []
            this.order.bills.forEach((bill) => {
                userIds.push(bill.uid)
            })
            userIds = Array.from(new Set(userIds))
            userIds.forEach((userid) => {
                const userbill = {}
                userbill.id = userid
                userbill.dishes = []
                this.order.bills.forEach((bill) => {
                    if (bill.uid === userid) {
                        userbill.avatar = bill.avatar
                        userbill.name = bill.name
                        userbill.dishes.push({
                            name: bill.dname,
                            imageUrl: bill.imageUrl
                        })
                    }
                })
                ret.push(userbill)
            })

            return ret
        }
    },
    methods: {
        afterEnter() {
             this.$nextTick(() => {
                this.$refs.scroll.refresh()
            })
        },
        onAdd(target) {
            this.$emit(EVENT_ADD, target)
        },
        afterLeave() {
            this.$emit(EVENT_LEAVE)
        }
    }
}
</script>
<style lang="stylus" scoped>
@import '~common/stylus/variable'
.order
    position:fixed
    top:0
    left:0
    width:100%
    bottom: 0
    background: $color-background-ssss
    &.move-enter-active, &.move-leave-active
        transition: all 0.3s linear
    &.move-enter, &.move-leave-active
        transform: translate3d(100%, 0, 0)
        opacity: 0
    .order-content
        width: 100%
        background: $color-background-ssss
        position: relative
        .order-header
            position: relative;
            background: $color-background-ssss
            padding: 15px
            .back-btn
                text-align: center
                font-size: $fontsize-large-xxx
                color: $color-dark-grey
                font-weight: 700
                text-align:left
                i
                    display:inline-block
            .status
                font-size: $fontsize-large-xx
                color: $color-dark-grey
                font-weight: 700
                margin-top:30px
        .box-wrapper
            padding: 0 15px
            .order-info
                padding: 15px
                border: 1px solid #eee
                background-color:$color-white
                border-radius:5px
                .date-wrapper
                    font-size:$fontsize-medium
                    color:$color-light-grey
                    .date
                        color: $color-light-pink
                .interval-wrapper
                    margin-top:10px
                    font-size:$fontsize-medium
                    color:$color-light-grey
                    .interval
                        color: $color-light-pink
            .dishes-info
                margin-top:5px
                padding: 15px
                border: 1px solid #eee
                background-color:$color-white
                border-radius:5px
                .dishes-info-head
                    div
                        display:inline-block
                        font-size: $fontsize-medium
                        padding-bottom:5px
                        border-bottom:2px solid $color-light-pink
                .dishes-wrapper
                    margin-top:5px
                    .dish-info
                        padding:10px 0
                        display:flex
                        .dish-image
                            flex: 0 0 50px
                        .dish-content
                            margin-top:5px
                            .name
                                font-size:$fontsize-small
                            .icon
                                margin-top:5px
                                display: flex
                .none-wrapper
                    font-size:$fontsize-small
                    color: $color-grey
                    margin-top:10px
            .bills-info
                margin-top:5px
                padding: 15px
                border: 1px solid #eee
                background-color:$color-white
                border-radius:5px
                .bills-info-head
                    div
                        display:inline-block
                        font-size: $fontsize-medium
                        padding-bottom:5px
                        border-bottom:2px solid $color-light-pink
                .bills-wrapper
                    margin-top:5px
                    .bill-info
                        padding: 10px 0
                        .user-wrapper
                            display:flex
                            align-items:center
                            .user-avatar
                                flex: 0 0 30px
                                img
                                    border: 1px solid $color-light-grey
                                    border-radius: 25px
                            .text
                                font-size: $fontsize-small
                    .dish-wrapper
                        margin-top:10px
                        ul
                            display:flex
                            flex-wrap: wrap
                            .dish
                                margin-bottom:10px
                                width:72px
                                text-align:center
                                .text
                                    margin-top:5px
                                    width:72px
                                    font-size:$fontsize-small
                                    color: $color-grey

</style>
