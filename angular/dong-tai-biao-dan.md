# 动态表单

### **模板驱动表单**

\
模板驱动表单是基于模板的表单处理方式，<mark style="color:red;">主要通过在模板中使用指令（如</mark> <mark style="color:red;"></mark><mark style="color:red;">`ngModel`</mark><mark style="color:red;">）来实现表单的双向数据绑定和验证</mark>。<mark style="color:red;">它适用于简单的表单场景，代码主要集中在模板中</mark>。例如：\
收起html

```
<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm)">
  <input type="text" name="username" [(ngModel)]="username" required>
  <button type="submit">Submit</button>
</form>
```

### **响应式表单**

\
响应式表单是基于响应式编程思想的表单处理方式，<mark style="color:red;">通过在组件类中创建表单控件和表单组来管理表单状态，提供了更强大的表单验证和控制功能</mark>，适用于复杂的表单场景。例如：

1. FormGroup：表示一组控件（FormControl），可以是简单的输入字段，也可以是其他 FormGroup 或 FormArray。

```javascript
const form = new FormGroup({
    password: new FormControl('', Validators.minLength(2)),
    passwordConfirm: new FormControl('', Validators.minLength(2)),
  }, passwordMatchValidator);
  
  function passwordMatchValidator(g: FormGroup) {
    return g.get('password').value === g.get('passwordConfirm').value
      ? null : {'mismatch': true};
  }
```

2. FormControl：表示单个表单控件，例如文本框、复选框等。你可以为每个 FormControl 添加验证器（Validators）。
3. &#x20;FormArray：表示一个动态数组，其中可以包含多个 FormControl 或 FormGroup。这对于实现动态添加或移除表单字段非常有用。

```javascript
const arr = new FormArray([
  new FormControl('Nancy', Validators.minLength(2)),
  new FormControl('Drew'),
]);

console.log(arr.value);   // ['Nancy', 'Drew']
console.log(arr.status);  // 'VALID'

```

4. FormBuilder：提供了一种简便的方式来创建 FormGroup、FormControl 和 FormArray，简化了表单构建过程。\
   this.fb.array(\[this.createItem()]) 创建了一个 FormArray，其中包含一个由 createItem 方法返回的 FormGroup。

在页面中

1. formArrayName\
   是 Angular Reactive Forms 中的一个指令，它用于将 FormArray 绑定到模板中的一个 \<div> 或其他容器元素。使用 formArrayName 指令可以让 Angular 知道哪个 DOM 元素对应于表单模型中FormArray
2. formGroup 指令用于将一个 FormGroup 实例绑定到一个 \<form> 元素或任何其他容器元素上。
3. formControlName 指令用于将一个 FormControl 实例绑定到一个具体的输入元素上。每个带有 formControlName 的输入元素都对应于 FormGroup 中的一个键值对。
