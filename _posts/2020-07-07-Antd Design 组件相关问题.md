---
layout:     post
title:      Antd Design 组件相关问题
date:       2020-07-07
author:     violetks
header-img: img/post-bg-ioses.jpg
catalog: true
tags:
    - React
    - Antd Design
---

### 一、`Alert` 组件的 `message` 中除了接收字符串外，还能接收DOM节点。<br>

```html
<Alert
  message={
   <p>
     <span>已选择 {selectedRowKeys.length} 项</span>
     <a onClick={resetChecked} style={% raw %}{{ marginLeft: '24px' }}{% endraw %}>清空</a>
   </p>
  }
  type="info"
  showIcon
 />
```

### 二、`Form` 表单高级搜索

收起时只显示前两个 `Form.Item` ，展开显示全部。<br>

```html
const [expand, setExpand] = useState(false);

// 搜索框结构样式
const renderForm = () => {
  return (
    <Form form={form} onFinish={handleSearch}>
      <Row gutter={24}>
        <Col span={8}>
          <Form.Item name="userName" label="用户名称"
            getValueFromEvent={event => event.target.value.trim()}>
            <Input placeholder="请输入用户名称" />
          </Form.Item>
        </Col>

        <Col span={8}>
          <Form.Item name="status" label="状态">
            <Select placeholder="请选择状态" allowClear={true}>
              <Option value="1">已启用</Option>
              <Option value="0">未启用</Option>
            </Select>
          </Form.Item>
        </Col>

        <Col span={8} style={% raw %}{{ display: expand ? 'block' : 'none' }}{% endraw %}>
          <Form.Item name="classId" label="所属班级">
            <Select placeholder="请选择班级" allowClear={true}>
              {
                classList.map(type =>
                  <Option key={type.fieldKey} value={type.fieldKey}>{type.fieldValue}</Option>
                )
              }
            </Select>
          </Form.Item>
        </Col>

        <Col span={8} style={% raw %}{{ display: expand ? 'block' : 'none' }}{% endraw %}>
          <Form.Item name="gradeId" label="所属年级">
            <Select placeholder="请选择所属年级" allowClear={true}>
              {
                gradeList.map(type =>
                  <Option key={type.fieldKey} value={type.fieldKey}>{type.fieldValue}</Option>
                )
              }
            </Select>
          </Form.Item>
        </Col>

        <Col span={expand ? 16 : 8} style={% raw %}{{ textAlign: expand ? "right" : "left" }}{% endraw %}>
          <Button type="primary" htmlType="submit">查询</Button>
          <Button style={% raw %}{{ marginLeft: 8 }}{% endraw %} onClick={handleReset}>重置</Button>
          <Button type="link" onClick={() => { setExpand(!expand) }}>
            {expand ? <span>收起<UpOutlined /></span> : <span>展开<DownOutlined /></span>}
          </Button>
        </Col>
      </Row>
    </Form>
  )
}
```

### 三、`Form.Item` 是 `<span>` 元素问题

```html
<Form.Item name="schedTime" label="时间">
  <span style={% raw %}{{ marginRight: (schedTime || time) ? 8 : 0 }}{% endraw %}>{time ? time : schedTime}</span>
  <Button type="primary" onClick={openDialog}>设置时间规则</Button>
</Form.Item>
```

**场景：**在 `FormItem` 里有一个 `<span>` 和 一个 `<Button>` ，点击按钮出现一个设置时间规则的组件，设置完成后的时间值，也就是一个字符串会在 `<span>` 中显示。最后和其他表单项的值一起提交给后端接口。<br>

**问题一：**表单填写完提交时没有 `<span>` 元素中的值。<br>
**原因：**因为 `form` 默认只将带 `name` 属性的 `input` 标签的值提交。`<span>` 元素没有这个属性，需要手动设置它的值。
这里我是在打开设置时间规则组件时通过 `setFieldsValue` 将值赋给 `FormItem`。<br>

```javascript
const getTimeRules = value => {
  setTime(value);
  form.setFieldsValue({ schedTime: value.trim() });
  setVisibleDialog(false);
}
```

**问题二：**虽然时间值有了，但页面上不显示，不能及时改变，清空表单时时间值还在。<br>
**解决方法：**定义了一个 `state` 变量 `time` ，用来显示的，实际传递给后端接口的还是 `schedTime`。<br>

```javascript
const [time, setTime] = useState("");

// 清空表单和数据
const clearForm = () => {
  setTime("");
  form.resetFields();
}
```

### 四、`Form.Item`内有多个元素，控制台报错。

![di4XQI.png](https://s1.ax1x.com/2020/08/15/di4XQI.png)
<!-- ![di4XQI.png](/instructPic/di4XQI.png) -->

`<Form.Item name="field" />` 只会对它的直接子元素绑定表单功能，例如直接包裹了 Input/Select。如果控件前后还有一些文案或样式装点，或者一个表单项内有多个控件，你可以使用内嵌的 `Form.Item` 完成。你可以给 `Form.Item` 自定义 `style` 进行内联布局，或者添加 `noStyle` 作为纯粹的无样式绑定组件（类似 3.x 中的 `getFieldDecorator`）。<br>

根据官网介绍，将第三点中 `Form.Item` 的结构改为如下形式，就没有上面的错误了。<br>

```html
<FormItem label="时间">
  <FormItem name="schedTime" noStyle>
    <span style={% raw %}{{ marginRight: (schedTime || time) ? 8 : 0 }}{% endraw %}>{time ? time : schedTime}</span>
  </FormItem>
  <Button type="primary" onClick={openDialog}>设置时间规则</Button>
</FormItem>
```

### 五、`Form.Item`下的子组件`defaultValue`不生效

```html
<FormItem name="num" label="数字">
  <InputNumber min={0} max={255} defaultValue={0} />
</FormItem>
```

一开始我使用上面的方法给 `InputNumber` 设置了初始值，但没有生效，控制台还报错。<br>

![di4jyt.png](https://s1.ax1x.com/2020/08/15/di4jyt.png)
<!-- ![di4jyt.png](/instructPic/di4jyt.png) -->

官网里有提到这个问题：当你为 `Form.Item` 设置 `name` 属性后，子组件会转为受控模式。因而 `defaultValue` 不会生效。你需要在 `Form` 上通过 `initialValues` 设置默认值。<br>

```html
<Form initialValues={% raw %}{{ num: 0 }}{% endraw %}>
  <FormItem name="num" label="数字">
    <InputNumber min={0} max={255} />
  </FormItem>
</Form>
```

### 六、`Form.Item`的 `noStyle` 属性

为 `true` 时不带样式。可编辑表格中的 `Form.Item` 设置 `noStyle` 之后，校验时不会出现下图中的效果。<br>

![09Gn3R.png](https://s1.ax1x.com/2020/09/25/09Gn3R.png)
<!-- ![09Gn3R.png](/instructPic/09Gn3R.png) -->

### 七、 编辑页`Select` 下拉框回显id问题

**场景：**新增和编辑共用一个表单，下拉框之间有联动的效果，先选择某个年级，才能选择这个年级下的某个班级，接着才能选择这个班级下的某个学生。也就是三个下拉框之间的联动，下图有点问题...<br>
**注意：**第一个和第二个下拉框编辑需要回显名称，实际传给接口的值是对应id，第三个下拉框显示名称，传的值也是名称。<br>
**问题：**最初第一个和第二个下拉框都是用 `onChange` 事件，实现第一个下拉框值改变时获取到第二个下拉框下拉选项，这样刚进入编辑页回显时第二个下拉框回显的是id而不是班级名称。<br>
**原因：**刚进入编辑页第一个下拉框的 `onChange` 事件没有触发，第二个下拉框数组没有值。<br>

新增页：<br>

![wcoens.png](https://s1.ax1x.com/2020/09/16/wcoens.png)
<!-- ![wcoens.png](/instructPic/wcoens.png) -->

编辑页：<br>

![wcomBn.png](https://s1.ax1x.com/2020/09/16/wcomBn.png)
<!-- ![wcomBn.png](/instructPic/wcomBn.png) -->

**解决：**将下拉框中的 `onChange` 事件在 `useEffect` 中调用，主要代码如下。<br>

```html
import React, { useEffect, useState } from 'react';
import { useSelector, useDispatch } from 'dva';
import { Form, Select, Button } from 'antd';
import { history, useLocation } from 'umi';

const FormItem = Form.Item;
const { Option } = Select;

const UpdatePage = () => {
  const dispatch = useDispatch();
  const [form] = Form.useForm();
  const { query: { id } } = useLocation();

  const gradeId = form.getFieldValue("gradeId");
  const classId = form.getFieldValue("classId");

  const { info, classList } = useSelector(state => state.student); // 某个年级下所有班级

  // 编辑表单回显
  useEffect(() => {
    if (id) {
      dispatch({...});

      if (gradeId) {
        getClassList(gradeId)
      }
    }
  }, [id, gradeId])

  return (
    <Form form={form} onFinish={update}>
      <FormItem name="gradeId" label="年级">
        <Select allowClear placeholder="请选择"
          onChange={key => getClassList(key)}>
          {
            gradeList.map(type =>
              <Option key={type.fieldKey} value={type.fieldKey}>{type.fieldValue}</Option>
            )
          }
        </Select>
      </FormItem>

      <FormItem name="classId" label="班级名称">
        <Select allowClear placeholder="请选择" disabled={!gradeId}
          onChange={key => getStudent(key)}>
          {
            classList.map(type =>
              <Option key={type.fieldKey} value={type.fieldKey}>{type.fieldValue}</Option>
            )
          }
        </Select>
      </FormItem>

      <FormItem name="student" label="学生姓名">
        <Select allowClear mode="multiple" placeholder="请选择" disabled={!classId}>
          {
            studentList.map(item =>
              <Option key={item} value={item}>{item}</Option>
            )
          }
        </Select>
      </FormItem>

      <FormItem>
        <Button onClick={clearForm}>取消</Button>
        <Button type="primary" htmlType="submit">确定</Button>
      </FormItem>
    </Form>
  )
};

export default UpdatePage;
```

### 七、`Select` 的 `onChange` 事件的两个参数

`Select` 下拉框的 `onChange` 事件，官方说明是 **选中 `option`，或 `input` 的 `value` 变化时，调用此函数。**<br>

```javascript
function(value, option:Option | Array<Option>)
```

也就是说，接收两个参数 `value` 和 `option`，`option` 可以是一个对象或者对象数组。<br>
之前一直只用到 `value` 参数，某天打印出了 `option` 对象时就有点迷惑。<br>

```html
<Select
  onChange={(value, option) => {
    console.log(value);
    console.log(option);
    fn1(option.key);
    fn2(option.value);
  }}
>
  {
    userList.map(item =>
      <Option key={item.userId} value={item.userName}>{item.userName}</Option>
    )
  }
</Select>
```

```javascript
// 打印出的 option 对象
{
  key: "1462050266472448",
  value: "jack"
  children: "jack"
}
```