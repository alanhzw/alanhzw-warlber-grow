<template>
  <div class="image-processer">
    <a-modal title="裁剪图片"
             v-model:visible="showModal"
             @ok="handleOk"
             @cancel="showModal = false"
             okText="确认"
             cancelText="取消">
      <div class="image-cropper">
        <img :src="baseImageUrl"
             id="processed-image" />
      </div>
    </a-modal>
    <div class="image-preview"
         :style="{ backgroundImage: backgrondUrl }"
         :class="{ 'extraHeight': showDelete }">
    </div>
    <div class="image-process">
      <uploader action="/utils/upload-img"
                @file-uploaded="handleFileUploaded"
                :beforeUpload="commonUploadCheck">
        <div class="uploader-container">
          <a-button>
            <template v-slot:icon>
              <UploadOutlined />
            </template>更换图片
          </a-button>
        </div>
        <template #loading>
          <div class="uploader-container">
            <a-button>
              <template v-slot:icon>
                <LoadingOutlined />
              </template>上传中
            </a-button>
          </div>
        </template>
        <template #uploaded>
          <a-button>
            <template v-slot:icon>
              <UploadOutlined />
            </template>更换图片
          </a-button>
        </template>
      </uploader>
      <a-button @click="showModal = true">
        <template v-slot:icon>
          <ScissorOutlined />
        </template>裁剪图片
      </a-button>
      <a-button v-if="showDelete"
                type="danger"
                @click="handleDelete">
        <template v-slot:icon>
          <DeleteOutlined />
        </template>删除图片
      </a-button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, watch, nextTick } from 'vue'
import Cropper from 'cropperjs'
import { message } from 'ant-design-vue'
import { UploadOutlined, ScissorOutlined, LoadingOutlined, DeleteOutlined } from '@ant-design/icons-vue'
import Uploader from './Uploader.vue'
import { commonUploadCheck, UploadImgProps } from '../helper'
export default defineComponent({
  props: {
    value: {
      type: String,
      required: true
    },
    ratio: {
      type: Number
    },
    showDelete: {
      type: Boolean,
      default: false
    }
  },
  components: {
    UploadOutlined,
    ScissorOutlined,
    LoadingOutlined,
    DeleteOutlined,
    Uploader
  },
  emits: ['change', 'uploaded'],
  setup(props, context) {
    const showModal = ref(false)
    let cropperData: any
    let cropper: Cropper
    watch(showModal, (newValue) => {
      if (newValue) {
        nextTick(() => {
          const image = document.getElementById('processed-image') as HTMLImageElement
          cropper = new Cropper(image, {
            checkCrossOrigin: false,
            crop(event) {
              const { x, y, width, height } = event.detail
              cropperData = {
                x: Math.floor(x),
                y: Math.floor(y),
                width: Math.floor(width),
                height: Math.floor(height)
              }
            },
            ...(props.ratio && { aspectRatio: props.ratio })
          })
        })
      } else {
        if (cropper) {
          cropper.destroy()
        }
      }
    })
    const baseImageUrl = computed(() => props.value.split('?')[0])
    const backgrondUrl = computed(() => `url(${props.value})`)
    const handleOk = () => {
      const { x, y, width, height } = cropperData
      const cropperedUrl = baseImageUrl.value + `?x-oss-process=image/crop,x_${x},y_${y},w_${width},h_${height}`
      context.emit('change', cropperedUrl)
      showModal.value = false
    }
    const handleFileUploaded = (uploadedData: UploadImgProps) => {
      message.success('上传成功')
      context.emit('change', uploadedData.data.urls[0])
      context.emit('uploaded', uploadedData)
    }
    const handleDelete = () => {
      context.emit('change', '')
    }
    return {
      showModal,
      handleOk,
      baseImageUrl,
      backgrondUrl,
      commonUploadCheck,
      handleFileUploaded,
      handleDelete
    }
  }
})
</script>

<style>
.image-processer {
  display: flex;
  justify-content: space-between;
}

.image-preview {
  width: 150px;
  height: 84px;
  border: 1px dashed #e6ebed;
  background: no-repeat 50%/contain;
}

.image-preview.extraHeight {
  height: 110px;
}

.image-cropper img {
  display: block;
  /* This rule is very important, please don't ignore this */
  max-width: 100%;
}

.image-process {
  padding: 5px 0;
  margin-left: 10px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
</style>
