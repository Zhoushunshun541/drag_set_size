<template>
  <div
    v-clickout
    class=" drag-size award-content-show editor-award stamp"
    ref="editorstamp"
    :class="value.setSize"
    :style="{ left: value.x + 'px', top: value.y + 'px' }"
  >
    <div @mousedown="setDomSize($event, 1)" class="set-size left-top"></div>
    <div @mousedown="setDomSize($event, 2)" class="set-size top"></div>
    <div @mousedown="setDomSize($event, 3)" class="set-size right-top"></div>
    <div @mousedown="setDomSize($event, 4)" class="set-size right"></div>
    <div @mousedown="setDomSize($event, 5)" class="set-size right-bottom"></div>
    <div @mousedown="setDomSize($event, 6)" class="set-size bottom"></div>
    <div @mousedown="setDomSize($event, 7)" class="set-size left-bottom"></div>
    <div @mousedown="setDomSize($event, 8)" class="set-size left"></div>
    <div @click="toDeleteImg" v-if="deleteImg" class="delete-img">删除</div>
    <img
      :id="id"
      ref="dragImg"
      :src="imgUrl"
      @load="getImgWH"
      @mousedown="moveStamp"
      :height="value.h"
      :width="value.w"
      draggable="false"
    />
  </div>
</template>

<script>
export default {
  name: 'dragSize',
  props: {
    value: {
      type: Object,
      default: () => {},
    },
    id: {
      type: String,
      default: 'dragImg',
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    deleteImg: {
      type: Boolean,
      default: false,
    },
    imgUrl: {
      type: String,
      default: '',
    },
    limitRangeId: {
      type: String,
      default: '',
    },
  },
  data() {
    return {
      limitRange: {
        width: 0,
        height: 0,
        x: 0,
        y: 0,
      },
    }
  },
  methods: {
    // 删除图片
    toDeleteImg() {
      try {
        console.log(this.$emit('on-delete-img'))
        this.$emit('on-delete-img')
      } catch (error) {
        console.log(
          '%c请绑定 on-delete-img 事件进行删除操作',
          'background:#D84D36 ; padding: 5px; border-radius:3px;  color: #fff'
        )
      }
    },
    /**
     * @description: 拖动印章的方法
     */
    moveStamp(element) {
      if (!this.disabled) {
        this.value.setSize = 'stamp-size-style'
        // 监听鼠标左键的时间
        if (element.button == 0) {
          // 获取鼠标相对于点击元素的位置
          let ox = element.offsetX
          let oy = element.offsetY
          document.onmousemove = (e) => {
            // 鼠标坐标
            let mouseX = e.clientX
            let mouseY = e.clientY
            //鼠标按下并移动的事件
            //用鼠标的位置减去鼠标相对元素的位置  再减去图片的位置，得到元素的位置
            let left = mouseX - ox - this.limitRange.x
            let top = mouseY - oy - this.limitRange.y
            this.value.w = element.target.offsetWidth
            this.value.h = element.target.offsetHeight

            //绑定元素位置并限制范围在图片内
            if (
              top >= 0 &&
              left >= 0 &&
              top + element.target.offsetHeight <= this.limitRange.height &&
              left + element.target.offsetWidth <= this.limitRange.width
            ) {
              this.value.y = top
              this.value.x = left
            }
            // 这里限制范围的同时  可以让其在范围外根据合理范围移动
            // 左上角
            if (top <= 0 && left <= 0) {
              this.value.y = 0
              this.value.x = 0
            } else if (top <= 0 && left >= 0) {
              this.value.y = 0
              this.value.x = left
            }
            // 左下角
            if (
              top <= 0 &&
              left + element.target.offsetWidth >= this.limitRange.width
            ) {
              this.value.y = 0
              this.value.x = this.limitRange.width - element.target.offsetWidth
            } else if (
              top + element.target.offsetHeight >= this.limitRange.height &&
              left + element.target.offsetWidth <= this.limitRange.width
            ) {
              this.value.y =
                this.limitRange.height - element.target.offsetHeight
              this.value.x = left
            }
            // 右上角
            if (
              top + element.target.offsetHeight >= this.limitRange.height &&
              left <= 0
            ) {
              this.value.y =
                this.limitRange.height - element.target.offsetHeight
              this.value.x = 0
            } else if (left <= 0 && top >= 0) {
              this.value.y = top
              this.value.x = 0
            } else if (
              left + element.target.offsetWidth >= this.limitRange.width &&
              top >= 0
            ) {
              this.value.y = top
              this.value.x = this.limitRange.width - element.target.offsetWidth
            }
            // 右下角
            if (
              top + element.target.offsetHeight >= this.limitRange.height &&
              left + element.target.offsetWidth >= this.limitRange.width
            ) {
              this.value.y =
                this.limitRange.height - element.target.offsetHeight
              this.value.x = this.limitRange.width - element.target.offsetWidth
            }
          }
          document.onmouseup = () => {
            this.value.setSize = ''
            document.onmousemove = null
            document.onmouseup = null
          }
        }
      }
    },
    /**
     * @description: type 判断当前拖动的是哪个
     * 1: 左上   改变图片的定位
     * 2: 正上   改变图片的定位
     * 3: 右上   改变图片的定位
     * 4: 右
     * 5: 右下
     * 6: 下
     * 7: 左下   改变图片的定位
     * 8: 左   改变图片的定位
     */
    // 设置大小
    setDomSize(element, type) {
      if (!this.disabled) {
        // 监听鼠标左键的事件
        if (element.button == 0) {
          // 获取鼠标相对于点击元素的位置
          let ox = element.offsetX
          let oy = element.offsetY
          document.onmousemove = (e) => {
            // 鼠标坐标
            let mouseX = e.clientX
            let mouseY = e.clientY
            //鼠标按下并移动的事件
            //用鼠标的位置减去鼠标相对元素的位置  再减去图片的位置，得到元素的位置
            let left = mouseX - ox - this.limitRange.x
            let top = mouseY - oy - this.limitRange.y

            switch (type) {
              case 1:
                // 限制  左上角不能拖到图片的右下角 并且在图片内
                if (
                  top < this.value.y + this.value.h - 30 &&
                  left < this.value.x + this.value.w - 30 &&
                  top >= 0 &&
                  left >= 0
                ) {
                  this.value.h += this.value.y - top
                  this.value.w += this.value.x - left
                  this.value.y = top
                  this.value.x = left
                }

                break
              case 2:
                if (top >= 0 && top < this.value.y + this.value.h - 30) {
                  this.value.h += this.value.y - top
                  this.value.y = top
                }
                break
              case 3:
                if (
                  top < this.value.y + this.value.h - 30 &&
                  left > this.value.x + 30 &&
                  top >= 0 &&
                  left <= this.limitRange.width
                ) {
                  this.value.h += this.value.y - top
                  this.value.w = left - this.value.x
                  this.value.y = top
                }
                break
              case 4:
                if (left <= this.limitRange.width && left > this.value.x + 30) {
                  this.value.w = left - this.value.x
                }
                break
              case 5:
                if (
                  left > this.value.x + 30 &&
                  top > this.value.y + 30 &&
                  top <= this.limitRange.height &&
                  left <= this.limitRange.width
                ) {
                  this.value.w = left - this.value.x
                  this.value.h = top - this.value.y
                }
                break
              case 6:
                if (top > this.value.y + 30 && top <= this.limitRange.height) {
                  this.value.h = top - this.value.y
                }
                break
              case 7:
                if (
                  top > this.value.y + 30 &&
                  top <= this.limitRange.height &&
                  left >= 0 &&
                  left < this.value.x + this.value.w - 30
                ) {
                  this.value.h = top - this.value.y
                  this.value.w -= left - this.value.x
                  this.value.x = left
                }
                break
              case 8:
                if (left >= 0 && left < this.value.x + this.value.w - 30) {
                  this.value.w -= left - this.value.x
                  this.value.x = left
                }
                break
            }
          }
          document.onmouseup = () => {
            this.value.setSize = ''
            document.onmousemove = null
            document.onmouseup = null
          }
        }
      }
    },
    // 获取图片的宽高
    getImgWH() {
      if (this.value.w == 0 || this.value.w == '') {
        const img = this.$refs.dragImg
        this.value.w = img.naturalWidth
        this.value.h = img.naturalHeight
      }
    },
    // 根据传值设置拖动的文件范围
    getParentDomLimitRange() {
      let dom = document.getElementById(this.limitRangeId)
      dom = dom ? dom : document.body
      dom.style.position = 'relative'
      // 判断是否挂载到指定的节点
      dom.appendChild(this.$el)
      this.limitRange.width = dom.scrollWidth
      this.limitRange.height = dom.scrollHeight
      // 获取当前dom到视图内容的top  left  宝藏api
      this.limitRange.x = dom.getBoundingClientRect().left
      this.limitRange.y = dom.getBoundingClientRect().top
    },
  },
  mounted() {
    this.getParentDomLimitRange()
  },
  // 退出时候移除
  destroyed() {
    if (this.limitRangeId && this.$el && this.$el.parentNode) {
      this.$el.parentNode.removeChild(this.$el)
    }
  },
  // 自定义指令
  directives: {
    clickout: {
      bind(el) {
        function documentHandler(e) {
          let child = el.childNodes
          // 这里判断点击的元素是否是本身，是本身，则返回
          if (el.contains(e.target)) {
            child.forEach((res) => {
              //  控制8个子div显示隐藏
              if (
                res.classList.value.indexOf('set-size') > -1 ||
                res.classList.value.indexOf('delete-img') > -1
              ) {
                res.style.display = 'block'
              }
            })
            el.classList.add('stamp-size-style')
            return false
          } else {
            child.forEach((res) => {
              if (
                res.classList.value.indexOf('set-size') > -1 ||
                res.classList.value.indexOf('delete-img') > -1
              ) {
                res.style.display = 'none'
              }
            })
            el.classList.remove('stamp-size-style')
          }
        }
        // 给当前元素绑定个私有变量，方便在unbind中可以解除事件监听
        el.__vueClickOutside__ = documentHandler
        document.addEventListener('click', documentHandler)
      },
      unbind(el) {
        // 解除事件监听
        document.removeEventListener('click', el.__vueClickOutside__)
        delete el.__vueClickOutside__
      },
    },
  },
}
</script>

<style lang="less" scoped>
.award-content-show {
  cursor: move;
  position: absolute;
  white-space: nowrap;
  user-select: none;
  text-align: left;
}
.stamp-size-style {
  border: 1px solid #007aff !important;
}

.stamp {
  border: 1px solid transparent;

  img {
    vertical-align: top;
  }

  .set-size {
    display: none;
    position: absolute;
    width: 10px;
    height: 10px;
    border: 1px solid #007aff;
    background: #fff;
  }
  .delete-img {
    display: none;
    position: absolute;
    cursor: pointer;
    bottom: 10px;
    right: 10px;
    color: #333;
    font-size: 12px;
    padding: 2px 10px;
    background: #efeff4;
    box-shadow: 0px 2px 4px 0px rgba(0, 0, 0, 0.5);
    border-radius: 4px;
  }
  .left-top {
    cursor: nwse-resize;
    top: -5px;
    left: -5px;
  }

  .top {
    cursor: ns-resize;
    top: -5px;
    left: 50%;
    margin-left: -5px;
  }

  .right-top {
    cursor: nesw-resize;
    top: -5px;
    right: -5px;
  }

  .right {
    cursor: ew-resize;
    top: 50%;
    margin-top: -5px;
    right: -5px;
  }

  .right-bottom {
    cursor: nwse-resize;
    bottom: -5px;
    right: -5px;
  }

  .bottom {
    cursor: ns-resize;
    bottom: -5px;
    left: 50%;
    margin-left: -5px;
  }

  .left-bottom {
    cursor: nesw-resize;
    bottom: -5px;
    left: -5px;
  }

  .left {
    cursor: ew-resize;
    top: 50%;
    margin-top: -5px;
    left: -5px;
  }
}
</style>
