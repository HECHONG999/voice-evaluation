<template>
  <div class="w-full h-full flex flex-row">
    <div class="w-2/12 border-2">
      <p class="text-center">课程列表</p>
      <ul  class="p-2" @click="switchCourse">
        <template v-for="course in courseList" :key="course.courseId">
          <li v-bind:courseId='course.courseId' >{{course.courseName}}</li>
        </template>
      </ul>
    </div>
    <div class="flex-1 flex flex-col h-full">
      <div class="flex-1 m-2">
        <ul >
          <template v-for="message in messageList">
            <li>
              <p>
                <PlayCircle />
              </p>
              <p>
                {{message.text}}
              </p>
            </li>
          </template>

          <li>Anifny</li>
          <li>Anifny</li>
        </ul>
      </div>
      <div class="m-4 flex flex-row justify-around">
        <button @click="startRecord" :disabled="isRecording"  type="button" :class="isRecording &&  'bg-gray-500 hover:bg-gray-500'" class="rounded-full bg-indigo-600 px-4 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">开始录音</button>
        <button @click="pauseRecord" :disabled="!isRecording" type="button"  :class="!isRecording &&  'bg-gray-500 hover:bg-gray-500'" class="rounded-full bg-indigo-600 px-4 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">暂停录音</button>
        <button @click="stopRecord" type="button" class="rounded-full bg-indigo-600 px-4 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">结束录音</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import {onMounted, reactive, ref, unref} from "vue";
import axios from "axios";
import {PlayCircle} from 'lucide-vue-next'
import Recorder from 'js-audio-recorder'
const courseList = ref([]);
const isRecording = ref(false)
const currentCourse = ref(null)
const recorderRef = ref(null);

const messageList = ref([])
onMounted(async () => {
  const {data: {code, data}} =  await axios.get('/learn/course/getAllCourse')
  if(code === 200) {
    courseList.value  =data
  }
})

const startRecord = () => {
  recorderRef.value = new Recorder()
  recorderRef.value.start()
  Recorder.getPermission().then(() => {
    console.log('开始录音')
    recorderRef.value.start() // 开始录音
    isRecording.value = true
  }, (error) => {
    alert(error.message)
    console.log(`${error.name} : ${error.message}`)
  })
}
const pauseRecord = () => {
  recorderRef.value.pause()
}
const stopRecord = () => {
  recorderRef.value.stop()
  const blob = recorderRef.value.getWAVBlob()
  console.log(blob)
}
const switchCourse = async (e) => {
  const courseId = e.target.getAttribute('courseId');
  const {data:{code, data}} = await axios.get('/learn/chat/initGptRole')
  messageList.value.push(data)
  const currentCourse = unref(courseList).find((course) => course.courseId === courseId)
  currentCourse.value = unref(currentCourse)
}
</script>
