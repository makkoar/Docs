# Конфигурации NeoGradle

NeoGradle имеет множество конфигураций, которые могут изменять способ настройки среды разработки.

## Включение Access Transformers

<a href="../NeoForge/Advanced Topics/Access Transformers.md">Access Transformers</a> (трансформеры доступа) могут расширять видимость или изменять флаг `final` классов, методов и полей Minecraft.

Чтобы включить Access Transformers в производственной среде (production), вы можете указать конфигурационный файл(ы) в блоке `accessTransformers`:

```gradle
minecraft {
    // ...

    // Добавьте файл Access Transformer относительно директории проекта
    accessTransformers {
        file('src/main/resources/META-INF/accesstransformer.cfg')

        // Можно указать несколько файлов, они применяются по порядку
        file('src/main/resources/accesstransformer_extras.cfg')
    }
}
```

В производственной среде NeoForge будет искать файлы Access Transformer, указанные в `mods.toml`, или по пути `META-INF/accesstransformer.cfg`, если ничего не указано:

```toml
[[accessTransformers]]
file="META-INF/accesstransformer.cfg"

[[accessTransformers]]
file="accesstransformer_extras.cfg"
```
---
<div align="center"><table><tr><td align="center">Предыдущий раздел<br><a href="../NeoGradle Documentation.md">Документация NeoGradle</a></td><td align="center">Следующий раздел<br><a href="./Run Configurations.md">Конфигурации запуска</a></td></tr></table></div>
