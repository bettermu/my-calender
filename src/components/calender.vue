<template>
    <div class="calender-wrap">
        <div class="c-head">
            <div class="prev-month cp" @click="changeMonth('prev')"> < </div>
            <div class="current-date">{{year}}年{{month}}月</div>
            <div class="next-month cp" @click="changeMonth('next')"> > </div>
        </div>
        <div class="c-week">
            <div class="p-week" v-for="(item,index) in weekArr" >{{item}}</div>
        </div>
        <div class="day-wrap">
            <div class="p-day out-cur" v-for="(item,index) in daysObj.beforeDays"><span>{{item.day}}</span></div>
            <div class="p-day" :class="{'now':isToday(item),'cur':isCurDay(item),'out-cur':isDisabled(item)}" v-for="(item,index) in daysObj.currentDays"><span>{{item.day}}</span></div>
            <div class="p-day out-cur" v-for="(item,index) in daysObj.afterDays"><span>{{item.day}}</span></div>
        </div>
        <div class="option-wrap">
            <span>当天</span>

        </div>
    </div>
</template>

<script>
export default {

    props:{
        num:{
            type:Number,
            default:1
        }
    },

    data(){
        return {
            str:'hh',
            weekArr:['日','一','二','三','四','五','六'],
            year:'',
            month:'',
            day:'',
            nowYear:'',
            nowMonth:'',
            nowDay:'',
            daysObj:{},
            tag:'-',  //日期格式化分割符
            disStart:'',
            disEnd:''
        }
    },

    computed:{
        beginDay(){
            return new Date(this.year,this.month-1,1).getDay()
        },
    },

    created(){
        this.getInitDate()
    },

    methods:{

        //个位数补零操作
        addZero(num){
            return num >9 ? ''+num : '0'+num
        },
        isToday(item){
            return this.dateFormat(this.nowYear,this.addZero(this.nowMonth),this.nowDay,this.tag) === item.format    //this.nowYear === item.year && this.nowMonth === item.month && this.nowDay === item.day
        },
        isCurDay(item){
            return this.dateFormat(this.year,this.addZero(this.month),this.day,this.tag) === item.format //return this.year === item.year && this.month === item.month && this.day === item.day
        },
        isDisabled(item){
            return (new Date(item.format) < new Date(this.disStart) || new Date(item.format) > new Date(this.disEnd))
        },

        //封装日期format（默认使用 - 连接）
        dateFormat(...time){
            return time.slice(0,3).join(time.slice(-1))
        },
        changeMonth(flag){
            if(flag === 'prev'){
                this.month = this.month === 1 ? 12 : --this.month
                this.year = this.month === 12 ? --this.year:this.year
                this.getFirstDay(this.year,this.month)
            }else {
                //下个月
                this.month = this.month === 12? 1:++this.month
                this.year = this.month === 1?++this.year:this.year
                this.getFirstDay(this.year,this.month) 
            }
        },
        getInitDate(){
            const date = new Date()
            this.nowYear = this.year = date.getFullYear()
            this.nowMonth = this.month = date.getUTCMonth() + 1
            this.nowDay = this.day = date.getDate()
            this.getFirstDay(this.year,this.month)
        },

        //获取日期列表
        getFirstDay(year,month){
            //获取该月第一天是周几
            let weekday = new Date(`${year}-${month}-01`).getDay()
            
            let beforeDays = []  //上个月的天数
            let currentDays = []  //当月天数
            let afterDays = []  //下个月天数

            //获取上个月天数
            let days = new Date(year,month-1,0).getDate()
            
            for(let i=0;i<days;i++){
                beforeDays.push({
                    year:year,
                    month: month === 1 ? 12 : month-1,
                    day:i+1,
                    format: this.dateFormat(year,this.addZero(month === 1 ? 12 : month-1),this.addZero(i+1),this.tag)
                })
            }
            //只留下显示的日期
            beforeDays.splice(0,beforeDays.length - weekday)


            //获取当月天数
            let curDays = new Date(year,month,0).getDate()
            //获取当月最后一天是周几

            let afterWeek = new Date(`${year}-${month}-${curDays}`).getDay()

            for(let j = 0;j<curDays;j++){
                currentDays.push({
                    year: year,
                    month: month,
                    day: j + 1,
                    format:this.dateFormat(year,this.addZero(month),this.addZero(j+1),this.tag)
                })
            }
            //当当月天数排完之后，还剩一行多的情况
            
        //    if(beforeDays.length + currentDays.length + 6 - afterWeek === 35 ){

        //        for(let i = 0;i<13 - afterWeek;i++){
        //            afterDays.push({
        //                year:year,
        //                month:month === 12 ? 1 : month +1,
        //                day:i+1,
        //                format: this.dateFormat(year,this.addZero(month === 12 ? 1 : month +1),this.addZero(i+1),this.tag)
        //            })
        //        }

        //    } else if(beforeDays.length + currentDays.length + 6 - afterWeek === 28){ //2026年2月 最后会空两行出来
        //        for(let i = 0;i<20 - afterWeek;i++){
        //            afterDays.push({
        //                year:year,
        //                month:month === 12 ? 1 : month +1,
        //                day:i+1,
        //                format:this.dateFormat(year,this.addZero(month === 12 ? 1 : month +1),this.addZero(i+1),this.tag)
        //            })
        //        }
        //    }else {
        //     for(let i = 0;i<6 - afterWeek;i++){
        //            afterDays.push({
        //                year:year,
        //                month:month === 12 ? 1 : month +1,
        //                day:i+1,
        //                format:this.dateFormat(year,this.addZero(month === 12 ? 1 : month +1),this.addZero(i+1),this.tag)
        //            })
        //        }
        //    }

           for(let i = 0;i<6 - afterWeek;i++){
                   afterDays.push({
                       year:year,
                       month:month === 12 ? 1 : month +1,
                       day:i+1,
                       format:this.dateFormat(year,this.addZero(month === 12 ? 1 : month +1),this.addZero(i+1),this.tag)
                   })
               }

           this.daysObj = {beforeDays,currentDays,afterDays} // [...beforeDays,...currentDays,...afterDays]
           console.log(this.daysObj)
        }
    }
}
</script>

<style lang="less" scoped>
.cp {
    cursor: pointer;
}
    .calender-wrap {
        width:350px;
        margin:0 auto;
        background-color:#fff;
        box-shadow:0 10px 3px -5px #f2f2f2;
        border:1px solid #ddd;
        border-radius:5px;
        .c-head {
            height:30px;
            color:#fff;
            border-bottom:1px solid #ccc;
            display:flex;
            line-height:30px;
            background-color:rgb(127, 201, 243);
            .prev-month {
                flex:1;
            }
            .current-date {
                flex:5
            }
            .next-month {
                flex:1;
            }
        }
        .c-week {
            border-bottom:1px solid #ccc;
            color:rgb(12, 156, 240);
            display:flex;
            justify-content: space-around;
            align-items: center;
            .p-week {
                height:30px;
                
            }
        }
        .day-wrap {
            // border:1px solid #ccc;
            border-bottom:1px solid #ccc;
            display:flex;
            justify-content: space-around;
            flex-wrap:wrap;
            .p-day {
                width:49px;
                height:30px;
                line-height:30px;
                text-align: center;
                span {
                    display:inline-block;
                    height:25px;
                    width:25px;
                    line-height:25px;
                    text-align: center;
                    cursor: pointer;
                }
                &.out-cur {
                    color:#ddd;
                    span {
                        display:none;
                    }
                }
                &.cur {
                    span {
                        color:#fff;
                        background-color:#eee;
                        border-radius:50%;
                    }
                    
                }
                &.now {
                    span {
                        color:#fff;
                        background-color:rgb(243, 92, 92);
                        border-radius:50%;
                    }
                    
                }
                
            }
        }
    }
</style>