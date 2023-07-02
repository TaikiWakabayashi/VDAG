<template>
  <div>
    <div id="selectOption">
      <!-- アクセス修飾子の選択 -->
      <div id="accessibility">
        <label for=""
          >アクセス修飾子
          <input type="checkbox" v-model="accessCheck" />
        </label>
        <div>
          <label v-for="access in accessArr" :key="access">
            <input type="radio" :disabled="!accessCheck" :value="access" v-model="accessRadio" />{{
              access
            }}
          </label>
        </div>
      </div>

      <!-- 変数・定数定義の選択 -->
      <div id="varDeclaration">
        <label for=""
          >変数宣言
          <input type="checkbox" v-model="varCheck" />
        </label>
        <div>
          <label v-for="varItem in varArr" :key="varItem">
            <input type="radio" :disabled="!varCheck" :value="varItem" v-model="varRadio" />{{
              varItem
            }}
          </label>
        </div>
      </div>

      <!-- データ型の選択 -->
      <div id="data">
        <label for=""
          >データ型
          <input type="checkbox" v-model="dataTypeCheck" />
        </label>
        <div>
          <label v-for="dataType in DataTypeArr" :key="dataType">
            <input
              type="radio"
              :disabled="!dataTypeCheck"
              :value="dataType"
              v-model="dataTypeRadio"
            />{{ dataType }}
          </label>
        </div>
      </div>

      <!-- その他の選択 -->
      <div id="other">
        <label for=""
          >その他
          <input type="checkbox" v-model="otherCheck" />
        </label>
        <div>
          <label v-for="other in othersArr" :key="other">
            <input
              type="checkbox"
              :disabled="!otherCheck"
              :value="other"
              v-model="othersCheckList"
            />{{ other }}
          </label>
        </div>
      </div>

      <!-- 変数入力 -->
      <label
        >変数名
        <input type="text" v-model="inputText" />
      </label>
      <br />
      <button @click="selectOptions">追加</button>
    </div>

    <div>
      <ul class="button-list">
        <li v-for="option in checkList" :key="option" class="btn btn-primary">{{ option }}</li>
      </ul>
    </div>
    <button @click="deleteArr()">削除</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      inputText: '',
      checkList: [],
      result: [],
      optionsArr: [],
      accessArr: ['public', 'protected', 'private'],
      varArr: ['var', 'let', 'const'],
      DataTypeArr: [
        'string',
        'int',
        'boolean',
        'number',
        'byte',
        'short',
        'char',
        'long',
        'float',
        'double',
        'symbol',
        'bigint',
        'void'
      ],
      othersArr: ['static', 'main'],
      loopNum: 1,
      accessRadio: 'public',
      varRadio: 'var',
      dataTypeRadio: 'string',
      othersCheckList: [],
      accessCheck: false,
      varCheck: false,
      dataTypeCheck: false,
      otherCheck: false
    }
  },
  methods: {
    selectOptions() {
      if (this.accessCheck === true) {
        this.checkList.push(this.accessRadio)
      }
      if (this.varCheck === true) {
        this.checkList.push(this.varRadio)
      }
      if (this.dataTypeCheck === true) {
        this.checkList.push(this.dataTypeRadio)
      }
      if (this.otherCheck === true) {
        for (let other of this.othersArr) {
          this.checkList.push(other)
        }
      }
      this.checkList.push(this.inputText)
      ;(this.accessCheck = false),
        (this.varCheck = false),
        (this.dataTypeCheck = false),
        (this.otherCheck = false)
    },
    deleteArr() {
      this.checkList.splice(0)
    }
  }
  // computed: {
  //   selectOptions() {
  //     return ''
  //   }
  // }
})
</script>

<style scoped>
.button-list {
  display: flex;
  list-style: none;
}
</style>
