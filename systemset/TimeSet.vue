<template>
  <a-card class="card" >
  <div style="width:100%">
    <div style="min-width: 200px;width:30%;float: left;">
        <a-table :columns="sys_columns" :dataSource="sys_data":pagination="false" />
    </div > 
    <div style="min-width: 400px;width:70%;float: left;">
    <!-- table -->
      <a-table
        :columns="columns"
        :dataSource="data"
        :pagination="false"
        :loading="memberLoading"
      >
        <template v-for="(col, i) in ['dvalue']" :slot="col" slot-scope="text, record">
          <span v-if="record.key==='1'">
            <a-date-picker
              :key="col"
              v-if="record.editable"
              style="margin: -5px 0"
              @change="e => handleChange(e.target.value, record.key, col)"
            />
            <a-time-picker
              :key="col"
              v-if="record.editable"
              style="margin: -5px 0"
              @change="e => handleChange(e.target.value, record.key, col)"
            />
            <!-- 
              <a-date-picker :defaultValue="moment('2019-10-01', 'YYYY-MM-DD')" />
              <a-time-picker/> -->
          </span>
          <span v-if="record.key==='2'">
            <a-select
              :key="col"
              v-if="record.editable"
              style="margin: -5px 0,width:200px"
              @change="e => handleChange(e.target.value, record.key, col)"
            >  <a-select-option value="UTC+4:00 莫斯科">UTC+4:00 莫斯科</a-select-option>
            <a-select-option value="UTC+5:00 伊斯兰堡">UTC+5:00 伊斯兰堡</a-select-option>
            <a-select-option value="UTC+6:00 阿斯塔纳">UTC+6:00 阿斯塔纳</a-select-option>
            <a-select-option value="UTC+7:00 曼谷">UTC+7:00 曼谷</a-select-option>
            <a-select-option value="UTC+8:00 北京">UTC+8:00 北京</a-select-option>
            <a-select-option value="UTC+9:00 东京">UTC+9:00 东京</a-select-option>
            </a-select>
            </span>
          <span v-if="record.key > '2'"> 
          <a-input
            :key="col"
            v-if="record.editable"
            style="margin: -5px 0"
            :value="text"
            @change="e => handleChange(e.target.value, record.key, col)"
          />
          </span>
          <template >
            <span v-if="record.key==='3'"><a-checkbox /></span>
            <span v-if="record.key==='4'"><a-textarea
          rows="4"
          placeholder="1.cnpool.ntp.org
1.asia.pool.net.org
0.asia.pool.net.org"
          v-decorator="[
            'description',
          ]" />
          </span>
            {{ text }}
            </template>
        </template>
        <template slot="operation" slot-scope="text, record">
          <template v-if="record.editable">
            <span v-if="record.isNew">
              <a-date-picker :defaultValue="moment('2019-10-01', 'YYYY-MM-DD')" />
              <a-time-picker/>
            </span>
            <span v-else>
              <a @click="saveRow(record)">保存</a>
              <a-divider type="vertical" />
              <a @click="cancel(record.key)">取消</a>
            </span>
          </template>
            <span v-else-if="record.saveable">
            <a @click="toggle(record.key)"><a-icon type="edit" /></a>
          </span>
        </template>
      </a-table>

    <!-- fixed footer toolbar -->
    </div>
  </div>
</a-card>
</template>
<script>
// import RepositoryForm from './RepositoryForm'
// import TaskForm from './TaskForm'
import FooterToolBar from '@/components/FooterToolbar'
import { mixin, mixinDevice } from '@/utils/mixin'

const fieldLabels = {
  name: '仓库名',
  url: '仓库域名',
  owner: '仓库管理员',
  approver: '审批人',
  dateRange: '生效日期',
  type: '仓库类型',
  name2: '任务名',
  url2: '任务描述',
  owner2: '执行人',
  approver2: '责任人',
  dateRange2: '生效日期',
  type2: '任务类型'
}

export default {
  name: 'AdvancedForm',
  mixins: [mixin, mixinDevice],
  components: {
    FooterToolBar
  },
  data () {
    return {
      description: '修改系统时间、系统时区以及NTP服务器的状态及NTP服务器地址清单。',
      loading: false,
      memberLoading: false,
      sys_columns: [
        {
          title: '类别',
          dataIndex: 'name',
          key: 'name',
          width: '100%',
          scopedSlots: { customRender: 'name' }
        },
      ],
      sys_data: [
        {
          key: '1',
          name: '当前系统时间'
        },
        {
          key: '2',
          name: '系统所在时区'
        },
        {
          key: '3',
          name: 'NTP服务器'
        }
      ],
      // table
      columns: [
        {
          title: '详情',
          dataIndex: 'dvalue',
          key: 'dvalue',
          width: '60%',
          scopedSlots: { customRender: 'dvalue' },
        },
        {
          key: 'action',
          scopedSlots: { customRender: 'operation' }
        }
      ],
      data: [
        {
          key: '1',
          dvalue: '2019-09-20 08:57:32',
          editable: false,
          saveable:true
        },
        {
          key: '2',
          dvalue: 'UTC +8:00 北京',
          editable: false,
          saveable:true
        },
        {
          key: '3',
          dvalue: '启用NTP服务',
          editable: false,
          saveable:false
        },
        {
          key: '4',
          dvalue: '',
          editable: false,
          saveable:true
        }
      ],

      errors: []
    }
  },
  methods: {
    handleSubmit (e) {
      e.preventDefault()
    },
    newMember () {
      const length = this.data.length
      this.data.push({
        key: length === 0 ? '1' : (parseInt(this.data[length - 1].key) + 1).toString(),
        name: '',
        workId: '',
        department: '',
        editable: true,
        isNew: true
      })
    },
    remove (key) {
      const newData = this.data.filter(item => item.key !== key)
      this.data = newData
    },
    saveRow (record) {
      this.memberLoading = true
      const { key, name, workId, department } = record
      if (!name || !workId || !department) {
        this.memberLoading = false
        this.$message.error('请填写完整成员信息。')
        return
      }
      // 模拟网络请求、卡顿 800ms
      new Promise((resolve) => {
        setTimeout(() => {
          resolve({ loop: false })
        }, 800)
      }).then(() => {
        const target = this.data.filter(item => item.key === key)[0]
        target.editable = false
        target.isNew = false
        this.memberLoading = false
      })
    },
    toggle (key) {
      const target = this.data.filter(item => item.key === key)[0]
      target.editable = !target.editable
    },
    getRowByKey (key, newData) {
      const data = this.data
      return (newData || data).filter(item => item.key === key)[0]
    },
    cancel (key) {
      const target = this.data.filter(item => item.key === key)[0]
      target.editable = false
    },
    handleChange (value, key, column) {
      const newData = [...this.data]
      const target = newData.filter(item => key === item.key)[0]
      if (target) {
        target[column] = value
        this.data = newData
      }
    },

    // 最终全页面提交
    validate () {
      const { $refs: { repository, task }, $notification } = this
      const repositoryForm = new Promise((resolve, reject) => {
        repository.form.validateFields((err, values) => {
          if (err) {
            reject(err)
            return
          }
          resolve(values)
        })
      })
      const taskForm = new Promise((resolve, reject) => {
        task.form.validateFields((err, values) => {
          if (err) {
            reject(err)
            return
          }
          resolve(values)
        })
      })

      // clean this.errors
      this.errors = []
      Promise.all([repositoryForm, taskForm]).then(values => {
        $notification['error']({
          message: 'Received values of form:',
          description: JSON.stringify(values)
        })
      }).catch(() => {
        const errors = Object.assign({}, repository.form.getFieldsError(), task.form.getFieldsError())
        const tmp = { ...errors }
        this.errorList(tmp)
        console.log(tmp)
      })
    },
    errorList (errors) {
      if (!errors || errors.length === 0) {
        return null
      }
      this.errors = Object.keys(errors).map(key => {
        if (!errors[key]) {
          return null
        }

        return {
          key: key,
          message: errors[key][0],
          fieldLabel: fieldLabels[key]
        }
      })
    },
    scrollToField (fieldKey) {
      const labelNode = document.querySelector(`label[for="${fieldKey}"]`)
      if (labelNode) {
        labelNode.scrollIntoView(true)
      }
    }
  }
}
</script>

<style lang="less" scoped>
  .card{
    margin-bottom: 24px;
  }
  .popover-wrapper {
    /deep/ .antd-pro-pages-forms-style-errorPopover .ant-popover-inner-content {
      min-width: 256px;
      max-height: 290px;
      padding: 0;
      overflow: auto;
    }
  }
  .antd-pro-pages-forms-style-errorIcon {
    user-select: none;
    margin-right: 24px;
    color: #f5222d;
    cursor: pointer;
    i {
          margin-right: 4px;
    }
  }
  .antd-pro-pages-forms-style-errorListItem {
    padding: 8px 16px;
    list-style: none;
    border-bottom: 1px solid #e8e8e8;
    cursor: pointer;
    transition: all .3s;

    &:hover {
      background: #e6f7ff;
    }
    .antd-pro-pages-forms-style-errorIcon {
      float: left;
      margin-top: 4px;
      margin-right: 12px;
      padding-bottom: 22px;
      color: #f5222d;
    }
    .antd-pro-pages-forms-style-errorField {
      margin-top: 2px;
      color: rgba(0,0,0,.45);
      font-size: 12px;
    }
  }
</style>
