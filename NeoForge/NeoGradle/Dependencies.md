# Зависимости

Зависимости используются не только для разработки взаимодействия между модами или добавления дополнительных библиотек в игру, но также определяют, для какой версии Minecraft ведется разработка. Здесь представлен краткий обзор того, как изменять блоки `repositories` и `dependencies` для добавления зависимостей в вашу среду разработки.

> Это не углубленное объяснение концепций Gradle. Настоятельно рекомендуется прочитать [руководство по управлению зависимостями Gradle](https://docs.gradle.org/8.1.1/userguide/dependency_management.html) перед продолжением.

## Зависимости модов

Все зависимости модов добавляются так же, как и любые другие артефакты.

```gradle
dependencies {
    // Предположим, у нас есть артефакт 'examplemod', который можно получить из указанного репозитория
    implementation 'com.example:examplemod:1.0'
}
```

### Локальные зависимости модов

Если мод, от которого вы хотите зависеть, недоступен в репозитории Maven (например, [Maven Central](https://central.sonatype.com/), [CurseMaven](https://cursemaven.com/), [Modrinth](https://docs.modrinth.com/docs/tutorials/maven/)), вы можете добавить зависимость мода, используя вместо этого [flat directory](https://docs.gradle.org/8.1.1/userguide/declaring_repositories.html#sub:flat_dir_resolver):

```gradle
repositories {
    // Добавляет папку 'libs' в каталоге проекта как flat directory
    flatDir {
        dir 'libs'
    }
}

dependencies {
    // ...

    // Для заданного <group>:<name>:<version>:<classifier (по умолчанию None)>
    //   с расширением <ext (по умолчанию jar)>
    // Артефакты в flat directories будут разрешаться в следующем порядке:
    // - <name>-<version>.<ext>
    // - <name>-<version>-<classifier>.<ext>
    // - <name>.<ext>
    // - <name>-<classifier>.<ext>

    // Если классификатор указан явно,
    //  артефакты с этим классификатором будут иметь приоритет:
    // - examplemod-1.0-api.jar
    // - examplemod-api.jar
    // - examplemod-1.0.jar
    // - examplemod.jar
    implementation 'com.example:examplemod:1.0:api'
}
```

> **Примечание:**
> Имя группы может быть любым, но не должно быть пустым для записей flat directory, так как оно не проверяется при разрешении файла артефакта.
---
<div align="center"><table><tr><td align="center">Предыдущий раздел<br><a href="./NeoGradle Configurations/Run Configurations.md">Конфигурации запуска</a></td><td align="center">Следующий раздел<br><a href="./Dependencies/Non-Minecraft dependencies.md">Зависимости, не относящиеся к Minecraft</a></td></tr></table></div>
