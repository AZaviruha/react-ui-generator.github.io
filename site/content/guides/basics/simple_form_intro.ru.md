---
title: "Простейшая сгенерированная форма"
description: ""
weight: 1
---

В данном примере мы рассмотрим самые основы работы с генератором форм. Перед нами стоит следующая задача: сгенерировать форму регистрации пользователя, содержащую три поля и две кнопки. Задача является преднамеренно упрощенной, чтобы не приходилось отвлекаться на детали бизнес-логики. В последующих статьях мы рассмотрим, как сгененрировать более сложные формы.

Поля будут следующие: "E-mail", "Пароль", "Подтверждение пароля".<br>
Кнопки: "Отправить" и "Очистить".

Начнем мы с создания простейшей структуры реакт-приложения:

```js
// App.jsx

import React from 'react';
import ReactDOM from 'react-dom'

class App extends React.Component {
  render() {
    return (
        <div>placeholder</div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('root'))
```

Пока-что, ничего интересного в этом коде нет - всего лишь создание пустого реакт-компонента, который выводит единственный `div` с текстом "placeholder".
Чтобы добавить в это приложение сгенерированную форму, для начала нужно сделать импорт необходимых компонентов:


```js
// App.jsx

import React from 'react';
import ReactDOM from 'react-dom'

import { GeneratedForm, Fields } from '@react-ui-generator/core'

...
```

Здесь мы импортировали два важнейших компонента реакта. Из названия, вероятно, уже стало понятно чем занимается компонент `GeneratedForm` - очевидно, генерацией формы. А вот каково предназначение компонента `Fields` станет понятно чуть позже.

Добавим инстанс формы в приложение:

```js
// App.jsx

import React from 'react';
import ReactDOM from 'react-dom'
import { GeneratedForm, Fields } from '@react-ui-generator/core'

class App extends React.Component {
  render() {
    return (
        <GeneratedForm
            meta={/* ??? */}
            data={/* ??? */}
            errors={/* ??? */}
            renderers={/* ??? */}
            onChange={/* ??? */}
        />
    );
  }
}

ReactDOM.render(<App />, document.getElementById('root'))
```

Как видим, форма содержит пять свойств (на самом деле их [больше](/#todo-link-to-api)), которые пока-что отмечены вопросительными знаками.
Собственно, заполнением этих свойств правильными значениями мы и займемся в оставшейся части статьи. Всех, кроме `errors` - это свойство относится к валидации формы, которой будет посвящена отдельная статья.

## `meta`

Свойство `meta` содержит мета-описание нашей формы. Мета-описание являет собою js-объект, описывающий структуру желаемой формы, включая набор полей, их названия, типы и т.д.

Вот как будет выглядеть мета-описание для нашей формы:

```js
// sign-up-meta.json

{
  "fields": [
    {
      "id": "email",
      "renderer": "text",
    },

    {
      "id": "password",
      "renderer": "text",
    },

    {
      "id": "confirmation",
      "renderer": "text",
    },

    {
      "id": "btnSend",
      "renderer": "button",
    },

    {
      "id": "btnClear",
      "renderer": "button",
    }
  ]
}

```

Как видите, в самом простом своем представлении мета-данные выглядят достаточно... просто. На самом деле здесь не хватает многих важных настроек, которые в итоге значительно увеличат размер нашего мета-описания, но - есть надежда - не слишком усложнят его для понимания.

Итак, в этом мета-описании мы задаем единственное верхнеуровневое свойство `fields`, содержаее массив с объектами-описаниями полей.
Каждое поле описывается двумя свойствами: `id` и `renderer`, последнее из которых описывает тип UI-компонента, которым данное поле будет представлено на форме.

## `data`

Свойство `data` компонента `GeneratedForm` содержит текущее значение полей формы. Как сказано в описании _React UI Generator_, генерируемые им формы являются **чистыми** компонентами, а это значит, что все данные, которые отображает форма, должны приходить к ней в виде `props`.

Структура объекта, использующегося в качестве значения `data`, такова:

```js
// App.jsx

...

const initialData = {
    email: { value: '', isDirty: false },
    password: { value: '', isDirty: false },
    confirmation: { value: '', isDirty: false },
}

...

class App extends React.Component {
    ...
}
```

Можно сказать, что `data` содержит актуальное *состояние* каждого поля: его значение и признак того, что в поле уже происходил пользовательский ввод.
Однако, забегая наперед, скажу что данное свойство НЕ содержит ошибки валидации поля. Для простоты манипулирования ими, ошибки вынесены в отдельное свойство `errors`, к которому мы ещё вернемся.

Пока же перейдем к следующему свойству нашего инстанса `GeneratedForm`.

## `renderers`

Это, пожалуй, самое интересное свойство, если можно так сказать. Именно оно определяет, какие в принципе типы полей допустимы для данной формы. По-сути, `renderers` содержит коллекцию функций-отрисовщиков, каждая из которых умеет отрисовывать поле определенного типа: `text`, `button` и пр.

Для того, чтобы заполнить это поле из нашего пример, нам потребуется добавить ещё один `import`:


```js
// App.jsx

import React from 'react';
...
import { Renderers } from '@react-ui-generator/bootstrap'

...

class App extends React.Component {
  render() {
    return (
        <GeneratedForm
            ...
            renderers={Renderers}
            ...
        />
    );
  }
}

...
```

Для тех кому любопытно, сразу скажу, что `Renderers` имеет очень простую структуру:

```js
{
    'text': function () { ... },
    'button': function () { ... },
    ...
}
```

Мы поговорим об устройстве renderers очень детально, когда дойдем до создания собственных типов полей. А сейчас лучше поговорим об `onChange` и затем перейдем к тому, чтобы собрать все вместе.

## `onChange`

Если три предыдущие свойства задают структуру, внешний вид и содержимое формы, то свойство `onChange` - это то, что делает форму живой. Именно благодаря этому свойству форма становится интерактивной, начинает реагировать на действия пользователя и т.д.

Свойствой принимает в качестве значения функцию-callback, со следующей сигнатурой:

```js
function (nextData, nextErrors, nextIsValid) {
  ...
}
```

где:

  - `nextData` - это предыдущее значение, переданное в свойство `data` плюс изменение, вызванное пользовательским вводом;
  - `nextErrors` - результат валидации объекта `nextData`;
  - `nextIsValid` - простой boolean-признак валиданости формы. Устанавливается в false, если `nextErrors` содержит хотя бы одну ошибку.

Валидацию формы, как уже было сказано, мы детально рассмотрим позже, а пока-что будем использовать только аргумент `nextData`. Что именно мы будем делать с этим аргументом - зависит от контекста, в котором используется генератор форм. Если, например, он используется в Redux-приложении, то скорее всего, `onChange` будет вызывать некий `action`, в который передавать `nextData` в качестве payload. Пример использования *React UI Generator* совсместно с *Redux* можно посмотреть [здесь][redux-demo-example]

В нашем же текущем примере, `onChange` будет использоваться для установки нового значения формы, посредством вызова стандартного метода React-компонента - `this.setState`.

Итак, настало время собрать все вышеописанные части формы в одно целое! :)

## Сборка формы №1

Ниже приведен исходный код React-компонента, описывающего нашу генерируемую форму:

```js
// App.jsx

import React from 'react';
import ReactDOM from 'react-dom'
import { GeneratedForm, Fields } from '@react-ui-generator/core'
import { Renderers: Renderers } from '@react-ui-generator/bootstrap'
import formMeta from './sign-up-meta.json'

const initialData = {
    email: { value: '', isDirty: false },
    password: { value: '', isDirty: false },
    confirmation: { value: '', isDirty: false },
}

class App extends React.Component {
  constructor(props) {
    this.state = {
      data: initialData,
      errors: {}
    }
  }

  handleOnChange(mextData) {
    this.setState({ data: nextData })
  }


  render() {
    return (
        <GeneratedForm
            meta={formMeta}
            data={this.state.data}
            errors={this.state.errors}
            renderers={Renderers}
            onChange={nextData => this.handleOnChange(nextData)}
        />
    );
  }
}

ReactDOM.render(<App />, document.getElementById('root'))
```

В целом, наша форма почти готова. "Почти" - потому что остался один небольшой, но крайне важный пункт: настройка layout-а формы.
В списке "основных возможностей генератора" указано, что "сложная кастомна разметка описывается в JSX". На самом деле, можно сказать, что любая разметка генератора описывается с помощью `JSX`, однако для большинства случаев это занимет всего несколько строк.

В нашем примере, описание разметки будет произведено с использованием компонента `Fields`:

```js
// App.jsx

...

class App extends React.Component {
  ...

  render() {
    return (
        <GeneratedForm
            meta={formMeta}
            data={this.state.data}
            errors={this.state.errors}
            renderers={Renderers}
            onChange={nextData => this.handleOnChange(nextData)}
        >
          <Fields />
        </GeneratedForm>
    );
  }
```

Теперь наша форма должна быть готова:

<iframe src="https://codesandbox.io/embed/zxmo92jr4l?hidenavigation=1&view=preview" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

## Сборка формы №2

Мда... результат оказался не таким впечатляющим, как мы ожидали. Поля выглядят ужасно, идентифицировать их, фактически, невозможно.
Про кнопку вообще молчу.

На самом деле, ничего неожиданного не произошло. Все дело в нашем сверх-минималистичном мета-описании формы:

```js
// sign-up-meta.json

{
  "fields": [
    {
      "id": "email",
      "renderer": "text",
    },

    {
      "id": "password",
      "renderer": "text",
    },

    {
      "id": "confirmation",
      "renderer": "text",
    },

    {
      "id": "btnSend",
      "renderer": "button",
    },

    {
      "id": "btnClear",
      "renderer": "button",
    }
  ]
}
```

... и в нашем ~~отсутствующем~~ layout-е.

Займемся их исправлением. Во-первых, добавим в мета-описание полей немного больше... описания.
Для этого используем вторую форму свойства `renderer` - объектную:

```js
// sign-up-meta.json

{
  "fields": [
    {
      "id": "email",
      "renderer": {
        "type": "text",
        "config": {
          "label": "E-mail",
          "placeholder": "Введите e-mail"
        }
      }
    },

    {
      "id": "password",
      "renderer": {
        "type": "text",
        "config": {
          "label": "E-mail",
          "placeholder": "Введите пароль",
          "isPassword": true
        }
      }
    },

    {
      "id": "confirmation",
      "renderer": {
        "type": "text",
        "config": {
          "label": "E-mail",
          "placeholder": "Введите пароль повторно",
          "isPassword": true
        }
      }
    },

    {
      "id": "btnSend",
      "renderer": {
        "type": "button",
        "config": {
          "title": "Send",
          "color": "dark"
        }
      }
    },

    {
      "id": "btnClear",
      "renderer": {
        "type": "button",
        "config": {
          "title": "Clear",
          "color": "dark"
        }
      }
    }
  ]
}
```

Посмотрим на промежуточный результат:

<iframe src="https://codesandbox.io/embed/k9j902mqn7?hidenavigation=1&view=preview" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

Уже гораздо лучше, но все ещё далеко до примемлемого результата. Давайте попробуем немного улучшить разметку формы, добавив один из layout-ов, из пакета `@react-ui-generator/bootstrap`.

```js
// App.jsx

import React from 'react';
...
import { Renderers, Layouts } from '@react-ui-generator/bootstrap'

const { FormGroups } = Layouts;

...

class App extends React.Component {
  render() {
    return (
        <GeneratedForm
            ...
            renderers={Renderers}
            ...
        >
          <div className="card border-dark mb-3">
            <div className="card-body">
              <FormGroups>
                <Fields />
              </FormGroups>
            </div>
          </div>
        </GeneratedForm>
    );
  }
}

...
```

Как видимо, наш layout действительно становится "кастомным", и причем - очень легко, т.к. вся разметка описывается прямо внутри формы, с использованием старого-доброго *JSX*.  

`<FormGroup />` здесь - ничто иное, как компонент-обертка над [одноименным css-компонентом][bootstrap-form-group] из *Bootstrap 4*. Все что данная обертка делает - это берет каждое отрендеренное поле из мета-описание, и обрамляет его соответствующей разметкой.

<iframe src="https://codesandbox.io/embed/m4wqzx5rx?hidenavigation=1&view=preview" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

Ну, вот это уже выглядит вполне ничего. Единственный момент, который "режет глаз" - это то, что теперь и каждая кнопка отрисовываетс в отдельной FormGroup-е, а хотелось бы все-таки расположить их на одной линии.

Для достижения этой цели мы воспользуемся свойством `until` у компонента `Fields`. С его помощью можно указать, какие поля исключить из текущего процеса отрисовки.
Однако, нам ведь нужно не *совсем* убрать кнопки, а просто вынести из за пределы `<FormGroups />`, и, например, добавить пробел между кнопками, чтобы они не сливались.

Для этого воспользуемся компонентом `<Field>`, с помощью которого мы можем нарисовать отдельное поле, указав его `id`:

```js
// App.jsx

import React from 'react';
import { GeneratedForm, Fields, field } from '@react-ui-generator/core'
import { Renderers, Layouts } from '@react-ui-generator/bootstrap'

...

class App extends React.Component {
  render() {
    return (
        <GeneratedForm
            ...
            renderers={Renderers}
            ...
        >
          <div className="card border-dark mb-3">
            <div className="card-body">
              <FormGroups>
                <Fields until="btnSend" />
              </FormGroups>

              <hr />

              <Field id="btnSend" />{' '}<Field id="btnClear" />
            </div>
          </div>
        </GeneratedForm>
    );
  }
}

...
```

Посмотрим, что у нас получилось на этот раз:

<iframe src="https://codesandbox.io/embed/1v47w04vxq?hidenavigation=1&view=preview" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

Наконец-то, наша форма имеет вполне приемлемый вид. За счет написания сугубо декларативного кода и мета-данных, мы получили вполне функциональную форму. Думаю, не нужно объяснять, что реальных *enterprise*-формах, которые имеют от десяти полей и больше, преимущество использования *React UI Generator* будут становиться все более очевидным.

Следующий этап, которым мы займемся во второй статье цикла, - это валидация формы, а также её сериализация для отправки на сервер. После прочтения второй обучающей статьи Вы будете вполне готовы к использованию имеющегося функционала генератора.

Тому, как этот функционал расширить под свои конкретные нужны - будут посвящены последующие статьи.

[redux-demo-example]: https://github.com/react-ui-generator/react-ui-generator/tree/master/packages/demo/src/
[bootstrap-form-group]: https://getbootstrap.com/docs/4.0/components/forms/#form-groups
