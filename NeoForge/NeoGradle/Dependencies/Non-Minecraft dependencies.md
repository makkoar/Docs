# Зависимости, не относящиеся к Minecraft

Зависимости, не относящиеся к Minecraft, по умолчанию не загружаются NeoForge в среде разработки. Чтобы NeoForge распознал такие зависимости, их необходимо добавить в конфигурации запуска (runs) как зависимость времени выполнения (runtime dependency).

Например, вы можете добавить библиотеку `com.example:example` ко всем конфигурациям запуска следующим образом:

```gradle
dependencies {
    implementation 'com.example:example:1.0'
}

runs {
    configureEach {
        dependencies {
            runtime 'com.example:example:1.0'
        }
    }
}
```

Или вы можете использовать конфигурацию:

```gradle
configurations {
    libraries
    // Это гарантирует, что все зависимости, добавленные в конфигурацию libraries, также будут добавлены в конфигурацию implementation.
    // Таким образом, вам понадобится только одно объявление зависимости как для времени выполнения, так и для времени компиляции.
    implementation.extendsFrom libraries
}

dependencies {
    libraries 'com.example:example:1.0'
}

runs {
    configureEach {
        dependencies {
            runtime project.configurations.libraries
        }
    }
}
```

> **Совет:**
> `configureEach` настроит каждую конфигурацию запуска. Если вы хотите добавить зависимость времени выполнения только к одной конкретной конфигурации запуска, вы можете использовать `named(<имя>).configure`:
>
> ```gradle
> runs {
>     // Настроить зависимости только для конфигурации запуска 'client'
>     named('client').configure {
>         dependencies {
>             // Добавьте зависимости здесь
>         }
>     }
> }
> ```
---
<div align="center"><table><tr><td align="center">Предыдущий раздел<br><a href="../Dependencies.md">Зависимости</a></td><td align="center">Следующий раздел<br><a href="./Jar-in-Jar.md">Jar-in-Jar</a></td></tr></table></div>
