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

```javascript
const hasSelected = selectedRowKeys.length > 0;

<div style={{ display: hasSelected ? 'block' : 'none', marginBottom: '16px' }}>
  <Alert
    message={
     <p>
       <span>已选择 {selectedRowKeys.length} 项</span>
       <a onClick={resetChecked} style={{ marginLeft: '24px' }}>清空</a>
     </p>
    }
    type="info"
    showIcon
   />
</div>
```

### 二、`Form` 表单高级搜索

收起时只显示前两个 `FormItem` ，展开显示全部。<br>

```javascript
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
            <Col span={8} style={{ display: expand ? 'block' : 'none' }}>
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
            <Col span={8} style={{ display: expand ? 'block' : 'none' }}>
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
            <Col span={expand ? 16 : 8} style={{ textAlign: expand ? "right" : "left" }}>
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