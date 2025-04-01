# Документация NeoGradle

Это переведённая официальная документация по `NeoGradle`, плагину для `Gradle`, предназначенному для разработки `NeoForge` и модов с использованием `NeoForge`.

Эта документация посвящена только `NeoForge` и не является учебным пособием по `Java`, `Groovy`/`Kotlin` или `Gradle`.

## Добавление плагина

`NeoGradle` можно добавить с помощью блока `plugins` в файле `build.gradle`, предварительно добавив репозиторий NeoForged maven в список доступных репозиториев плагинов в файле `settings.gradle`:

```groovy
// В settings.gradle
pluginManagement {
    repositories {
        // ...
        // Добавьте репозиторий NeoForged maven
        maven { url = 'https://maven.neoforged.net/releases' }
    }
}
```

```groovy
// В build.gradle
plugins {
    // Добавьте плагин NeoGradle userdev
    id 'net.neoforged.gradle.userdev' version '7.0.120'
    // ...
}
```

> **Примечание**
> Хотя вы можете использовать диапазоны версий для плагина NeoGradle, делать это не рекомендуется, так как это может привести к более частым раундам декомпиляции и перекомпиляции, а также к возможным изменениям в поведении. Актуальную версию NeoGradle можно найти в нашем [Списке проектов](<!-- URL списка проектов NeoGradle -->).

## Добавление зависимости NeoForge

Чтобы получить декомпилированное окружение Minecraft и классы `NeoForge` в вашей среде разработки, вам просто нужно добавить зависимость `net.neoforged:neoforge` в конфигурацию, используемую как для времени выполнения, так и для времени компиляции (обычно это `implementation`):

```groovy
dependencies {
    implementation 'net.neoforged:neoforge:20.6.43-beta'
}
```

> **Примечание**
> MDK NeoForge устанавливает версию NeoForge через файл `gradle.properties`. Актуальную версию NeoForge можно найти в нашем [Списке проектов](<!-- URL списка проектов NeoForge -->).