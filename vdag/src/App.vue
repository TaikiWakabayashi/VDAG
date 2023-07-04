<template>
  <div class="container">
    <h1 class="title">VDAG</h1>
    <p class="subTitle">Variable Definition Automatic Generation</p>
    <div id="selectOption">
      <div class="wrapper">
        <!-- アクセス修飾子の選択 -->
        <label class="label" for=""
          >アクセス修飾子
          <input type="checkbox" v-model="accessCheck" />
        </label>
        <div id="accessibility" class="subContainer">
          <div>
            <label v-for="access in accessArr" :key="access">
              <input
                type="radio"
                :disabled="!accessCheck"
                :value="access"
                v-model="accessRadio"
                class="sSpace"
              />{{ access }}
            </label>
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- 変数・定数定義の選択 -->
        <label class="label" for=""
          >変数宣言
          <input type="checkbox" v-model="varCheck" />
        </label>
        <div id="varDeclaration" class="subContainer">
          <div>
            <label v-for="varItem in varArr" :key="varItem">
              <input type="radio" :disabled="!varCheck" :value="varItem" v-model="varRadio" />{{
                varItem
              }}
            </label>
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- 型の選択 -->
        <label class="label" for=""
          >型1
          <input type="checkbox" v-model="dataTypeCheck" />
        </label>
        <div id="data" class="subContainer">
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
      </div>

      <div class="wrapper">
        <!-- 型の選択2 -->
        <label class="label" for=""
          >型2
          <input type="checkbox" v-model="dataTypeCheck" />
        </label>
        <div id="data" class="subContainer">
          <div>
            <label v-for="dataType in DataTypeArr2" :key="dataType">
              <input
                type="radio"
                :disabled="!dataTypeCheck"
                :value="dataType"
                v-model="dataTypeRadio"
              />{{ dataType }}
            </label>
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- 型（カスタム） -->
        <label class="label" for="">型（カスタム）） </label>
        <div id="data" class="subContainer">
          <div>
            <input type="text" />
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- 配列の選択 -->
        <label class="label" for=""
          >配列
          <input type="checkbox" v-model="dataTypeCheck" />
        </label>
        <div id="data" class="subContainer">
          <div>
            <label v-for="(arrFlg, index) in arrayFlgArr" :key="index">
              <input
                type="radio"
                :disabled="!dataTypeCheck"
                :value="arrFlg"
                v-model="dataTypeRadio"
              />{{ arrFlg }}
            </label>
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- $付け -->
        <label class="label" for=""
          >$付け
          <input type="checkbox" v-model="otherCheck" />
        </label>
        <div id="other" class="subContainer">
          <div>
            <label v-for="(doll, index) in dollar" :key="index">
              <input type="radio" :disabled="!otherCheck" :value="doll" v-model="othersChecks" />{{
                doll
              }}
            </label>
          </div>
        </div>
      </div>

      <div class="wrapper">
        <!-- その他の選択 -->
        <label class="label" for=""
          >その他
          <input type="checkbox" v-model="otherCheck" />
        </label>
        <div id="other" class="subContainer">
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
      </div>
    </div>

    <!-- エラーゾーン -->
    <div class="errorZone">
      <p class="text-danger" :class="{ displayNone: selectCheck }">その他を選択してください！！</p>
      <p class="text-danger" :class="{ displayNone: dupAccessCheck }">
        同じアクセス修飾子が存在します！！
      </p>
      <p class="text-danger" :class="{ displayNone: dupVarCheck }">同じ変数宣言が存在します！！</p>
      <p class="text-danger" :class="{ displayNone: dupDataTypeCheck }">同じ型が存在します！！</p>
      <p class="text-danger" :class="{ displayNone: dupOtherCheck }">
        同じその他設定が存在します！！
      </p>
    </div>

    <div class="text-center btnContainer">
      <button class="btn btn-primary" @click="selectOptions">追加</button>
      <button class="btn btn-primary" @click="deleteArr">全削除</button>
    </div>

    <div class="listContainer">
      <p class="label">追加リスト</p>
      <span class="text-info short disc">ドラッグ&ドロップで順番を変更できます</span>
      <div class="listZone">
        <ul class="optionList">
          <li
            v-for="(option, index) in checkList"
            :key="option"
            class="btn btn-primary optionItem"
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
    </div>

    <!-- 変数入力 -->
    <div class="varContainer">
      <label class="label">変数名</label>
      <span class="text-info short disc"
        >※命名規則を反映する場合は、スペース区切りで入力してください</span
      >
      <div class="subContainer">
        <input type="text" v-model="inputText" />
        <button class="btn btn-primary" @click="addVarName" :disabled="!isAddVarNameOn">
          追加
        </button>
      </div>
    </div>

    <div class="varNameListContainer">
      <!-- 追加リスト -->
      <p class="label">追加変数リスト</p>
      <ul class="subContainer">
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
    </div>

    <div>
      <!-- モード -->
      <div>
        <p class="label">命名規則</p>
        <div class="subContainer">
          <label v-for="mode in namingConvention" :key="mode">
            <input type="radio" v-model="namingConversationCheck" :value="mode" /> {{ mode }}</label
          >
        </div>
      </div>
      <div class="text-center btnContainer">
        <button class="btn btn-primary" @click="generate" :disabled="!isGenerateOn">生成</button>
        <button class="btn btn-primary" @click="clear">クリア</button>
      </div>
    </div>

    <!-- 完成 -->
    <div class="resultContainer">
      <p class="label">完成品</p>
      <div class="subContainer">
        <p v-for="item in result" :key="item">{{ item }}</p>
      </div>
    </div>
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
      DataTypeArr2: [
        'String',
        'Number',
        'Boolean',
        'Map',
        'Array',
        'Function',
        'Math',
        'Date',
        'Integer'
      ] as string[],
      customDataType: '' as string,
      othersArr: ['static', 'main', 'readonly'] as string[],
      arrayFlgArr: ['あり', 'なし'] as string[],
      dollar: ['あり', 'なし'] as string[],
      namingConvention: [
        'なし',
        'キャメルケース',
        'ケバブケース',
        'パスカルケース',
        'スネークケース'
      ] as string[],
      namingConversationCheck: 'なし' as string,
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
      selectCheck: true as boolean,
      dupAccessCheck: true as boolean,
      dupVarCheck: true as boolean,
      dupDataTypeCheck: true as boolean,
      dupOtherCheck: true as boolean,
      isGenerateOn: false as boolean,
      arrayFlg: false as boolean
    }
  },
  methods: {
    selectOptions() {
      try {
        if (this.otherCheck === true) {
          if (this.othersChecks.length == 0) {
            this.selectCheck = false
            throw new Error('その他を選択してください')
          } else {
            for (let other of this.othersChecks) {
              if (this.checkList.indexOf(other) != -1) {
                this.dupOtherCheck = false
                this.otherCheck = false
                throw new Error('同じその他設定が既に存在します')
              } else {
                this.checkList.push(other)
                this.selectCheck = true
              }
            }
          }
        }
        if (this.accessCheck === true) {
          if (this.checkList.indexOf(this.accessRadio) != -1) {
            this.dupAccessCheck = false
            this.accessCheck = false
            throw new Error('同じアクセス修飾子が既に存在します')
          } else {
            this.checkList.push(this.accessRadio)
            this.dupAccessCheck = true
          }
        }
        if (this.varCheck === true) {
          if (this.checkList.indexOf(this.varRadio) != -1) {
            this.dupVarCheck = false
            this.varCheck = false
            throw new Error('同じ変数宣言が存在します')
          } else {
            this.checkList.push(this.varRadio)
            this.dupVarCheck = true
          }
        }
        if (this.dataTypeCheck === true) {
          if (this.checkList.indexOf(this.dataTypeRadio) != -1) {
            this.dupDataTypeCheck = false
            this.dataTypeCheck = false
            throw new Error('同じ型が存在します')
          } else {
            this.checkList.push(this.dataTypeRadio)
            this.dupDataTypeCheck = true
          }
        }
        ;(this.accessCheck = false),
          (this.varCheck = false),
          (this.dataTypeCheck = false),
          (this.otherCheck = false),
          (this.dupAccessCheck = true),
          (this.dupVarCheck = true),
          (this.dupDataTypeCheck = true),
          (this.dupOtherCheck = true)
      } catch (error: any) {
        console.log(error.message)
      }
    },
    deleteArr() {
      ;(this.otherCheck = false),
        (this.selectCheck = true),
        (this.dupAccessCheck = true),
        (this.dupVarCheck = true),
        (this.dupDataTypeCheck = true),
        (this.dupOtherCheck = true)
      this.checkList.splice(0)
      this.othersChecks.splice(0)
    },
    addVarName() {
      const newVarNameObj: varNameType = {
        id: this.varNameList.length,
        varName: this.inputText,
        editFlg: false,
        insertFlg: true,
        disabledFlg: false
      }
      this.varNameList.push(newVarNameObj)
      this.inputText = ''
      this.isGenerateOn = true
    },
    generate() {
      let defaultVar: string = ''
      const varNameList: varNameType[] = this.varNameList
      for (let check of this.checkList) {
        defaultVar += check + ' '
      }
      for (let varNameObj of varNameList) {
        if (this.namingConversationCheck == 'なし') {
          this.result.push(defaultVar + varNameObj.varName)
        } else if (this.namingConversationCheck == 'キャメルケース') {
          const name: string = varNameObj.varName
          const replaceName: string = name.replace(/\s./g, (searchStr) => {
            return searchStr.charAt(1).toUpperCase()
          })
          this.result.push(defaultVar + replaceName)
        } else if (this.namingConversationCheck == 'ケバブケース') {
          const name: string = varNameObj.varName
          const replaceName: string = name.replace(/\s/g, '-')
          this.result.push(defaultVar + replaceName)
        } else if (this.namingConversationCheck == 'パスカルケース') {
          const name: string = varNameObj.varName
          const initial: string = varNameObj.varName.charAt(0).toUpperCase()
          // キャメルケースを一度生成
          const replaceName: string = name.replace(/\s./g, (searchStr) => {
            return searchStr.charAt(1).toUpperCase()
          })
          // パスカルケースを生成
          const pascal: string = replaceName.replace(replaceName.charAt(0), initial)
          this.result.push(defaultVar + pascal)
        } else if (this.namingConversationCheck == 'スネークケース') {
          const name: string = varNameObj.varName
          const replaceName: string = name.replace(/\s/g, '_')
          this.result.push(defaultVar + replaceName)
        }
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
        } else {
          return { ...x }
        }
      })
      this.varNameList = x

      let newVarName: string = ''
      if (this.editingVarName == '') {
        newVarName = varNameObj.varName
      } else {
        newVarName = this.editingVarName
      }
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
        } else {
          x.disabledFlg = true
          return { ...x }
        }
      })
      this.varNameList = x
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
html,
body {
  height: 100%;
  font-size: 62.5%;
  padding: 0;
  margin: 0;
  font-family: 'Sawarabi Gothic', sans-serif;
}
label {
  padding-right: 10px;
  margin: 0;
}
input {
  margin: 0 5px;
  vertical-align: middle;
}
button {
  margin: 0 10px;
}
#selectOption {
  margin: 50px 0 30px 0;
  display: grid;
  grid-template-columns: 1fr 25px 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
}
.wrapper {
  width: 100%;
}
.wrapper:nth-of-type(1) {
  grid-row: 1/2;
  grid-column: 1/2;
}
.wrapper:nth-of-type(2) {
  grid-row: 1/2;
  grid-column: 3/4;
}
.wrapper:nth-of-type(3) {
  grid-row: 2/3;
  grid-column: 1/2;
}
.wrapper:nth-of-type(4) {
  grid-row: 2/3;
  grid-column: 3/4;
}
.wrapper:nth-of-type(5) {
  grid-row: 3/4;
  grid-column: 1/2;
}
.wrapper:nth-of-type(6) {
  grid-row: 3/4;
  grid-column: 3/4;
}
.wrapper:nth-of-type(7) {
  grid-row: 4/5;
  grid-column: 1/2;
}
.wrapper:nth-of-type(8) {
  grid-row: 4/5;
  grid-column: 3/4;
}
.subContainer {
  width: 100%;
  border: 1px solid #007bff;
  border-radius: 10px;
  margin-bottom: 45px;
  padding: 20px;
}
.listContainer {
  width: 100%;
  position: relative;
}
.varContainer {
  width: 100%;
  position: relative;
}
.varNameListContainer {
  widows: 100%;
}
.disc {
  position: absolute;
  top: 12%;
  left: 180px;
}
.title,
.subTitle {
  text-align: center;
}
.title {
  font-size: 6rem;
}
.subTitle {
  letter-spacing: 3px;
  font-size: 1rem;
}
.errorZone {
  width: 100%;
  /* padding: 20px; */
}
.errorZone p {
  margin: 0;
}
.btnContainer {
  margin: 20px 0;
}
.optionList {
  width: 100%;
  display: flex;
  justify-content: center;
  list-style: none;
  padding: 0;
  margin: 0;
}
.optionItem {
  margin: 0 10px;
}
.listZone {
  width: 100%;
  margin-bottom: 50px;
  padding: 30px 0;
  border: 1px solid #007bff;
  border-radius: 10px;
}
.label {
  font-size: 13px;
  width: 150px;
  height: 50px;
  line-height: 50px;
  margin: 0;
  margin-left: 15px;
  text-align: center;
  background-color: #007bff;
  color: white;
  border-radius: 8px 8px 0 0;
}
.short {
  font-size: 0.8rem;
}
.displayNone {
  display: none;
}
</style>
