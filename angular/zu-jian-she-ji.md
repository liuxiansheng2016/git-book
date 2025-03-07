# 组件设计

### **方法 1：使用 `@angular/material` 实现 Dialog**

`MatDialog` 提供了开箱即用的对话框组件，使用简单，适合大多数场景。

#### **步骤 1：安装 `@angular/material`**

如果你还没有安装 Angular Material：

```bash
ng add @angular/material
```

#### **步骤 2：导入 `MatDialogModule`**

在 `app.module.ts` 或者 `feature.module.ts` 中：

```typescript
import { NgModule } from '@angular/core';
import { MatDialogModule } from '@angular/material/dialog';

@NgModule({
  imports: [MatDialogModule],
})
export class AppModule {}
```

#### **步骤 3：创建 Dialog 组件**

创建 `dialog.component.ts`，这个组件会作为弹窗的内容：

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-dialog',
  template: `
    <h2 mat-dialog-title>确认操作</h2>
    <mat-dialog-content>你确定要执行这个操作吗？</mat-dialog-content>
    <mat-dialog-actions align="end">
      <button mat-button mat-dialog-close>No</button>
      <button mat-button [mat-dialog-close]="true">Yes</button>
    </mat-dialog-actions>
  `,
})
export class DialogComponent {}
```

#### **步骤 4：在主组件中打开 Dialog**

在 `app.component.ts`（或其他组件）中：

```typescript
import { Component } from '@angular/core';
import { MatDialog } from '@angular/material/dialog';
import { DialogComponent } from './dialog.component';

@Component({
  selector: 'app-root',
  template: `<button mat-button (click)="openDialog()">打开对话框</button>`,
})
export class AppComponent {
  constructor(private dialog: MatDialog) {}

  openDialog(): void {
    const dialogRef = this.dialog.open(DialogComponent, {
      width: '300px',
    });

    dialogRef.afterClosed().subscribe(result => {
      console.log('对话框关闭，返回值:', result);
    });
  }
}
```

***

### **方法 2：使用 `@angular/cdk` 实现 Dialog**

如果你不想依赖 Material，而是用 `@angular/cdk` 来实现更轻量级的 Dialog，需要使用 `OverlayModule`。

#### **步骤 1：安装 `@angular/cdk`**

如果还没有安装：

```bash
ng add @angular/cdk
```

#### **步骤 2：导入 `OverlayModule`**

在 `app.module.ts` 中：

```typescript
import { NgModule } from '@angular/core';
import { OverlayModule } from '@angular/cdk/overlay';

@NgModule({
  imports: [OverlayModule],
})
export class AppModule {}
```

#### **步骤 3：创建自定义 Dialog 服务**

在 `dialog.service.ts`：

```typescript
import { Injectable, ComponentRef, Injector } from '@angular/core';
import { Overlay, OverlayRef } from '@angular/cdk/overlay';
import { ComponentPortal } from '@angular/cdk/portal';
import { DialogComponent } from './dialog.component';

@Injectable({ providedIn: 'root' })
export class DialogService {
  private overlayRef!: OverlayRef;

  constructor(private overlay: Overlay, private injector: Injector) {}

  open(): void {
    this.overlayRef = this.overlay.create({
      hasBackdrop: true,
      backdropClass: 'cdk-overlay-dark-backdrop',
      positionStrategy: this.overlay.position()
        .global()
        .centerHorizontally()
        .centerVertically(),
    });

    // 创建组件
    const dialogPortal = new ComponentPortal(DialogComponent);
    this.overlayRef.attach(dialogPortal);

    // 监听背景点击，关闭对话框
    this.overlayRef.backdropClick().subscribe(() => this.close());
  }

  close(): void {
    this.overlayRef.dispose();
  }
}
```

#### **步骤 4：创建 `DialogComponent`**

与 `MatDialog` 方法类似，在 `dialog.component.ts`：

```typescript
import { Component } from '@angular/core';
import { DialogService } from './dialog.service';

@Component({
  selector: 'app-dialog',
  template: `
    <div class="dialog">
      <h2>确认操作</h2>
      <p>你确定要执行这个操作吗？</p>
      <div class="actions">
        <button (click)="close()">取消</button>
        <button (click)="confirm()">确认</button>
      </div>
    </div>
  `,
  styles: [`
    .dialog {
      background: white;
      padding: 20px;
      border-radius: 8px;
      width: 300px;
      text-align: center;
      box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
    }
    .actions {
      display: flex;
      justify-content: space-around;
      margin-top: 10px;
    }
  `]
})
export class DialogComponent {
  constructor(private dialogService: DialogService) {}

  close(): void {
    this.dialogService.close();
  }

  confirm(): void {
    console.log('确认操作');
    this.dialogService.close();
  }
}
```

#### **步骤 5：在组件中使用 Dialog**

在 `app.component.ts`：

```typescript
import { Component } from '@angular/core';
import { DialogService } from './dialog.service';

@Component({
  selector: 'app-root',
  template: `<button (click)="openDialog()">打开对话框</button>`,
})
export class AppComponent {
  constructor(private dialogService: DialogService) {}

  openDialog(): void {
    this.dialogService.open();
  }
}
```

***

### **总结**

| 方法                      | 适用场景                      | 主要特点                |
| ----------------------- | ------------------------- | ------------------- |
| **`@angular/material`** | 需要快速实现 Material 风格 Dialog | API 简洁、功能丰富，开箱即用    |
| **`@angular/cdk`**      | 需要更轻量级、可自定义的 Dialog       | 更灵活，但需要手动管理 Overlay |

你可以创建一个可复用的 Angular 验证码输入组件，该组件支持自动聚焦、粘贴识别、长度限制等功能。下面是一个完整的实现，包括输入框自动跳转、支持粘贴验证码等功能。

#### 组件功能：

1. **输入框自动跳转**：输入一位后自动跳转到下一个框，删除时自动回到前一个框。
2. **支持粘贴验证码**：可以直接粘贴完整验证码，会自动填充到所有输入框。
3. **双向绑定**：支持 `ngModel` 绑定数据，外部可获取验证码输入结果。

***

### **实现验证码输入组件**

#### 1️⃣ **创建组件**

```bash
ng generate component otp-input
```

***

#### 2️⃣ **编写 `otp-input.component.ts`**

```typescript
import { Component, ElementRef, EventEmitter, Input, Output, QueryList, ViewChildren } from '@angular/core';

@Component({
  selector: 'app-otp-input',
  templateUrl: './otp-input.component.html',
  styleUrls: ['./otp-input.component.scss']
})
export class OtpInputComponent {
  @Input() length: number = 6; // 验证码长度
  @Input() type: string = 'text'; // 支持 'text' 或 'number'
  @Output() otpComplete = new EventEmitter<string>(); // 当输入完成时触发
  @ViewChildren('otpInput') otpInputs!: QueryList<ElementRef>;

  otpValues: string[] = Array(this.length).fill('');

  constructor() {}

  /**
   * 处理输入事件
   */
  handleInput(event: Event, index: number): void {
    const input = event.target as HTMLInputElement;
    const value = input.value.replace(/\D/g, '').slice(0, 1); // 仅保留单个字符
    this.otpValues[index] = value;

    // 自动跳转到下一个输入框
    if (value && index < this.length - 1) {
      this.otpInputs.get(index + 1)?.nativeElement.focus();
    }

    // 检查是否完成输入
    const otp = this.otpValues.join('');
    if (otp.length === this.length) {
      this.otpComplete.emit(otp);
    }
  }

  /**
   * 处理按键事件（Backspace 删除时回到前一个框）
   */
  handleKeydown(event: KeyboardEvent, index: number): void {
    if (event.key === 'Backspace' && index > 0 && !this.otpValues[index]) {
      this.otpInputs.get(index - 1)?.nativeElement.focus();
    }
  }

  /**
   * 处理粘贴事件
   */
  handlePaste(event: ClipboardEvent): void {
    event.preventDefault();
    const pasteData = event.clipboardData?.getData('text') || '';
    const otpChars = pasteData.replace(/\D/g, '').slice(0, this.length).split('');

    if (otpChars.length === this.length) {
      this.otpValues = otpChars;
      this.otpComplete.emit(this.otpValues.join(''));
      this.otpInputs.get(this.length - 1)?.nativeElement.focus();
    }
  }
}
```

***

#### 3️⃣ **编写 `otp-input.component.html`**

```html
<div class="otp-container">
  <input
    *ngFor="let value of otpValues; let i = index"
    #otpInput
    [type]="type"
    maxlength="1"
    [(ngModel)]="otpValues[i]"
    (input)="handleInput($event, i)"
    (keydown)="handleKeydown($event, i)"
    (paste)="handlePaste($event)"
    class="otp-input"
  />
</div>
```

***

#### 4️⃣ **编写 `otp-input.component.scss`**

```scss
.otp-container {
  display: flex;
  gap: 8px;
  justify-content: center;
}

.otp-input {
  width: 40px;
  height: 50px;
  font-size: 20px;
  text-align: center;
  border: 2px solid #ccc;
  border-radius: 5px;
  outline: none;
  transition: border-color 0.3s;

  &:focus {
    border-color: #007bff;
  }
}
```

***

### **组件使用方式**

```html
<app-otp-input [length]="6" [type]="'number'" (otpComplete)="onOtpComplete($event)"></app-otp-input>
```

```typescript
onOtpComplete(otp: string) {
  console.log('输入的验证码:', otp);
}
```

***

