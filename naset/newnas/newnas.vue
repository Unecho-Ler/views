<template>
  <a-card :bordered="false">
    <a-steps class="steps" :current="currentTab">
      <a-step title="基本信息" />
      <a-step title="协议配置" />
      <a-step title="权限设置" />
      <a-step title="快照设置" />
      <a-step title="完成" />
    </a-steps>
    <div class="content">
      <step1 v-if="currentTab === 0" @nextStep="nextStep" @finish="finish"/>
      <step2 v-if="currentTab === 1" @nextStep="nextStep" @prevStep="prevStep" @finish="finish"/>
      <step3 v-if="currentTab === 2" @nextStep="nextStep" @prevStep="prevStep" @finish="finish"/>
      <step4 v-if="currentTab === 3" @nextStep="nextStep" @prevStep="prevStep" @finish="finish"/>
      <step5 v-if="currentTab === 4" @prevStep="prevStep" @finish="finish"/>
    </div>
  </a-card>
</template>

<script>
import Step1 from './Step1'
import Step2 from './Step2'
import Step3 from './Step3'
import Step4 from './Step4'
import Step5 from './Step5'

export default {
  name: 'StepForm',
  components: {
    Step1,
    Step2,
    Step3,
    Step4,
    Step5
  },
  data () {
    return {
      // description: '将一个冗长或用户不熟悉的表单任务分成多个步骤，指导用户完成。',
      currentTab: 0,

      // form
      form: null
    }
  },
  methods: {

    // handler
    nextStep () {
      if (this.currentTab < 4) {
        this.currentTab += 1
      }
    },
    prevStep () {
      if (this.currentTab > 0) {
        this.currentTab -= 1
      }
    },
    finish () {
      this.currentTab = 0
    }
  }
}
</script>

<style lang="less" scoped>
  .steps {
    max-width: 750px;
    margin: 16px auto;
  }
</style>
