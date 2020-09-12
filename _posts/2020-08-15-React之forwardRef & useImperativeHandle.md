---
layout:     post
title:      React之forwardRef & useImperativeHandle
date:       2020-08-15
author:     violetks
header-img: img/tag-bg-o.jpg
catalog: true
tags:
    - React
---

### 一、forwardRef

`React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 `ref` 属性转发到其组件树下的另一个组件中。<br>
`React.forwardRef` 接受渲染函数作为参数。React 将使用 `props` 和 `ref` 作为参数来调用此函数。此函数应返回 React 节点。<br>

```javascript
// 通过 forwardRef 创建子组件
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 调用子组件
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

在上述的示例中，React 会将 `<FancyButton ref={ref}>` 元素的 `ref` 作为第二个参数传递给 `React.forwardRef` 函数中的渲染函数。该渲染函数会将 `ref` 传递给 `<button ref={ref}>` 元素。<br>
因此，当 React 附加了 `ref` 属性之后，`ref.current` 将直接指向 `<button>` DOM 元素实例。<br>

### 二、useImperativeHandle

```javascript
useImperativeHandle(ref, createHandle, [deps])
```

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。`useImperativeHandle` 应当与 `forwardRef` 一起使用：<br>

```javascript
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```

在本例中，渲染 `<FancyInput ref={inputRef} />` 的父组件可以调用 `inputRef.current.focus()。`<br>

### 三、使用场景

使用步骤条处理事件时，每一步有一个表单，可分别定义为一个组件，并且有一个共同的父组件。<br>
可以通过 `forwardRef` 创建子组件，在子组件中使用 `useImperativeHandle` 方法将每一步的表单值暴露给父组件。<br>

```html
// 父组件
import React, { useEffect, useState, useRef } from 'react';
import { useDispatch } from 'dva';
import { Button, Steps, Row, Space } from 'antd';

const { Step } = Steps;

const Parent = () => {
  const dispatch = useDispatch();
  
  const [step, setStep] = useState(0);
  const [valueOne, setValueOne] = useState({});      // 子组件第一步表单值
  const [valueTwo, setValueTwo] = useState({});      // 子组件第二步表单值
  const [valueThree, setValueThree] = useState({});  // 子组件第三步表单值

  // 子组件ref
  const stepOne = useRef();
  const stepTwo = useRef();
  const stepThree = useRef();

  const nextStep = () => {
    switch (step) {
      case 0: stepOne.current.nextStep(); break;
      case 1: stepTwo.current.nextStep(); break;
      case 2: stepThree.current.commit(); break;
      default: break;
    }
  }

  return (
    <>
      <Steps current={step} className={styles.steps_btn}>
        <Step key="0" title="第一步页面" />
        <Step key="1" title="第二步页面" />
        <Step key="2" title="第三步页面" />
      </Steps>
      {step === 0 &&
        <StepOne ref={stepOne} setStep={setStep} valueOne={valueOne} setValueOne={setValueOne} taskId={taskId} />}
      {step === 1 &&
        <StepTwo ref={stepTwo} setStep={setStep} valueOne={valueOne}
          valueTwo={valueTwo} setValueTwo={setValueTwo} taskId={taskId} />}
      {step === 2 &&
        <StepThree ref={stepThree} setStep={setStep} valueOne={valueOne} valueTwo={valueTwo}
          valueThree={valueThree} setValueThree={setValueThree} taskId={taskId} />}
    </>
    <div className={styles.steps_action}>
      <Row justify="center">
        <Space size={12}>
          {step > 0 && <Button onClick={() => setStep(i => i - 1)}>上一步</Button>}
          {step < 2 && <Button type="primary" onClick={nextStep}>下一步</Button>}
          {step === 2 && <Button type="primary" onClick={nextStep} >提交</Button>}
        </Space>
      </Row>
    </div>
  )
}

export default Parent;
```

```html
// 子组件
import React, { useImperativeHandle, forwardRef } from 'react';
import { Form } from 'antd';

const StepOne = ({ setStep, setValueOne, valueOne, taskId }, ref) => {
  const [form] = Form.useForm();

  // 将子组件的方法向上暴露，将下一步方法暴露给父组件
  useImperativeHandle(ref, () => ({
    nextStep: () => {
      form.validateFields().then(values => {
        setValueOne(params);  // 表单值传给父组件
        setStep(i => i + 1);
      })
    }
  }));

  return (
    <Form form={form} {...formItemLayout}>
      ...
    </Form>
  )
}

export default forwardRef(StepOne);
```