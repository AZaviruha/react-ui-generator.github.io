# React UI Generator

Семейство библиотек и утилит для генерации GUI из метаданных, с помощью библиотеки React.


{{%panel%}}React UI Generator позволяет разработчику сократить время, затрачиваемое на разработку однообразных CRUD-форм, сосредоточившись вместо этого на решении действительно сложных и интересных задач.{{%/panel%}}

<blockquote class="text-to-right">
<p><quote>Let amazingResult be the result of doing some amazing things.</quote></p>
<i>"DOM Living Standard"</i>
</blockquote>

## Основные возможности генератора:

- Позволяет легко сгенерировать форму из мета-описания. Результатом является полноценный React-компонент.
- Сложная кастомная разметка описывается в JSX (не в метаданных, потому что разметка-в-json - это боль).
- Позволяет легко добавлять собственные типы полей (renderers).
- Позволяет легко дабавлять собственные варианты разметки (layouts)
- Чистый компонент! Никакого state, только props. За счет чего достигается легкость интеграции с фреймворками (Redux, etc).
- Поддерживает вложенные формы. Вы можете вкладывать одну сгенерированную форму в другую, с помощью компонентов SubForm и ListForm.
- Динамические формы. Манипулируя метаданными вы можете на лету изменять структуру сгенерированного компонента.

Пример сгенерированной формы
![demo-screenshot](/img/demo-screen.png)

На этом скриншоте - форма, сгенерированная с помощью набора рендререров Bootstrap.
Мета-описание данной формы находится [здесь][metadata-example].

## Интерактивный демо-пример.
<iframe
    src="https://codesandbox.io/embed/new"
    style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
    sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
/>

## Архитектурный принцип

![demo-screenshot](/img/generator-architecture.png)

## TODO
- complete this README.md
- see [Roadmap][roadmap]

[metadata-example]: https://github.com/react-ui-generator/react-ui-generator/tree/master/packages/demo/src/meta/complete.json
[roadmap]: https://github.com/react-ui-generator/react-ui-generator/tree/master/TODO
