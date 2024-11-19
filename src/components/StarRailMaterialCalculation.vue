<template>
    <h2>Start Material</h2>
    <div class="input-box">
        <div class="el">
            Lv3: <input v-model="material_3" type="number" min="0">
        </div>
        <div class="el">
            Lv2: <input v-model="material_2" type="number" min="0">
        </div>
        <div class="el">
            Lv1: <input v-model="material_1" type="number" min="0">
        </div>
    </div>
    <hr>

    <h2>Target Material</h2>
    <div class="input-box">
        <div class="el">
            Lv3: <input v-model="target_material_3" type="number" min="0">
        </div>
        <div class="el">
            Lv2: <input v-model="target_material_2" type="number" min="0">
        </div>
        <div class="el">
            Lv1: <input v-model="target_material_1" type="number" min="0">
        </div>
    </div>
    <hr>

    <h2>Crafted Times</h2>
    <div class="click-times-box">
        Lv2 Click Times: {{ Lv2ct() }}
        <br>
        Lv3 Click Times: {{ Lv3ct() }}
    </div>
    <hr>

    <h2>Crafted Result</h2>
    <div class="result-box">
        <div class="el">
            Lv3: <span class="result-display">{{ RdLv3() }}</span>
        </div>
        <div class="el">
            Lv2: <span class="result-display">{{ RdLv2() }}</span>
        </div>
        <div class="el">
            Lv1: <span class="result-display">{{ RdLv1() }}</span>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
let material_3 = ref(0)
let material_2 = ref(0)
let material_1 = ref(0)

let target_material_3 = ref(154)
let target_material_2 = ref(81)
let target_material_1 = ref(22)

let Lv3ct = function() {
    let obj = calcClickTimes()
    // console.log("Lv3ct:", obj)
    return obj.needClick.Lv3
}

let Lv2ct = function() {
    let obj = calcClickTimes()
    // console.log("Lv2ct:", obj)
    return obj.needClick.Lv2
}

let RdLv3 = function() {
    let obj = calcClickTimes()
    return obj.diff.Lv3 + target_material_3.value
}
let RdLv2 = function() {
    let obj = calcClickTimes()
    return obj.diff.Lv2 + target_material_2.value
}
let RdLv1 = function() {
    let obj = calcClickTimes()
    return obj.diff.Lv1 + target_material_1.value
}

function calcClickTimes() {
    // 用于存储中间计算结果
    let obj = {
        diff: {
            Lv1: 0,
            Lv2: 0,
            Lv3: 0
        },
        lack: {
            Lv1: 0,
            Lv2: 0,
            Lv3: 0
        },
        needClick: {
            Lv2: 0,
            Lv3: 0
        }
    }
    obj.diff.Lv1 = material_1.value - target_material_1.value
    if (obj.diff.Lv1 < 0) {
        obj.lack.Lv1 = Math.abs(obj.diff.Lv1)
    }
    else {
        obj.lack.Lv1 = 0
    }
  
    obj.diff.Lv2 = material_2.value - target_material_2.value
    if (obj.diff.Lv2 < 0) {
        obj.lack.Lv2 = Math.abs(obj.diff.Lv2)
    }
    else {
        obj.lack.Lv2 = 0
    }

    obj.diff.Lv3 = material_3.value - target_material_3.value
    if (obj.diff.Lv3 < 0) {
        obj.lack.Lv3 = Math.abs(obj.diff.Lv3)
    }
    else {
        obj.lack.Lv3 = 0
    }

    // console.log('obj.diff', obj.diff)
    // console.log('obj.lack', obj.lack)

    // 先计算3阶材料
    // 先使用2阶材料合成
    if (obj.diff.Lv2 >= 0) {
        let intDiv2to3 = Math.floor(obj.diff.Lv2 / 3)
        // 如果三级材料缺少数量小于二阶材料溢出所能合成的三级材料数量
        // lack.Lv3 = 61, intDiv2to3 = 154 // 3 = 51
        // console.log(obj.lack.Lv3, intDiv2to3)
        if (obj.lack.Lv3 <= intDiv2to3) {
            obj.needClick.Lv3 += obj.lack.Lv3
            // 
            obj.diff.Lv2 -= obj.lack.Lv3 * 3
            // 
            obj.lack.Lv3 -= obj.lack.Lv3

            obj.diff.Lv3 += obj.lack.Lv3
        }
        else {
            // needClick.Lv3 = 0, intDiv2to3 = 51
            obj.needClick.Lv3 += intDiv2to3
            // needClick.Lv3 = 51
            obj.diff.Lv2 -= intDiv2to3 * 3
            // diff.Lv2 = 154 - 153 = 1
            // lack.Lv3 = 61
            obj.diff.Lv3 += intDiv2to3
            // diff.Lv3 = -61 + 51 = -10
            obj.lack.Lv3 -= intDiv2to3
            // lack.Lv3 = 61 - 51 = 10
        }
    }
    
    if (obj.diff.Lv1 >= 0) {
        // 然后使用1阶材料合成
        let intDiv1to3 = Math.floor(obj.diff.Lv1 / 9)
        // lack.Lv3 = 10, diff.Lv1 = 159, intDiv1to3 = 159 // 9 = 21
        if (obj.lack.Lv3 <= intDiv1to3) {
            // needClick.Lv3 = 51
            obj.needClick.Lv3 += obj.lack.Lv3
            // needClick.Lv3 = 51 + 10 = 61
            obj.needClick.Lv2 += obj.lack.Lv3 * 3
            // needClick.Lv2 = 0 + 10 * 3 = 30
            obj.diff.Lv1 -= obj.lack.Lv3 * 9
            // diff.Lv1 = 159 - 10 * 9 = 69
            obj.diff.Lv3 += obj.lack.Lv3
            // diff.Lv3 = -10 + 10 = 0
            obj.lack.Lv3 -= obj.lack.Lv3
            // lack.Lv3 = 10 - 10 = 0
        }
        else {
            obj.needClick.Lv3 += intDiv1to3
            obj.needClick.Lv2 += intDiv1to3 * 3
            obj.diff.Lv1 -= intDiv1to3 * 9
            obj.diff.Lv3 += intDiv1to3
            obj.lack.Lv3 -= intDiv1to3
        }
    }
    // console.log(obj)
    
    if (obj.diff.Lv1 >= 0) {
        // 2阶材料计算
        let intDiv1to2 = Math.floor(obj.diff.Lv1 / 3)
        if (obj.lack.Lv2 <= intDiv1to2) {
            obj.needClick.Lv2 += obj.lack.Lv2
            obj.diff.Lv1 -= obj.lack.Lv2 * 3
            obj.diff.Lv2 += obj.lack.Lv2
            obj.lack.Lv2 -= obj.lack.Lv2
        }
        else {
            obj.needClick.Lv2 += intDiv1to2
            obj.diff.Lv1 -= intDiv1to2 * 3
            obj.diff.Lv2 += intDiv1to2
            obj.lack.Lv2 -= intDiv1to2
        }
    }

    return obj
    
}
</script>

<style scoped>
.input-box {
    display: flex;
    flex-flow: row wrap;
    min-width: 20%;
    margin-bottom: 5px;

    .el input {
        outline: none;
        width: 80px;
        border-radius: 5px;
        margin: 0 5px;
        padding: 1px;
    }
}

.result-box {
    display: flex;
    flex-flow: row wrap;
    min-width: 20%;
    margin-bottom: 5px;
    
    .el {
        margin-bottom: 5px;
    }

    span {
        color: black;
        display: inline-flex;
        width: 80px;
        border-radius: 5px;
        margin: 0 5px;
        padding: 1px;
        background-color: aqua;
    }
}
</style>