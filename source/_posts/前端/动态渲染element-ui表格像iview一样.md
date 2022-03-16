---
layout: vue
title: 动态渲染element-ui表格像iview一样
date: 2019-07-05 23:29:57
tags: 
  - vue
categories:
  - vue
readtime: true
---

由于代码还是比较简单的 能力强的同学直接看demo就行了
<!--more-->
<br>
[demo传送](https://github.com/gexin1/dynamic-render-element-table)
使用到了渲染函数o(╥﹏╥)o
<br>
不了解的同学可以去官网看下=>[传送门](https://cn.vuejs.org/v2/guide/render-function.html)
头好大啊o(╥﹏╥)o
<br>
我们仿照iview 表格的写法 通过传入一个render函数来渲染自定义列
<br>
我通过函数自定义组件来合并传入的自定义列
<br>
<br />
### 自定义加强的column
```javascript
//ColumnPlus.vue
<script>
/**
 * 动态渲染 el-table-column
 */
export default {
    name: 'ColumnPlus',
    props: {
        attrs: {
            type: Object,
            default: () => ({}),
            required: true
        }
    },
    render: function(h) {
        let attrs = this.attrs;
        let scopedSlots = {};
        // 如果传入render 就表示需要自定义列 我们把渲染函数加入插槽里边
        if (attrs.render) {
            scopedSlots.default = scope => attrs.render(h, scope);
        }
        return h('el-table-column', {
            attrs,
            scopedSlots
        });
    }
};
</script>
```
我们赶快来动态构造table吧
### table 动态渲染
```html
//table.vue
<template>
    <el-table :data="data" v-bind="attrs" v-on="event">
        <column-plus
            v-for="(item, index) in columns"
            :key="index"
            :attrs="item"
        ></column-plus>
    </el-table>
</template>

<script>
import ColumnPlus from './ColumnPlus';
export default {
    props: {
        data: {
            type: Array,
            required: true
        },
        columns: {
            type: Array,
            required: true
        },
        attrs: {
            type: Object,
            default: () => ({})
        },
        event: {
            type: Object,
            default: () => ({})
        }
    },
    computed: {
        _attrs() {
            //默认table 参数
            const defaultParams = {};
            return Object.assign(this.attrs, defaultParams);
        }
    },
    components: {
        ColumnPlus
    }
};
</script>
```
终于完成了-_-||
### 使用组件
```html
<template>
  <div class="app">
    <dynamic-table
      :columns="columns2"
      :data="data"
      :attrs="tableAttr"
      :event="tableEvent"
    ></dynamic-table>
  </div>
</template>

<script>
import DynamicTable from "./components/DynamicTable";
export default {
  name: "App",
  data() {
    return {
      //table
      tableAttr: {
        height: `400px`,
        stripe: false,
        border: true,
        fit: true
      },
      tableEvent: {
        "selection-change": val => {
          console.log(val);
        }
      },
      columns2: [
        {
          type: "selection",
          width: "55"
        },
        {
          label: "Address",
          prop: "address",
          width: 300
        },
        {
          label: "Postcode",
          prop: "zip",
          width: 200,
          sortable: true,
          "sort-method": function(a, b) {
            return b.zip - a.zip;
          }
        },
        {
          label: "Action",
          fixed: "right",
          width: "300",
          render: (h, params) => {
            return h("div", [
              h(
                "el-button",
                {
                  props: {
                    type: "primary",
                    size: "mini"
                  },
                  on: {
                    click() {
                      console.log(params);
                    }
                  }
                },
                "查看"
              )
            ]);
          }
        }
      ],
      data: [
        {
          name: "John Brown",
          age: 18,
          address: "New York No. 1 Lake Park",
          province: "America",
          city: "New York",
          zip: 100010
        }
      ]
    };
  },
  components: {
    DynamicTable
  }
};
</script>

```
是不是很简单呢^_^
<br>
[demo传送](https://github.com/gexin1/dynamic-render-element-table)