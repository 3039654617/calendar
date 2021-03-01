<template>
    <div class="allElement" v-click>
        <div class="header">
            <span class="iconfont icon-calendar"/>
            <input type="text" :value='chooseDate'>
        </div>
        <div class="body" v-if='show'>
            <div class="arrow"/>
            <div class="cHeader">
                <span class="iconfont icon-left-two-arrow" @click="chooseYear('prev')"/>
                <span class="iconfont icon-left-arrow" @click="chooseMonth('prev')"/>
                <span>{{ showToday.year }}年{{ +showToday.month+1}}月</span>
                <span class="iconfont icon-right-arrow" @click="chooseMonth('next')"/>
                <span class="iconfont icon-right-two-arrow" @click="chooseYear('next')"/>
            </div>
            <div class="cBody">
                <div class="weeks">
                    <div
                        v-for="week in ['日', '一', '二' , '三', '四', '五', '六']"
                        :key="week"
                    >
                        {{ week }}
                    </div>
                </div>
                <div class="showDay">
                    <div
                        v-for="day in showDay"
                        :key="day.getTime()"
                        :class="{
                            'circle': isCur(day).select, 
                            'today': isCur(day).day,
                            'other-month': !isCur(day).month,
                        }"
                        @click="onChooseDate(day)"
                    >
                        {{ day.getDate() }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    directives: {
        'click': {
            bind(el, binding, vnode) {
                const vm = vnode.context;
                document.onclick = function(e){
                    const contain = el.contains(e.target);
                    vm.showDate(contain);
                }
            }
        }
    },
    props: {
        date: {
            type: Date,
            default: () => new Date()
        }
    },
    created (){
        this.getShowDate();
    },
    computed: {
        chooseDate(){
            const {year, month, day} = this.getYearMonthDay(this.date);

            return `${year}-${+month+1}-${day}`
        },
        showDay() {
            let arrDay = [];
            const {year, month} = this.showToday;
            const start = +new Date(year, +month, 1);
            const week = new Date(start).getDay();
            for(let i = 0; i < 42; i++){
                const num = i - week;
                arrDay.push(new Date(start + num*24*60*60*1000));
            }
            return arrDay;
        }
    },
    data (){
        return {
            show: false,
            showToday: {
                year: 0,
                month: 0,
                day: 0,
            }
        }
    },
    methods: {
        getYearMonthDay(date){
            let year = date.getFullYear();
            let month = date.getMonth();
            let day = date.getDate();
            let second = date.getSeconds();

            return {
                year,
                month,
                day,
                second
            }
        },
        showDate(boolean) {
            this.show = boolean;
        },
        getShowDate() {
            const {year, month, day} = this.getYearMonthDay(this.date);
            this.showToday = {
                year,
                month,
                day
            }
        },
        isCur(date) {
            const chooseDate = new Date(this.chooseDate)
            const {year, month, day} = this.getYearMonthDay(date);
            const {year: curYear, month: curMonth, day: curDay} = this.showToday;
            const {year:chooseYear, month: chooseMonth, day: chooseDay} = this.getYearMonthDay(chooseDate);

            return {
                month: year === curYear && month === curMonth,
                day: year === curYear && month === curMonth && day === curDay,
                select: year === chooseYear && month === chooseMonth && day === chooseDay,
            }
        },
        onChooseDate(date) {
            this.$emit('chooseDate', date)
        },
        chooseMonth(flag) {
            let {year, month} = this.showToday;
            if(flag === 'prev'){
                if(month === 0){
                    month = 12
                    --year
                }
                month = month - 1;
                this.showToday.month = month;
                this.showToday.year = year;
            }else if(flag === 'next'){
                if(month === 11){
                    month = -1
                    ++year
                }
                month = month + 1;
                this.showToday.month = month;
                this.showToday.year = year;
            }
        },
        chooseYear(flag) {
            let {year} = this.showToday;
            if(flag === 'prev'){
                --year
            }else{
                ++year
            }
            this.showToday.year = year;
        }
    }
}
</script>

<style scoped>
@import './assets/font.css';

.allElement{
    display: inline-block;
    position: relative;
}
.header{
    position: relative;
}
.header span{
    position: absolute;
    margin-top: 4px;
    margin-left: 8px;
}
.header input{
    border-radius: 3px;
    height: 20px;
    line-height: 20px;
    padding-left: 30px;
}
.body{
    position: absolute;
    margin-top: 5px;
    width: 250px;
    border-radius: 3px;
    border: 1px solid gray;
    box-shadow: 0 2px 12px 0 gray;
}
.body .arrow{
    position: absolute;
    left: 20px;
    top: -12px;
    width: 0;
    height: 0;
    border: 6px solid transparent;
    border-bottom: 6px solid gray;
}
.body .arrow::after{
    position: absolute;
    left: -6px;
    top: -5px;
    width: 0;
    height: 0;
    content: '';
    border: 6px solid transparent;
    border-bottom: 6px solid white;
}
.body .cHeader{
    display: flex;
    justify-content: space-around;
    align-items: center;
    padding: 5px 0;
}
.body .cHeader .iconfont{
    cursor: pointer;
}
.cBody{
}
.cBody .weeks{
    user-select: none;
    margin: 0 5px;
    display: flex;
    justify-content: space-around;
    border-bottom: 1px solid rgba(6, 6, 6, .1);
    padding-bottom: 5px;
}
.cBody .showDay{
    padding-top: 5px;
    user-select: none;
    display: flex;
    flex-wrap: wrap;
    white-space: normal;
    color: #606166;
    width: 240px;
    margin: 0 auto;
}
.cBody .showDay div{
    width: 30px;
    height: 30px;
    text-align: center;
    line-height: 30px;
    margin: 2px 2px;
    cursor: pointer;
}
.cBody .showDay div.circle{
    width: 30px;
    height: 30px;
    background-color: rosybrown;
    border-radius: 30px;
}
.cBody .showDay div.other-month{
    color: rgba(6, 6, 6, .3);
}
.cBody .showDay div:hover{
    color: skyblue;
}
.cBody .showDay div.today{
    color: rgb(216, 29, 154);
}
</style>