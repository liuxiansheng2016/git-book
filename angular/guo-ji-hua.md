# 国际化

使用TranslateModule 和TranslateLoaderFactory 工厂类来加载国际化的文件

结合 `TranslateService` 来动态切换语言

### 实现工厂类（来动态加载和合并翻译文件）

TranslateLoaderFactory 这是一个工厂类，返回一个新的类 LazyTranslateLoader，该类实现了 TranslateLoader 接口。

```javascript
export class TranslateLoaderFactory {
  static forModule(module: string): any {
    return class LazyTranslateLoader implements TranslateLoader {
      getTranslation(lang: string): Observable<any> {
        return forkJoin([
          from(
            import(`../../../../assets/i18n/${module}/default.json`)
          ).pipe(catchError(() => of({}))),
          from(
            import(`../../../../assets/i18n/${module}/${lang}.json`)
          ).pipe(catchError(() => of({})))
        ]).pipe(
          map(([defaultTranslation, brandTranslation]) => {
            // 创建一个新的目标对象，以确保只读属性可以被修改。
            const newDefaultTranslation = { ...defaultTranslation };
            const merge = mergeDeep(newDefaultTranslation, brandTranslation);
            return merge;
          })
        );
      }
    };
  }
}

```

## 模块导入

```javascript
import { SharedModule } from 'path-to-shared-module';
import { TranslateModule } from '@ngx-translate/core';
import { TranslateLoaderFactory } from 'path-to-translate-loader-factory';

@NgModule({
  imports: [
    SharedModule,
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useClass: TranslateLoaderFactory.forModule('core')
      },
      isolate: true
    })
  ]
})

```

## 使用 TranslateService

```
import { TranslateService } from '@ngx-translate/core';
```

通过Providers: \[brandTranslateService] 使用：

```javascript
export class BrandTranslateService {
    brandTranslateParam: any;

    constructor(
        private readonly translateService: TranslateService,
        private readonly appSettingsService: AppSettingsService
    ) {}

    useBrand(brand?: string): void {
        this.translateService.setDefaultLang(
            this.appSettingsService.brandContent?.defaultTranslateBrand ?? WhitelabelConstants.DEFAULT_BRAND_NAME
        );

        this.translateService.use(brand ?? this.appSettingsService.brand);

        this.brandTranslateParam = {
            displayedBrandName: this.appSettingsService.brandContent?.displayedBrandName,
            phoneNumber: this.appSettingsService.contactInfo?.phoneNumber,
            displayedShortDomain: this.appSettingsService.brandContent?.displayedShortDomain
        };
    }

    getTextT(searchString: string): Observable<T> {
        if (searchString === null || searchString === '') {
            return throwError('invalid searchString');
        }

        this.translateService.setDefaultLang(
            this.appSettingsService.brandContent?.defaultTranslateBrand ?? WhitelabelConstants.DEFAULT_BRAND_NAME
        );

        this.translateService.use(this.appSettingsService.brand);

        return this.translateService.get(searchString).pipe(
            map((result) => {
                let jsonString = JSON.stringify(result).replace(
                    new RegExp(`{displayedBrandName}`, 'g'),
                    this.appSettingsService.brandContent?.displayedBrandName
                );
                return JSON.parse(jsonString);
            })
        );
    }
}

```

## 在模板中使用管道

在html 里面使用

```html
<!-- app.component.html -->
<h1>{{ 'WELCOME' | translate }}</h1>
<p>{{ 'HELLO' | translate: {value: 'World'} }}</p>

```

或者在js中使用

```javascript
this.brandTranslateService.getText('WELCOME')
    .subscribe((res: string) => { 
        this.welcomeMessage = res; 
    });
```
