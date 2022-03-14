<template>
  <div class="wrapper">
      <div class="container">
        <div ref="shadow" class="shadow">
          <div ref="shadowContent" class="shadow-content">
            <span>{{ shadowText.before }}</span
            ><a-dropdown
                v-if="shadowText.content"
                :visible="popup.visible"
              ><span ref="position" class="text-position"
                >{{ shadowText.content }}</span
              >
              <template #overlay>
                <!-- <div > -->
                    <a-menu class="overlay-container">
                        <a-menu-item
                        @click="clickUser(item)"
                        class="popup-item"
                        v-for="(item, index) in filterUsers"
                        :key="item.id || index"
                      >
                        {{ item.name }}
                      </a-menu-item>

                    </a-menu>

                <!-- </div> -->


              </template>
            </a-dropdown>
            <span>{{ shadowText.after }}</span>
          </div>
        </div>
        <textarea
          ref="textarea"
          @beforeinput="beforeinput"
          id="textarea"
          :value="textareaValue"
          @input="input"
          rows="10"
          cols="50"
          class="area"
        />
      </div>

  </div>

</template>
<style lang="less" scoped>
.base {
  font-size: 12px;
  font-size: 12px;
  line-height: 1.2;
  vertical-align: middle;
  word-break: break-word;
  word-wrap: break-word;
  white-space: pre-wrap;
  outline: 0;
//   background-color: #f6f6f6;
  box-sizing: border-box;
  -webkit-appearance: none;
  font-family: "Times New Roman", Georgia, Serif;
  resize: none;
  border: none;
}
.wrapper {
    border: 1px #555 solid;
    border-radius: 5px;
    padding: 5px;
    display: inline-block;
}

.container {
  position: relative;
  display: inline-block;
  word-wrap: break-word;
  text-align: left;

}
.shadow {
  position: absolute;
  top: 0;
  bottom: 0;
//   top: 100%;
//   bottom: -100%;
  left: 0;
  right: 0;
  overflow: auto;
}

.shadow-content {
  width: 100%;
  .base;
}
.text-position {
  position: relative;
//   color: #0ff;
}

.overlay-container {
    max-height: 200px;
    max-width: 100px;
    overflow: auto;

}
.area {
  resize: none;
  opacity: .9;
  display: block;
  padding: 0;
  margin: 0;
  .base;
}
</style>
<script>
import { defineComponent, toRaw } from "vue";
import {cloneDeep} from 'lodash'
export default defineComponent({
  props: {
    users: {
      type: Array,
      default() {
        return [
        //   {
        //     name: "1212",
        //     id: "dsdfds",
        //     img: "dfds",
        //   },

        ];
      },
    },
  },
  data() {
    return {
      textareaValue: "",
      beforeSelection: {
        start: 0,
        end: 0,
      },
      afterSelection: {
        start: 0,
        end: 0,
      },
      // users: [],
      oUsers: [],
      popup: {
        visible: false,
        position: -1,
        name: "",
        placement: "bottomRight"
      },
    };
  },
  mounted() {
    let textarea = this.$refs.textarea;
    textarea &&
      textarea.addEventListener("scroll", (e) => {
        this.$refs.shadow.scrollTop = textarea.scrollTop;
      });
  },
  watch: {
      textareaValue() {
          this.$nextTick(() => {
              try {
                  let {left, top} = this.$refs.position?.getBoundingClientRect()
                this.popup.placement = `${top> 200 ? 'top': 'bottom'}${left> 100? 'Left': 'Right'}`
              } catch(e) {
                  console.warn(e)
              }
          })
      }
  },
  computed: {
    shadowText() {
      if (this.popup.position < 0) {
        return {
          before: this.textareaValue,
        };
      }
      let before = this.textareaValue.slice(0, this.popup.position);
      let content = this.textareaValue.slice(
        this.popup.position,
        this.popup.position + 1
      );
      let after = this.textareaValue.slice(this.popup.position + 1);
      return {
        before,
        content,
        after,
      };
    },
    filterUsers() {
        return this.users.filter(user => user?.name?.includes(this.popup.name))
    }
  },

  methods: {
    beforeinput(e) {
      this.beforeSelection = this.getSelection();
      if(this.beforeSelection.end != this.afterSelection.end) {
          this.popup.visible = false
      }
    },
    input(e) {
      let textareaValue = e.target.value
      this.afterSelection = this.getSelection();
      // 修改user 时 当成普通字符串
      this.oUsers = this.oUsers.filter(i => {
          return !((this.beforeSelection.end > i.start && this.beforeSelection.start < i.end )||  (i.start < this.beforeSelection.start && i.end> this.beforeSelection.end) )
      })
      // 光标后面的user
      let afterUser = this.oUsers.filter(i =>i.start>this.beforeSelection.end)
      afterUser.forEach(i => {
          let selectLength = this.beforeSelection.end-this.beforeSelection.start
          if(selectLength >0) {
              i.start = i.start- selectLength + e.data.length
              i.end = i.end- selectLength + e.data.length
          } else  {
              if(e.data&&e.data.length>0) {
                  i.start = i.start+ e.data.length
                    i.end = i.end + e.data.length

              } else {
                  i.start = i.start-1
                  i.end = i.end -1
              }
          }
      })
      if(e.inputType === 'deleteContentBackward' ) {
        let userIndex = this.oUsers.findIndex(i => i.end === this.beforeSelection.start)
        if(userIndex>=0) {
            let user = this.oUsers[userIndex]
            this.oUsers.splice(userIndex, 1)
            textareaValue = this.textareaValue.slice(0, user.start) + this.textareaValue.slice(user.end)
            this.$nextTick(() => {
               this.$refs.textarea.setSelectionRange(user.start,user.start)
               this.afterSelection = this.getSelection();
            })
        }
      }
      this.textareaValue = textareaValue;
      if (e.data === "@" && (!/[a-zA-Z0-9]/.test(this.textareaValue[e.target.selectionStart-2]) || !this.textareaValue[e.target.selectionStart-2])) {
        this.popup.position = e.target.selectionStart - 1;
        this.popup.visible = true
      } else if (e.data === " ") {
        this.popup.position = -1;
        this.popup.visible = false
      }
      if(this.popup.visible) {
          this.popup.name = this.textareaValue.slice(this.popup.position + 1, this.afterSelection.end)
          this.$emit('changeUserName', this.popup.name)
      }
      this.$emit('change', {
          textValue: this.textareaValue,
          users: cloneDeep(toRaw(this.oUsers))
      })
    },
    getSelection() {
        return {
            start: this.$refs.textarea.selectionStart,
            end: this.$refs.textarea.selectionEnd,
        }
    },
    clickUser(item) {
        let start = this.textareaValue.slice(0, this.popup.position+1)
        let mid = this.textareaValue.slice(this.popup.position+1 , this.afterSelection.start)
        let end = this.textareaValue.slice(this.afterSelection.start)
        let arr = [start, mid, end]

        arr[1] = item.name
        this.textareaValue = arr.join('')
        let pos = this.popup.position + item.name.length + 1

        this.oUsers.push({
            ...item,
            start: this.popup.position,
            end: pos,
        })
        this.oUsers.sort((i,j)=> i.start<j.start)
        this.$emit('change', {
            textValue: this.textareaValue,
            users: cloneDeep(toRaw(this.oUsers))
        })
        this.popup.visible = false
        this.popup.position = -1
        this.$nextTick(() => {
            this.$refs.textarea.focus();
            this.$refs.textarea.setSelectionRange(pos, pos)
            this.afterSelection = this.getSelection();
        })
    }
  },
});
</script>