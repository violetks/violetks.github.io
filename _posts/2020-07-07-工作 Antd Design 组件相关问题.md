---
layout:     post
title:      工作 Antd Design 组件相关问题
date:       2020-07-07
author:     violetks
header-img: img/post-bg-ioses.jpg
catalog: true
tags:
    - React
    - Antd Design
    - < 工作问题记录 >
---

### 一、`Alert` 组件默认隐藏

`Alert` 组件的 `message` 中除了接收字符串外，还能接收DOM节点。<br>

```html,javascript
const hasSelected = selectedRowKeys.length > 0;

<div style={% raw %}{{ display: hasSelected ? 'block' : 'none', marginBottom: '16px' }}{% endraw %}>
  <Alert
    message={
     <p>
       <span>已选择 {selectedRowKeys.length} 项</span>
	   <a onClick={resetChecked} style={% raw %}{{ marginLeft: '24px' }}{% raw %}>清空</a>
	 </p>
	}
    type="info"
    showIcon
   />
</div>
```

### 二、`Form` 表单高级搜索

收起时只显示前两个 `FormItem` ，展开显示全部。<br>

```html,javascript
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
              <Button style={{ marginLeft: 8 }} onClick={handleReset}>重置</Button>
              <Button type="link" onClick={() => { setExpand(!expand) }}>
                {expand ? <span>收起<UpOutlined /></span> : <span>展开<DownOutlined /></span>}
              </Button>
            </Col>
          </Row>
        </Form>
    )
  }
```

### 三、`FormItem` 是 `<span>` 元素问题

```html,javascript
<FormItem name="schedTime" label="时间">
  <span style={% raw %}{{ marginRight: (schedTime || time) ? 8 : 0 }}{% endraw %}>{time ? time : schedTime}</span>
  <Button type="primary" onClick={openDialog}>设置时间规则</Button>
</FormItem>
```

**场景：**在 `FormItem` 里有一个 `<span>` 和 一个 `<Button>` ，点击按钮出现一个设置时间规则的组件，设置完成后的时间值，也就是一个字符串会在 `<span>` 中显示。最后和其他表单项的值一起提交给后端接口。<br>

**问题一：**表单填写完提交时没有 `<span>` 元素中的值。<br>
**原因：**因为 `form` 默认只将带 `name` 属性的 `input` 标签的值提交。`<span>` 元素没有这个属性，需要手动设置它的值。
这里我是在打开设置时间规则组件时通过 `setFieldsValue` 将值赋给 `FormItem`。<br>

```javascript
  const getTimeRules = (value) => {
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