# Composition API

The introduction of `setup` and Vue’s [Composition API](https://v3.vuejs.org/guide/composition-api-introduction.html) open up new possibilities. But to be able to get the full potential out of Vue I18n, we will need to use a few new functions to replace access to `this`.

We have been describing the features of Vue I18n using the Legacy API, which is compatible with vue-i18n v8.x. Now let’s take a look at Vue I18n `useI18n` for Composition API.

## Basic Usage

Let’s look at the basic usage of Vue I18n Composition API! Here we will learn the basic usage by modifying the code in [Getting Started](../essentials/started) to learn the basic usage.

To compose with `useI18n` in `setup` of Vue 3, there is one thing you need to do, you need set the `legacy` option of the `createI18n` function to `false`.

The following is an example of adding the `legacy` option to `createI18n`:

```js{5}
// ...

// 2. Create i18n instance with options
const i18n = VueI18n.createI18n({
  legacy: false, // you must set `false`, to use Compostion API
  locale: 'ja', // set locale
  fallbackLocale: 'en' // set fallback locale
  messages, // set locale messages
  // If you need to specify other options, you can set other options
  // ...
})

// ...
```

You can set `legacy: false` to allow Vue I18n to switch the API mode, from Legacy API mode to Composition API mode.

:::tip NOTE
The following properties of i18n instance created by `createI18n` change it’s behavior:

- `mode` property: `"legacy"` to `"composition"`
- `global` property: VueI18n instance to Composer instance
:::

You are now ready to use `useI18n`. Now you are ready to use `useI18n` in the component `setup`. The code looks like this:

```js{5-8}
// ...

// 3. Create a vue root instance
const app = Vue.createApp({
  setup() {
    const { t } = VueI18n.useI18n() // call `useI18n`, and spread `t` from  `useI18n` returning
    return { t } // return render context that included `t`
  }
})

// ...
```

You must call `useI18n` at top of the `setup`.

The `useI18n` returns a Composer instance. The Compser instance provides a translation API such as the `t` function, as well as properties such as `locale` and `fallbackLocale`, just like the VueI18n instance. For more information on the Composer instance, see the [API Reference](../api/).

In the above example, there are no options for `useI18n`, so it returns a Composer instance that works with the global scope. As such, it returns a Composer instance that works with the global scope, which means that the localized message referenced by the spread `t` function here is the one specified in `createI18n`. This means that the locale message referenced by the spread `t` function here will be the locale message specified in `createI18n`.

By returning `t` as render context in the `setup`, you can use `t` in the components template:

```html{2}
<div id="app">
  <p>{{ t("message.hello") }}</p>
</div>
```

The output follows:

```html{2}
<div id="app">
  <p>こんにちは、世界</p>
</div>
```

## Message Translation

In the Legacy API mode, The messages were translated using either `$t` or the VueI18n instance of `t` to translate the message.

In the Compostion API mode, the Message Format Syntax remains the same as in the Legacy API mode. You can use the Composer instance `t` to translate a message as follows:

```html
<template>
  <p>{{ t('named', { msg }) }}</p>
  <p>{{ t('list', [msg]) }}</p>
  <p>{{ t('literal') }}</p>
  <p>{{ t('linked') }}</p>
</template>

<script>
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t } = useI18n({
      locale: 'en',
      messages: {
        en: {
          msg: 'hello',
          named: '{msg} world!',
          list: '{0} world!',
          literal: "{'hello'} world!",
          the_world: 'the world',
          dio: 'DIO:',
          linked: '@:dio @:the_world !!!!'
        },
        ja: {
          msg: 'こんにちは',
          named: '{msg} 世界！',
          list: '{0} 世界！',
          literal: "{'こんにちは'} 世界！",
          the_world: 'ザ・ワールド！',
          dio: 'ディオ:',
          linked: '@:dio @:the_world ！！！！'
        }
      }
    })

    const msg = computed(() => t('msg'))

    return { t, msg }
  }
}
</script>
```

For more detals of `t`, see the [API Reference](../api/).

## Pluralization

In the Legacy API mode, the plural form of the message was translated using either `$tc` or the VueI18n instance of `tc` to translate the message.

In the Compostion API mode, the plural form of the message is left in sytax as in the Legacy API mode, but is translated using the `t` of the Composer instance:

```html
<template>
  <h2>Car:</h2>
  <p>{{ t('car', 1) }}</p>
  <p>{{ t('car', 2) }}</p>
  <h2>Apple:</h2>
  <p>{{ t('apple', 0) }}</p>
  <p>{{ t('apple', 1) }}</p>
  <p>{{ t('apple', { count: 10 }, 10) }}</p>
  <p>{{ t('apple', 10) }}</p>
  <h2>Banana:</h2>
  <p>{{ t('banana', { n: 1 }, 1) }}</p>
  <p>{{ t('banana', 1) }}</p>
  <p>{{ t('banana', { n: 'too many' }, 100) }}</p>
</template>

<script>
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t } = useI18n({
      locale: 'en',
      messages: {
        en: {
          car: 'car | cars',
          apple: 'no apples | one apple | {count} apples',
          banana: 'no bananas | {n} banana | {n} bananas'
        }
      }
    })

    return { t }
  }
}
</script>
```

:::tip NOTE
In the Composition API mode, plural translations have been integrated into `t`.
:::

## Datetime Localization

In the Legacy API mode, Datetime value was localized using `$d` or the VueI18n instance of `d`.

In the Compostion API mode, it uses the `d` of the Composer instance to localize:

```html
<template>
  <p>{{ t('current') }}: {{ d(now, 'long') }}</p>
</template>

<script>
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t, d } = useI18n({
      locale: 'en-US',
      messages: {
        'en-US': {
          current: 'Current Datetime'
        }
      },
      datetimeFormats: {
        'en-US': {
          long: {
            year: 'numeric',
            month: '2-digit',
            day: '2-digit',
            hour: '2-digit',
            minute: '2-digit',
            second: '2-digit'
          }
        }
      }
    })

    const now = ref(new Date())

    return { t, d, now }
  }
}
</script>
```

For more detals of `d`, see the [API Reference](../api/).

## Number Localization

In the Legacy API mode, Number value is localized using `$n` or the `n` of the VueI18n instance.

In the Compostion API mode, it is localized using the `n` of the Composer instance:

```html
<template>
  <p>{{ t('money') }}: {{ n(money, 'currency') }}</p>
</template>

<script>
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t, n } = useI18n({
      locale: 'en-US',
      messages: {
        'en-US': {
          money: 'Money'
        }
      },
      numberFormats: {
        'en-US': {
          currency: {
            style: 'currency',
            currency: 'USD'
          }
        }
      }
    })

    const money = ref(1000)

    return { t, n, money }
  }
}
</script>
```

For more detals of `n`, see the [API Reference](../api/).

## Global scope

A Global Scope in the Composition API mode is created when an i18n instance is created with `createI18n`, similar to the Legacy API mode.

While the Legacy API mode `global` property of the i18n instance is the VueI18n instance, the Composition API mode allows you to reference the Composer instance.

There are two ways to refer the Global Scope Composer instance at the component.

### Explicit with `useI18n`

As we have explaiined, `setup` in `useI18n` is a way.

```js
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t } = useI18n({ useScope: 'global' })

    // Something to do here ...

    return { t }
  }
}
```

The above code sets the `useI18n` option to `useScope: 'global'`, which allows `useI18n` to return a Composer instance that can be accessed by the i18n instance `global` property. This allows `useI18n` to return the Composer instance that can be accessed by i18n instance`global` property, which is a Global scope. The Commposer instance is a Global scope.

Then you can compose using the functions and properties exposed from the Composer instance.

### Implicit with injected properties and functions

Another way to refer a global scope Composer instance is through properties and functions implicitly injected into the component.

If you use Compostion API mode with the `legacy: false` option for `createI18n`, Vue I18n defaults to the following properties and functions, which injected to the component via `app.config.globalProperties`:

- `$i18n`: An object wrapped with the following global scope Composer instance properties
  - `locale`
  - `fallbackLocale`
  - `availableLocales`
- `$t`: `t` function of Composer that is global scope
- `$d`: `d` function of Composer that is global scope
- `$n`: `n` function of Composer that is global scope
- `$tm`: `tm` function of Composer that is global scope

The Vue 3 runtime globally injects components with what is set in `app.config.globalProperties`. Thus, the ones listed above are injected by the Vue 3 runtime and can therefore be used implicitly in template.

:::warning NOTICE
- The `setup` does not allow to see these properties and functions injected into the component
- In Legacy API mode, some Vue I18n APIs prifexed with `$` were injected, but the properties and functions prefixing components with `$` and injected in Compostion API mode are different from the Legacy API mode.
:::

You’ve noticed that the ones listed above are prifixed with `$`. The reason for prefixing them with `$` is that they are:

- The `setup` does not conflict with the properties and functions returned by render context
- Global scope accessible identifier for Vue I18n Composition API mode

By doing so, the user is made aware that they are special properties and functions.

With the previously explained explicit way, you had to run `useI18n` in `setup` every time. **If your Vue application doesn’t use local scope and does everything i18n in global scope**, it is very convenient because you don’t have to run `useI18n` in the `setup` every time.


## Local scope

In Legacy API mode, VueI18n instance is created by specifying the `i18n` component option for each component. This enables resources such as local messges managed by VueI18n instance to be local scopes that could be referenced only by the target component.

To enable local scope in the Composition API mode, you need to set an option to `useI18n`, which will create a new instance of Composer based on the given locale, locale messages, etc. When the option is given, `useI18n` creates and returns a new Composer instance based on the locale, locale messages, and other resources specified by the option.

The following example codes:

```js
import { useI18n } from 'vue-i18n'

export default {
  setup() {
    const { t, d, n, tm, locale } = useI18n({
      locale: 'ja-JP',
      fallbackLocale: 'en-US',
      messages: {
        'en-US': {
          // ....
        },
        'ja-JP': {
          // ...
        }
      },
      datetimeFormats: {
        'en-US': {
          // ....
        },
        'ja-JP': {
          // ...
        }
      },
      numberFormats: {
        'en-US': {
          // ....
        },
        'ja-JP': {
          // ...
        }
      }
    })

    // Something to do here ...

    return { t, d, n, tm, locale }
  }
}
```


If you use i18n custom blocks  in SFC as i18n resource of locale messages, it  will be merged with the locale messages specified by the `messages` option of `useI18n`.

The following is an example of using  i18n custom blocks and `useI18n` options:

```html
<script>
import { useI18n } from 'vue-i18n'
import en from './en.json'

export default {
  setup() {
    const { t, availableLocales, getLocaleMessages } = useI18n({
      locale: 'en',
      messages: {
        en
      }
    })

    availableLocales.forEach(locale => {
      console.log(`${locale} locale messages`, getLocaleMessages(locale))
    })

    return { t }
  }
}
</script>

<i18n locale="ja">
{
  "hello": "こんにちは！"
}
</i18n>
```

:::tip NOTE
In this example, the definition of resources is separated from i18n custom blocks and the `messages` option of `useI18n`, but in local scope, resource messages are specified in the `messages` option in a lump sum for administrative purposes in resource messages or define all resource messages in the i18n custom blocks, which is preferable.
:::

## Locale Changing

### Global Scope

Changing the locale in the global scope, i.e., Composer instance  `locale` property  referenced by i18n instance `global` property,  you can change with `$i18n.locale`.

```html
<select v-model="$i18n.locale">
  <option value="en">en</option>
  <option value="ja">ja</option>
</select>
```

If you want to change the locale with `setup`, just get a global Composer with `useI18n` and change it using the `locale` property of the instance.

```js
setup() {
  const { t, locale } = useI18n({ useScope: 'global' })

  locale.value = 'en' // change!

  return { t, locale } // you can return it with render context!
}
```

When you change the locale of the global scope, components that depend on the global scope, such as `t` translation API can work reactive and switch the display messages to those of the target locale.

### Local Scope

The local scope locales, that is, the  Composer instance `locale` property returned by `useI18n`, are inherited from the global scope, as is the Legacy API. Therefore, when you change the locale at global scope, the inherited local scope locale is also changed. If you want to switch the locale for the whole application, you need to do so via `$i18n.locale`.

:::tip NOTE
If you do not want to inherit the locale from the global scope, the `inheritLocale` option of `useI18n` must be `false`.
:::

:::warning NOTICE
Changes to the `locale` at the local scope have **no effect on the global scope locale, but only within the local scope**.
:::

## Mapping between VueI18n instance and Composer instance

The API offered by the Composer instance in the Compostion API is very similar interface to the API provided by the VueI18n instance.

:::tip MEMO
Internally, the VueI18n instance of the Legacy API mode works by wrapping the Composer instance.
For this reason, the performance overhead is less in the Composition API mode than in the Legacy API mode.
:::

Below is the mapping table:

| VueI18n instance  | Composer instance       |
| ----------------- | ----------------------- |
| `id` | `id` |
| `locale` | `locale` |
| `fallbackLocale` | `fallbackLocale` |
| `availableLocales` | `availableLocales` |
| `messages` | `messages` |
| `datetimeFormats` | `datetimeFormats` |
| `numberFormats` | `numberFormats` |
| `modifiers` | `modifiers` |
| `formatter` | N/A |
| `missing` | `getMissingHandler` / `setMissingHandler` |
| `postTranslation` | `getPostTranslationHandler` / `setPostTranslationHandler`|
| `silentTranslationWarn` | `missingWarn` |
| `silentFallbackWarn` | `fallbackWarn` |
| `formatFallbackMessages` | `fallbackFormat` |
| `sync` | `inheritLocale` |
| `warnHtmlInMessage` | `warnHtmlMessage` |
| `escapeParameterHtml` | `escapeParameter` |
| `preserveDirectiveContent` | N/A |
| `t` | `t` |
| `tc` | `t` |
| `te` | N/A |
| `tm` | `tm` |
| `getLocaleMessage` | `getLocaleMessage` |
| `setLocaleMessage` | `setLocaleMessage`|
| `mergeLocaleMessage` | `mergeLocaleMessage` |
| `d` | `d` |
| `getDateTimeFormat` | `getDateTimeFormat` |
| `setDateTimeFormat` | `setDateTimeFormat` |
| `mergeDateTimeFormat` | `mergetDateTimeFormat` |
| `n` | `n` |
| `getNumberFormat` | `getNumberFormat` |
| `setNumberFormat` | `setNumberFormat` |
| `mergeNumberFormat` | `mergeNumberFormat` |
| `getChoiiceIndex` | N/A |