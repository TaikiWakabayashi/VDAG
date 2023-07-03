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
              v-model="othersChecks"
            />{{ other }}
          </label>
        </div>
      </div>
      <p class="text-danger" :class="{ displayNone: errorCheck }">選択してください！！</p>
      <br />
      <button class="btn btn-primary" @click="selectOptions">追加</button>
      <button class="btn btn-primary" @click="deleteArr">削除</button>
    </div>

    <div>
      <ul class="button-list">
        <li
          v-for="(option, index) in checkList"
          :key="option"
          class="btn btn-primary"
          :draggable="true"
          @dragstart="dragStart(index)"
          @dragenter="dragEnter(index)"
          @dragover.prevent
          @dragend="dragEnd"
        >
          {{ option }}
        </li>
      </ul>
    </div>

    <!-- 変数入力 -->
    <div>
      <label
        >変数名
        <input type="text" v-model="inputText" />
      </label>
      <button class="btn btn-primary" @click="addVarName" :disabled="!isAddVarNameOn">追加</button>
    </div>

    <!-- 追加リスト -->
    <div>
      <p>追加リスト</p>
      <ul>
        <li v-for="(varNameObj, index) in varNameList" :key="varNameObj.id">
          <input
            type="text"
            :disabled="!varNameObj.editFlg"
            :value="varNameObj.varName"
            @change="editVarName"
          />
          <button
            class="btn btn-primary"
            :class="{ displayNone: varNameObj.insertFlg }"
            @click="insertVar(varNameObj, index)"
          >
            完了
          </button>
          <button
            class="btn btn-primary"
            :disabled="varNameObj.disabledFlg"
            :class="{ displayNone: varNameObj.editFlg }"
            @click="editing(varNameObj.id)"
          >
            編集
          </button>
          <button class="btn btn-primary" @click="deleteVar(index)">削除</button>
        </li>
      </ul>
      <button class="btn btn-primary" @click="generate" :disabled="!isGenerateOn">生成</button>
      <!-- <button class="btn btn-primary" @click="editVar" :disabled="!isGenerateOn">編集</button> -->
    </div>
    <p v-for="item in result" :key="item">{{ item }}</p>
    <button class="btn btn-primary" @click="clear">クリア</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

type varNameType = {
  id: number
  varName: string
  editFlg: boolean
  insertFlg: boolean
  disabledFlg: boolean
}

export default defineComponent({
  data() {
    return {
      inputText: '' as string,
      checkList: [] as string[],
      result: [] as string[],
      optionsArr: [] as string[],
      accessArr: ['public', 'protected', 'private'] as string[],
      varArr: ['var', 'let', 'const'] as string[],
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
      ] as string[],
      othersArr: ['static', 'main', 'readonly'] as string[],
      varNameList: [] as varNameType[],
      loopNum: 1 as number,
      selectIndex: 0 as number,
      accessRadio: 'public' as string,
      varRadio: 'var' as string,
      dataTypeRadio: 'string' as string,
      othersChecks: [] as string[],
      editingVarName: '' as string,
      accessCheck: false as boolean,
      varCheck: false as boolean,
      dataTypeCheck: false as boolean,
      otherCheck: false as boolean,
      errorCheck: true as boolean,
      isGenerateOn: false as boolean
    }
  },
  methods: {
    selectOptions() {
      try {
        if (this.otherCheck === true) {
          if (this.othersChecks.length == 0) {
            throw new Error('その他を選択してください')
          } else {
            for (let other of this.othersChecks) {
              this.checkList.push(other)
            }
            this.errorCheck = true
          }
        }
        if (this.accessCheck === true) {
          this.checkList.push(this.accessRadio)
        }
        if (this.varCheck === true) {
          this.checkList.push(this.varRadio)
        }
        if (this.dataTypeCheck === true) {
          this.checkList.push(this.dataTypeRadio)
        }
        ;(this.accessCheck = false),
          (this.varCheck = false),
          (this.dataTypeCheck = false),
          (this.otherCheck = false)
      } catch (error: any) {
        this.errorCheck = false
        console.log(error.message)
      }
    },
    deleteArr() {
      this.checkList.splice(0)
      this.othersChecks.splice(0)
    },
    addVarName() {
      console.log(this.varNameList)
      const newVarNameObj: varNameType = {
        id: this.varNameList.length,
        varName: this.inputText,
        editFlg: false,
        insertFlg: true,
        disabledFlg: false
      }
      this.varNameList.push(newVarNameObj)
      console.log(this.varNameList)
      this.inputText = ''
      this.isGenerateOn = true
    },
    generate() {
      let defaultVar = ''
      const varNameList: varNameType[] = this.varNameList
      for (let check of this.checkList) {
        defaultVar += check + ' '
      }
      for (let varNameObj of varNameList) {
        this.result.push(defaultVar + varNameObj.varName)
      }
    },
    clear() {
      this.result.splice(0)
    },
    dragStart(index: number) {
      console.log('drag start : ' + index)
      this.selectIndex = index
    },
    dragEnter(index: number): void {
      if (index === this.selectIndex) {
        return
      }
      const deleteElement = this.checkList.splice(this.selectIndex, 1)[0]
      this.checkList.splice(index, 0, deleteElement)
      this.selectIndex = index
    },
    dragEnd() {
      this.selectIndex = 0
    },
    insertVar(varNameObj: varNameType, index: number) {
      const x = this.varNameList.map((x) => {
        if (varNameObj.id != x.id) {
          x.disabledFlg = false
          return { ...x }
          // return { ...x, disabledFlg: false }
        } else {
          return { ...x }
        }
      })
      this.varNameList = x
      const newVarName: string = this.editingVarName
      const editVarNameObj: varNameType = {
        ...varNameObj,
        varName: newVarName,
        editFlg: false,
        insertFlg: true,
        disabledFlg: false
      }
      this.varNameList.splice(index, 1, editVarNameObj)
      this.editingVarName = ''
    },
    editing(id: number) {
      const x = this.varNameList.map((x) => {
        if (id == x.id) {
          x.editFlg = true
          x.insertFlg = false
          return { ...x }
          // return { ...x, editFlg: true, insertFlg: false }
        } else {
          x.disabledFlg = true
          return { ...x }
          // return { ...x, disabledFlg: true }
        }
      })
      this.varNameList = x
      console.log(this.varNameList)
    },
    deleteVar(index: number) {
      this.varNameList.splice(index, 1)
    },
    editVarName(event: Event) {
      event.preventDefault()
      const target = event.target as HTMLInputElement
      this.editingVarName += target.value
    }
  },
  computed: {
    isAddVarNameOn() {
      if (
        this.checkList.length == 0 ||
        this.inputText === null ||
        this.inputText === undefined ||
        this.inputText === ''
      ) {
        return false
      } else {
        return true
      }
    }
  }
})
</script>

<style scoped>
.button-list {
  display: flex;
  list-style: none;
}

.displayNone {
  display: none;
}
</style>
