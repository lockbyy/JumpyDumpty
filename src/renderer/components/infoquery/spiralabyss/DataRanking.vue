<template>
    <div id="forth-content-dr">
        <span class="big-title">{{rankingName}}</span>

        <div id="ranking-cards-wrapper">
            <a-empty v-if="spiralAbyss.total_battle_times==0"/>
            <a-row :gutter="[8,24]">
                <div class="ranking-card-wrapper" v-for="(item,i) in ranking" v-if="rankingID=='reveal_rank'">
                    <a-col :xs="24" :sm="24" :md="12" :lg="8" :xl="6" :xxl="4">
                        <a-card class="char-card" hoverable>
                            <img class="char-card-icon" slot="cover" alt="example" :src="item.avatar_icon" />

                            <div class="char-card-container" style="position: relative;">
                                <div class="char-card-title">
                                    {{idToName[item.avatar_id]}}
                                </div>

                                <a-icon type="star" theme="filled"  style="font-size: 12px;color: orange;"
                                    v-for="(item,i) in item.rarity" :key="i" />
                                <div class="char-card-right">
                                    {{item.value}}次
                                </div>
                            </div>

                        </a-card>
                    </a-col>
                </div>

            </a-row>


        </div>



        <div id="ranking-lists-wrapper" v-if="rankingID!='reveal_rank' && spiralAbyss.total_battle_times!=0">

            <a-list item-layout="horizontal" >
                <a-list-item v-for="(item,i) in ranking" :key="i" style="position: relative;">
                    <a-avatar :src="item.avatar_icon" :size="64" />
                    <div class="list-title">
                        {{idToName[item.avatar_id]}}
                    </div>
                    <div class="list-stars">
                        <a-icon type="star" theme="filled"   v-for="(item,i) in item.rarity"
                            style="font-size: 12px;color: orange;" :key="i" />
                    </div>
                    <div class="list-container">

                        <div class="list-value">
                            {{item.value}}
                            <span v-show="rankingID!='damage_rank' && rankingID!='take_damage_rank'">
                                次
                            </span>
                        </div>

                    </div>

                </a-list-item>
            </a-list>

        </div>




        <!-- <div id="char-wrapper">
            <a-card id="char-card" hoverable>

                <img id="char-card-icon" slot="cover" alt="example" :src="character.icon" />
             
            </a-card>
        </div> -->

    </div>
</template>
<style scoped>
    .big-title {
        height: 35px;
        font-size: 28px;
        font-weight: 500;
        margin-bottom: 28px;
        display: block;
        overflow: hidden;
    }

    .title {
        font-size: 20px;
        font-weight: 500;
        margin-bottom: 20px;
        display: inline-block;
    }

    .explain {
        font-weight: 300;
        font-size: 13px;
        margin-bottom: 28px;
        display: block;
    }

    #forth-content-dr {
        padding: 50px;
        padding-top: 30px;
        position: relative;
        width: 100%;
        height: 100%;
        background-color: rgb(245, 245, 245);
        overflow: auto;
        /* z-index: -1; */
    }



    .char-card-icon {
        background-color: rgba(228, 228, 228, 0.363);
    }

    #ranking-cards-wrapper {
        max-width: 1080px;
    }

    .char-card {
        min-width: 150px;
        max-width: 180px;
    }

    .char-card-title {
        margin-bottom: 5px;
        font-size: 16px;
        font-weight: 500;
    }


    .char-card-right {
        position: absolute;
        right: 0px;
        top: 26px;
        font-size: 17px;
        font-weight: 500;
        text-align: right;
    }

    #forth-content-dr::-webkit-scrollbar {
        width: 8px;
        /*高宽分别对应横竖滚动条的尺寸*/
        height: 1px;
    }

    #ranking-lists-wrapper {
        max-width: 760px;
        min-width: 230px;
        overflow: hidden;
    }

    .list-title {
        position: absolute;
        top: 30px;
        left: 80px;
        font-size: 16px;
    }

    .list-stars {
        position: absolute;
        top: 55px;
        left: 80px;
    }

    .list-container {
        position: relative;
        top: 20px;
        font-size: 16px;
        font-weight: 500;
    }

    #forth-content-dr::-webkit-scrollbar-thumb {
        /*滚动条里面小方块*/
        border-radius: 10px;
        box-shadow: inset 0 0 5px rgba(255, 151, 151, 0.2);
        background: #ffababea;

    }

    #forth-content-dr::-webkit-scrollbar-track {
        /*滚动条里面轨道*/
        box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
        border-radius: 10px;
        background: #EDEDED;

    }
</style>

<script>
    import axios from 'axios'

    export default {

        data() {
            return {
                rankingID: this.$route.params.value,
                rankingName: '未知',
                ranking: [],
                idToName: {},
                charsInfo: [],
                spiralAbyss:{}
                        }
        },
        mounted() {
            this.getData()
            // this.initData()
        },
        methods: {
            getData() {
                axios.get('../../../../data/userInfo.json').then(res => {
                    if (res.status === 200) {
                        this.charsInfo = res.data.data.avatars
                    }
                })
                axios.get('../../../../data/spiralAbyssInfo.json').then(res => {
                    if (res.status === 200) {
                        this.spiralAbyss = res.data.data
                        this.initData()
                    }
                })
            },
            initData() {
                if (this.rankingID == 'reveal_rank') {
                    this.rankingName = '出战次数'
                } else if (this.rankingID == 'defeat_rank') {
                    this.rankingName = '击破数'
                } else if (this.rankingID == 'damage_rank') {
                    this.rankingName = '最大伤害'
                } else if (this.rankingID == 'take_damage_rank') {
                    this.rankingName = '承受伤害'
                } else if (this.rankingID == 'normal_skill_rank') {
                    this.rankingName = '元素战技次数'
                } else if (this.rankingID == 'energy_skill_rank') {
                    this.rankingName = '元素爆发次数'
                } else {
                    this.rankingName = "未知"
                }
                this.ranking = this.spiralAbyss[this.rankingID]

                for (let item of this.charsInfo) {
                    this.idToName[item.id] = item.name
                }
                // console.log(this.idToName)
            }
        }


    };
</script>