<template>
    <div class="calender-wrap">
        <div class="c-head">
            <div class="prev-month cp" @click="changeMonth('prev')"> < </div>
            <div class="current-date">{{year}}年{{month}}月{{day}}日</div>
            <div class="next-month cp" @click="changeMonth('next')"> > </div>
        </div>
        <div class="c-week">
            <div class="p-week" v-for="(item,index) in weekArr" >{{item}}</div>
        </div>
        <div class="day-wrap">
            <div class="p-day" v-for="(item,index) in daysArr">{{item.day}}</div>
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
            daysArr:[]
        }
    },

    computed:{
        beginDay(){
            return new Date(this.year,this.month-1,1).getDay()
        }
    },

    created(){
        this.getInitDate()
    },

    methods:{

        changeMonth(flag){
            if(flag === 'prev'){
                //上个月
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
            this.year = date.getFullYear()
            this.month = date.getUTCMonth() + 1
            this.day = date.getDate()
            this.getFirstDay(this.year,this.month)
        },

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
                    month:month-1,
                    day:i+1
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
                    day: j + 1
                })
            }
            //当当月天数排完之后，还剩一行多的情况
           if(beforeDays.length + currentDays.length + 6 - afterWeek === 35 ){

               for(let i = 0;i<13 - afterWeek;i++){
                   afterDays.push({
                       year:year,
                       month:month + 1,
                       day:i+1
                   })
               }

           } else if(beforeDays.length + currentDays.length + 6 - afterWeek === 28){ //2026年2月 最后会空两行出来
               for(let i = 0;i<20 - afterWeek;i++){
                   afterDays.push({
                       year:year,
                       month:month + 1,
                       day:i+1
                   })
               }
           }else {
            for(let i = 0;i<6 - afterWeek;i++){
                   afterDays.push({
                       year:year,
                       month:month + 1,
                       day:i+1
                   })
               }
           }

           this.daysArr = [...beforeDays,...currentDays,...afterDays]

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
        .c-head {
            height:30px;
            border:1px solid #ccc;
            display:flex;
            line-height:30px;
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
            border:1px solid #ccc;
            
            display:flex;
            justify-content: space-around;
            align-items: center;
            .p-week {
                height:30px;
                
            }
        }
        .day-wrap {
            border:1px solid #ccc;
            display:flex;
            justify-content: space-around;
            flex-wrap:wrap;
            .p-day {
                width:49px;
                height:30px;
                line-height:30px;
            }
        }
    }
</style>