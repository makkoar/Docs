# Реестры

Регистрация — это процесс добавления объектов мода (таких как [предметы](../../items/Items.md), [блоки](../Blocks.md), сущности и т. д.) в игру, чтобы она о них знала. Регистрация важна, так как без нее игра просто не будет знать об этих объектах, что приведет к необъяснимому поведению и сбоям.

Реестр (registry) — это, по сути, обертка над картой (map), которая сопоставляет имена реестра (registry names, см. далее) с зарегистрированными объектами, часто называемыми записями реестра (registry entries). Имена реестра должны быть уникальными в пределах одного реестра, но одно и то же имя может присутствовать в нескольких реестрах. Самый распространенный пример — блоки (в реестре `BLOCKS`), которые имеют форму предмета с тем же именем реестра (в реестре `ITEMS`).

Каждый зарегистрированный объект имеет уникальное имя, называемое именем реестра. Имя представлено как [`ResourceLocation`](../../misc/resourcelocation.md). Например, имя реестра блока земли — `minecraft:dirt`, а имя реестра зомби — `minecraft:zombie`. Объекты модов, конечно, не будут использовать пространство имен `minecraft`; вместо этого будет использоваться их идентификатор мода (mod id).

## Vanilla vs Моды

Чтобы понять некоторые проектные решения, принятые в системе реестров NeoForge, сначала рассмотрим, как это делает Minecraft. В качестве примера будем использовать реестр блоков, так как большинство других реестров работают аналогично.

Реестры обычно регистрируют [синглтоны](https://ru.wikipedia.org/wiki/%D0%9E%D0%B4%D0%B8%D0%BD%D0%BE%D1%87%D0%BA%D0%B0_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)). Это означает, что все записи реестра существуют ровно один раз. Например, все блоки камня, которые вы видите в игре, на самом деле являются одним и тем же блоком камня, отображаемым много раз. Если вам нужен блок камня, вы можете получить его, ссылаясь на зарегистрированный экземпляр блока.

Minecraft регистрирует все блоки в классе `Blocks`. Через метод `register` вызывается `Registry#register()`, где первым параметром является реестр блоков `BuiltInRegistries.BLOCK`. После регистрации всех блоков Minecraft выполняет различные проверки на основе списка блоков, например, самопроверку, которая удостоверяется, что для всех блоков загружена модель.

Основная причина, по которой все это работает, заключается в том, что класс `Blocks` загружается Minecraft достаточно рано. Моды не загружаются автоматически Minecraft, поэтому необходимы обходные пути.

## Способы регистрации

NeoForge предлагает два способа регистрации объектов: класс `DeferredRegister` и событие `RegisterEvent`. Обратите внимание, что первый является оберткой над вторым и рекомендуется для предотвращения ошибок.

### `DeferredRegister`

Начнем с создания нашего `DeferredRegister`:

```java
public static final DeferredRegister<Block> BLOCKS = DeferredRegister.create(
        // Реестр, который мы хотим использовать.
        // Реестры Minecraft находятся в BuiltInRegistries, реестры NeoForge - в NeoForgeRegistries.
        // Моды также могут добавлять свои собственные реестры, обратитесь к документации или исходному коду конкретного мода, чтобы узнать, где их найти.
        BuiltInRegistries.BLOCKS,
        // Наш ID мода.
        ExampleMod.MOD_ID
);
```

Затем мы можем добавить наши записи реестра как статические финальные поля, используя один из следующих методов (см. [статью о блоках](../Blocks.md) для информации о параметрах в `new Block()`):

```java
public static final DeferredHolder<Block, Block> EXAMPLE_BLOCK_1 = BLOCKS.register(
        // Наше имя реестра.
        "example_block",
        // Поставщик (supplier) объекта, который мы хотим зарегистрировать.
        () -> new Block(...)
);

public static final DeferredHolder<Block, SlabBlock> EXAMPLE_BLOCK_2 = BLOCKS.register(
        // Наше имя реестра.
        "example_block",
        // Функция, создающая объект, который мы хотим зарегистрировать,
        // получая его имя реестра как ResourceLocation.
        registryName -> new SlabBlock(...)
);
```

Класс `DeferredHolder<R, T extends R>` содержит наш объект. Параметр типа `R` — это тип реестра, в который мы регистрируем (в нашем случае `Block`). Параметр типа `T` — это тип нашего поставщика. Поскольку в первом примере мы напрямую регистрируем `Block`, мы предоставляем `Block` в качестве второго параметра. Если бы мы регистрировали объект подкласса `Block`, например `SlabBlock` (как показано во втором примере), мы бы предоставили `SlabBlock` здесь.

`DeferredHolder<R, T extends R>` является подклассом `Supplier<T>`. Чтобы получить наш зарегистрированный объект, когда он нам нужен, мы можем вызвать `DeferredHolder#get()`. Тот факт, что `DeferredHolder` расширяет `Supplier`, также позволяет нам использовать `Supplier` в качестве типа нашего поля. Таким образом, приведенный выше блок кода становится следующим:

```java
public static final Supplier<Block> EXAMPLE_BLOCK_1 = BLOCKS.register(
        // Наше имя реестра.
        "example_block",
        // Поставщик объекта, который мы хотим зарегистрировать.
        () -> new Block(...)
);

public static final Supplier<SlabBlock> EXAMPLE_BLOCK_2 = BLOCKS.register(
        // Наше имя реестра.
        "example_block",
        // Функция, создающая объект, который мы хотим зарегистрировать,
        // получая его имя реестра как ResourceLocation.
        registryName -> new SlabBlock(...)
);
```

> **Примечание:**
> Имейте в виду, что в некоторых местах явно требуется `Holder` или `DeferredHolder`, и они не примут просто любой `Supplier`. Если вам нужен один из этих двух, лучше всего изменить тип вашего `Supplier` обратно на `Holder` или `DeferredHolder` по мере необходимости.

Наконец, поскольку вся система является оберткой над событиями реестра, нам нужно указать `DeferredRegister` прикрепиться к событиям реестра по мере необходимости:

```java
// Это конструктор нашего мода
public ExampleMod(IEventBus modBus) {
    ExampleBlocksClass.BLOCKS.register(modBus);
    // Другие вещи здесь
}
```

> **Информация:**
> Существуют специализированные варианты `DeferredRegister` для блоков, предметов и компонентов данных, которые предоставляют вспомогательные методы: [`DeferredRegister.Blocks`](../Blocks.md#deferredregisterblocks-helpers), [`DeferredRegister.Items`](../../items/Items.md#deferredregisteritems) и [`DeferredRegister.DataComponents`](../../items/datacomponents.md#creating-custom-data-components) соответственно.

### `RegisterEvent`

`RegisterEvent` — это второй способ регистрации объектов. Это [событие](./Events.md) срабатывает для каждого реестра после конструкторов модов (поскольку именно там `DeferredRegister` регистрируют свои внутренние обработчики событий) и перед загрузкой конфигураций. `RegisterEvent` срабатывает на шине событий мода.

```java
@SubscribeEvent
public void register(RegisterEvent event) {
    event.register(
            // Это ключ реестра.
            // Получите их из BuiltInRegistries для ванильных реестров,
            // или из NeoForgeRegistries.Keys для реестров NeoForge.
            BuiltInRegistries.BLOCKS,
            // Регистрируйте ваши объекты здесь.
            registry -> {
                registry.register(ResourceLocation.fromNamespaceAndPath(MODID, "example_block_1"), new Block(...));
                registry.register(ResourceLocation.fromNamespaceAndPath(MODID, "example_block_2"), new Block(...));
                registry.register(ResourceLocation.fromNamespaceAndPath(MODID, "example_block_3"), new Block(...));
            }
    );
}
```

## Запрос реестров

Иногда вы окажетесь в ситуации, когда захотите получить зарегистрированный объект по заданному идентификатору. Или захотите получить идентификатор определенного зарегистрированного объекта. Поскольку реестры — это, по сути, карты идентификаторов (`ResourceLocation`) к уникальным объектам, т. е. обратимая карта, обе эти операции работают:

```java
BuiltInRegistries.BLOCKS.getValue(ResourceLocation.fromNamespaceAndPath("minecraft", "dirt")); // возвращает блок земли
BuiltInRegistries.BLOCKS.getKey(Blocks.DIRT); // возвращает ResourceLocation "minecraft:dirt"

// Предположим, что ExampleBlocksClass.EXAMPLE_BLOCK.get() - это Supplier<Block> с ID "yourmodid:example_block"
BuiltInRegistries.BLOCKS.getValue(ResourceLocation.fromNamespaceAndPath("yourmodid", "example_block")); // возвращает example block
BuiltInRegistries.BLOCKS.getKey(ExampleBlocksClass.EXAMPLE_BLOCK.get()); // возвращает ResourceLocation "yourmodid:example_block"
```

Если вы просто хотите проверить наличие объекта, это также возможно, но только с ключами:

```java
BuiltInRegistries.BLOCKS.containsKey(ResourceLocation.fromNamespaceAndPath("minecraft", "dirt")); // true
BuiltInRegistries.BLOCKS.containsKey(ResourceLocation.fromNamespaceAndPath("create", "brass_ingot")); // true только если установлен Create
```

Как показывает последний пример, это возможно с любым идентификатором мода и, следовательно, является идеальным способом проверить, существует ли определенный предмет из другого мода.

Наконец, мы также можем перебирать все записи в реестре, либо по ключам, либо по записям (записи используют тип Java `Map.Entry`):

```java
for (ResourceLocation id : BuiltInRegistries.BLOCKS.keySet()) {
    // ...
}
for (Map.Entry<ResourceKey<Block>, Block> entry : BuiltInRegistries.BLOCKS.entrySet()) {
    // ...
}
```

> **Примечание:**
> Операции запроса всегда используют ванильные `Registry`, а не `DeferredRegister`. Это связано с тем, что `DeferredRegister` являются всего лишь утилитами для регистрации.

> **Опасно:**
> Операции запроса безопасны для использования только **после** завершения регистрации. **НЕ ЗАПРАШИВАЙТЕ РЕЕСТРЫ, ПОКА РЕГИСТРАЦИЯ ЕЩЕ ПРОДОЛЖАЕТСЯ!**

## Пользовательские реестры

Пользовательские реестры позволяют вам определять дополнительные системы, к которым могут подключаться аддоны для вашего мода. Например, если ваш мод добавляет заклинания, вы можете сделать заклинания реестром и тем самым позволить другим модам добавлять заклинания в ваш мод, без необходимости делать что-либо еще с вашей стороны. Это также позволяет автоматически выполнять некоторые действия, такие как синхронизация записей.

Начнем с создания [ключа реестра](../../misc/resourcelocation.md#resourcekeys) и самого реестра:

```java
// Мы используем заклинания (spells) в качестве примера для реестра, без подробностей о том, что такое заклинание на самом деле (так как это не имеет значения).
// Конечно, все упоминания заклинаний могут и должны быть заменены тем, чем на самом деле является ваш реестр.
public static final ResourceKey<Registry<Spell>> SPELL_REGISTRY_KEY = ResourceKey.createRegistryKey(ResourceLocation.fromNamespaceAndPath("yourmodid", "spells"));
public static final Registry<YourRegistryContents> SPELL_REGISTRY = new RegistryBuilder<>(SPELL_REGISTRY_KEY)
        // Если вы хотите включить синхронизацию целочисленных ID для сети.
        // Их следует использовать только в сетевых контекстах, например, в пакетах или в данных NBT, связанных исключительно с сетью.
        .sync(true)
        // Ключ по умолчанию. Аналогично minecraft:air для блоков. Это необязательно.
        .defaultKey(ResourceLocation.fromNamespaceAndPath("yourmodid", "empty"))
        // Эффективно ограничивает максимальное количество. Обычно не рекомендуется, но может иметь смысл в таких настройках, как сеть.
        .maxId(256)
        // Создать реестр.
        .create();
```

Затем сообщите игре, что реестр существует, зарегистрировав его в корневом реестре в `NewRegistryEvent`:

```java
@SubscribeEvent
static void registerRegistries(NewRegistryEvent event) {
    event.register(SPELL_REGISTRY);
}
```

Теперь вы можете регистрировать новое содержимое реестра, как и с любым другим реестром, как через `DeferredRegister`, так и через `RegisterEvent`:

```java
public static final DeferredRegister<Spell> SPELLS = DeferredRegister.create(SPELL_REGISTRY, "yourmodid");
public static final Supplier<Spell> EXAMPLE_SPELL = SPELLS.register("example_spell", () -> new Spell(...));

// Альтернативно:
@SubscribeEvent
public static void register(RegisterEvent event) {
    event.register(SPELL_REGISTRY_KEY, registry -> {
        registry.register(ResourceLocation.fromNamespaceAndPath("yourmodid", "example_spell"), () -> new Spell(...));
    });
}
```

## Реестры пакетов данных (Datapack Registries)

Реестр пакетов данных (также известный как динамический реестр или, по основному варианту использования, реестр генерации мира) — это особый тип реестра, который загружает данные из JSON-файлов [пакетов данных](../../resources/index.md#data) (отсюда и название) при загрузке мира, вместо того чтобы загружать их при запуске игры. Реестры пакетов данных по умолчанию включают, в частности, большинство реестров генерации мира, а также несколько других.

Реестры пакетов данных позволяют указывать их содержимое в JSON-файлах. Это означает, что код (кроме [генерации данных](#генерация-данных-для-реестров-пакетов-данных), если вы не хотите писать JSON-файлы самостоятельно) не требуется. Каждый реестр пакетов данных имеет связанный с ним [`Codec`](../../datastorage/codecs.md), который используется для сериализации, и идентификатор каждого реестра определяет его путь в пакете данных:

*   Реестры пакетов данных Minecraft используют формат `data/yourmodid/registrypath` (например, `data/yourmodid/worldgen/biome`, где `worldgen/biome` — это путь реестра).
*   Все остальные реестры пакетов данных (NeoForge или модов) используют формат `data/yourmodid/registrynamespace/registrypath` (например, `data/yourmodid/neoforge/biome_modifier`, где `neoforge` — это пространство имен реестра, а `biome_modifier` — путь реестра).

Реестры пакетов данных можно получить из `RegistryAccess`. Этот `RegistryAccess` можно получить, вызвав `ServerLevel#registryAccess()`, если на сервере, или `Minecraft.getInstance().getConnection()#registryAccess()`, если на клиенте (последнее работает только если вы действительно подключены к миру, так как в противном случае соединение будет null). Результат этих вызовов затем можно использовать как любой другой реестр для получения конкретных элементов или для итерации по содержимому.

### Пользовательские реестры пакетов данных

Пользовательские реестры пакетов данных не требуют создания `Registry`. Вместо этого им нужен только ключ реестра и хотя бы один [`Codec`](../../datastorage/codecs.md) для (де)сериализации его содержимого. Возвращаясь к примеру с заклинаниями, регистрация нашего реестра заклинаний как реестра пакетов данных выглядит примерно так:

```java
public static final ResourceKey<Registry<Spell>> SPELL_REGISTRY_KEY = ResourceKey.createRegistryKey(ResourceLocation.fromNamespaceAndPath("yourmodid", "spells"));

@SubscribeEvent
public static void registerDatapackRegistries(DataPackRegistryEvent.NewRegistry event) {
    event.dataPackRegistry(
            // Ключ реестра.
            SPELL_REGISTRY_KEY,
            // Кодек содержимого реестра.
            Spell.CODEC,
            // Сетевой кодек содержимого реестра. Часто идентичен обычному кодеку.
            // Может быть сокращенным вариантом обычного кодека, который опускает данные, не нужные на клиенте.
            // Может быть null. Если null, записи реестра вообще не будут синхронизироваться с клиентом.
            // Может быть опущен, что функционально идентично передаче null (вызывается перегрузка метода
            // с двумя параметрами, которая передает null обычному методу с тремя параметрами).
            Spell.CODEC,
            // Потребитель (consumer), который настраивает созданный реестр через RegistryBuilder.
            // Может быть опущен, что функционально идентично передаче builder -> {}.
            builder -> builder.maxId(256)
    );
}
```

### Генерация данных для реестров пакетов данных

Поскольку написание всех JSON-файлов вручную утомительно и чревато ошибками, NeoForge предоставляет [поставщика данных](../../resources/index.md#data-generation) для генерации JSON-файлов за вас. Это работает как для встроенных, так и для ваших собственных реестров пакетов данных.

Сначала мы создаем `RegistrySetBuilder` и добавляем в него наши записи (один `RegistrySetBuilder` может содержать записи для нескольких реестров):

```java
new RegistrySetBuilder()
    .add(Registries.CONFIGURED_FEATURE, bootstrap -> {
        // Зарегистрируйте настроенные функции через контекст bootstrap (см. ниже)
    })
    .add(Registries.PLACED_FEATURE, bootstrap -> {
        // Зарегистрируйте размещенные функции через контекст bootstrap (см. ниже)
    });
```

Параметр лямбды `bootstrap` — это то, что мы фактически используем для регистрации наших объектов. Он имеет тип `BootstrapContext`. Чтобы зарегистрировать объект, мы вызываем на нем `#register`, вот так:

```java
// Ключ ресурса нашего объекта.
public static final ResourceKey<ConfiguredFeature<?, ?>> EXAMPLE_CONFIGURED_FEATURE = ResourceKey.create(
    Registries.CONFIGURED_FEATURE,
    ResourceLocation.fromNamespaceAndPath(MOD_ID, "example_configured_feature")
);

new RegistrySetBuilder()
    .add(Registries.CONFIGURED_FEATURE, bootstrap -> {
        bootstrap.register(
            // Ключ ресурса нашей настроенной функции.
            EXAMPLE_CONFIGURED_FEATURE,
            // Фактическая настроенная функция.
            new ConfiguredFeature<>(Feature.ORE, new OreConfiguration(...))
        );
    })
    .add(Registries.PLACED_FEATURE, bootstrap -> {
        // ...
    });
```

`BootstrapContext` также можно использовать для поиска записей из другого реестра, если это необходимо:

```java
public static final ResourceKey<ConfiguredFeature<?, ?>> EXAMPLE_CONFIGURED_FEATURE = ResourceKey.create(
    Registries.CONFIGURED_FEATURE,
    ResourceLocation.fromNamespaceAndPath(MOD_ID, "example_configured_feature")
);
public static final ResourceKey<PlacedFeature> EXAMPLE_PLACED_FEATURE = ResourceKey.create(
    Registries.PLACED_FEATURE,
    ResourceLocation.fromNamespaceAndPath(MOD_ID, "example_placed_feature")
);

new RegistrySetBuilder()
    .add(Registries.CONFIGURED_FEATURE, bootstrap -> {
        bootstrap.register(EXAMPLE_CONFIGURED_FEATURE, ...);
    })
    .add(Registries.PLACED_FEATURE, bootstrap -> {
        HolderGetter<ConfiguredFeature<?, ?>> otherRegistry = bootstrap.lookup(Registries.CONFIGURED_FEATURE);
        bootstrap.register(EXAMPLE_PLACED_FEATURE, new PlacedFeature(
            otherRegistry.getOrThrow(EXAMPLE_CONFIGURED_FEATURE), // Получить настроенную функцию
            List.of() // Пустое действие при размещении - замените на ваши параметры размещения
        ));
    });
```

Наконец, мы используем наш `RegistrySetBuilder` в фактическом поставщике данных и регистрируем этого поставщика данных в событии:

```java
@SubscribeEvent
public static void onGatherData(GatherDataEvent.Client event) {
    // Добавляет сгенерированные объекты реестра к текущему провайдеру поиска для использования
    // в другой генерации данных.
    this.createDatapackRegistryObjects(
        // Наш построитель набора реестров для генерации данных.
        new RegistrySetBuilder().add(...),
        // (Необязательно) Би-потребитель (biconsumer), который принимает любые условия для загрузки объекта,
        // связанного с ключом ресурса
        conditions -> {
            conditions.accept(resourceKey, condition);
        },
        // (Необязательно) Набор идентификаторов модов, для которых мы генерируем записи
        // По умолчанию предоставляет идентификатор мода текущего контейнера мода.
        Set.of("yourmodid")
    );

    // Вы можете использовать провайдер поиска с вашими сгенерированными записями, вызвав один
    // из методов `#create*` или получив фактический поиск через `#getLookupProvider`
    // ...
}
```

---
<div align="center"><table><tr><td align="center">Предыдущий раздел<br><a href="../Getting%20Started/Versioning.md">Версионирование</a></td><td align="center">Следующий раздел<br><a href="./Sides.md">Стороны</a></td></tr></table></div>
