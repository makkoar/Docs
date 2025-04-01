# Parchment

[Parchment](https://parchmentmc.org/) — это созданный сообществом набор маппингов **имен параметров** и **javadoc**, который дополняет официальные имена, выпущенные Mojang. Используя Parchment, вы можете получить имена параметров для большинства методов Minecraft вместо неописательных имен `p_`.

## Настройка Parchment

Самая базовая конфигурация использует следующие свойства в файле `gradle.properties`:

```properties
# Версия Minecraft, для которой предназначена версия Parchment
neogradle.subsystems.parchment.minecraftVersion=1.20.2
# Версия маппингов Parchment
neogradle.subsystems.parchment.mappingsVersion=2023.12.10
```

Подсистема также имеет Gradle DSL и поддерживает больше параметров, что объясняется в следующем фрагменте Gradle:

```gradle
subsystems {
    parchment {
        // Версия Minecraft, для которой были созданы маппинги Parchment.
        // Необязательно должна совпадать с версией Minecraft, на которую нацелен ваш мод
        // По умолчанию используется значение свойства Gradle neogradle.subsystems.parchment.minecraftVersion
        minecraftVersion = "1.20.2"
        
        // Версия маппингов Parchment для применения.
        // Список см. на https://parchmentmc.org/docs/getting-started.
        // По умолчанию используется значение свойства Gradle neogradle.subsystems.parchment.mappingsVersion
        mappingsVersion = "2023.12.10"
        
        // Переопределяет полную Maven-координату артефакта Parchment для использования
        // По умолчанию вычисляется из свойств minecraftVersion и mappingsVersion.
        // Если вы установите это свойство явно, minecraftVersion и mappingsVersion будут проигнорированы.
        // Встроенное значение по умолчанию также можно переопределить с помощью свойства Gradle neogradle.subsystems.parchment.parchmentArtifact
        // parchmentArtifact = "org.parchmentmc.data:parchment-$minecraftVersion:$mappingsVersion:checked@zip"
        
        // Переопределяет полную Maven-координату инструмента, используемого для применения маппингов Parchment
        // См. https://github.com/neoforged/JavaSourceTransformer
        // Встроенное значение по умолчанию также можно переопределить с помощью свойства Gradle neogradle.subsystems.parchment.toolArtifact
        // toolArtifact = "net.neoforged.jst:jst-cli-bundle:1.0.30"
        
        // Установите значение false, если вы не хотите, чтобы репозиторий https://maven.parchmentmc.org/ добавлялся автоматически, когда
        // включено применение маппингов Parchment
        // Встроенное значение по умолчанию также можно переопределить с помощью свойства Gradle neogradle.subsystems.parchment.addRepository
        // addRepository = true
        
        // Может использоваться для явного отключения этой подсистемы. По умолчанию она будет включена автоматически, как только
        // будут установлены parchmentArtifact или minecraftVersion и mappingsVersion.
        // Встроенное значение по умолчанию также можно переопределить с помощью свойства Gradle neogradle.subsystems.parchment.enabled
        // enabled = true
    }
}

```

> **Совет:**
> Вы можете найти последние версии Parchment в их [документации](https://parchmentmc.org/docs/getting-started).

---
<div align="center"><table><tr><td align="center">Предыдущий раздел<br><a href="./Dependencies/Jar-in-Jar.md">Jar-in-Jar</a></td></tr></table></div>
