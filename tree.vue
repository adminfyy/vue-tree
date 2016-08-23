<template>
  <li>
    <div>
        <span @click="toggle"  
        :class="['c-icon', node.is_leaf ? 'icon-file' : 'icon-folder', !node.isExpanded && node.childs.length ? '':'opened']"></span>
        <span v-if="checkable" @click="checked"
        :class="['c-icon', node.checked ? 'icon-square-checked' : 'icon-square-unchecked']"></span>
        <span @click="_selectNodeAction">{{ node[label]}}</span>
    </div>
    <template v-if="!node.is_leaf">
      <ul>
        <c-tree v-for="child in node.childs" track-by="$index"
        :class="{'hidden': !node.isExpanded , 'c-tree': true }"
        :node="child" 
        :isvuexdata="isvuexdata"
        :label="label" 
        :value="value"
        :checkable="checkable"
        :update-node-action="updateNodeAction"
        :select-node-action="selectNodeAction"
        @mutate="_mutate()"
        ></c-tree>
      </ul>
    </template>
  </li>  
</template>
<script>
export default {
  name: 'c-tree',
  props: {
    node: {
      type: Object,
      default: () => {
        return {}
      }
    },
    label: {
      type: String,
      default: 'label'
    },
    value: {
      type: String,
      default: 'value'
    },
    /* 更新vuex 节点数据的action*/
    updateNodeAction: {
      type: Function,
      default: () => false
    },
    selectNodeAction: {
      type: Function,
      default: () => false
    },
    /* 是否使用来自 vuex的数据  vuex的数据不允许直接操纵*/
    isvuexdata: {
      type: Boolean,
      default: false
    },
    checkable: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    toggle () {
      const newObj = Object.assign({}, this.node)
      newObj['isExpanded'] = !this.node.isExpanded
      this._updateNode('isExpanded', newObj)
    },
    _mutate(){
      if (!this.node.checked) {
        this.checked()
      }
    },
    checked () {
      const newObj = Object.assign({}, this.node)
      newObj['checked'] = !this.node['checked']
      this._updateNode('check',newObj)
      newObj['checked'] && this.$emit('mutate', newObj)
    },
    _updateNode (role, newObj) {
      this.isvuexdata && this.updateNodeAction(role, newObj)
      !this.isvuexdata && (this.node =  newObj)
    },
    _selectNodeAction () {
      this.selectNodeAction(this.node)
    }
  }
}
</script>
<style src="styles/components/tree"></style>