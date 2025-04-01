# ������������ NeoGradle

��� ����������� ����������� ������������ �� `NeoGradle`, ������� ��� `Gradle`, ���������������� ��� ���������� `NeoForge` � ����� � �������������� `NeoForge`.

��� ������������ ��������� ������ `NeoForge` � �� �������� ������� �������� �� `Java`, `Groovy`/`Kotlin` ��� `Gradle`.

## ���������� �������

`NeoGradle` ����� �������� � ������� ����� `plugins` � ����� `build.gradle`, �������������� ������� ����������� NeoForged maven � ������ ��������� ������������ �������� � ����� `settings.gradle`:

```groovy
// � settings.gradle
pluginManagement {
    repositories {
        // ...
        // �������� ����������� NeoForged maven
        maven { url = 'https://maven.neoforged.net/releases' }
    }
}
```

```groovy
// � build.gradle
plugins {
    // �������� ������ NeoGradle userdev
    id 'net.neoforged.gradle.userdev' version '7.0.120'
    // ...
}
```

> **����������**
> ���� �� ������ ������������ ��������� ������ ��� ������� NeoGradle, ������ ��� �� �������������, ��� ��� ��� ����� �������� � ����� ������ ������� ������������ � ��������������, � ����� � ��������� ���������� � ���������. ���������� ������ NeoGradle ����� ����� � ����� [������ ��������](<!-- URL ������ �������� NeoGradle -->).

## ���������� ����������� NeoForge

����� �������� ����������������� ��������� Minecraft � ������ `NeoForge` � ����� ����� ����������, ��� ������ ����� �������� ����������� `net.neoforged:neoforge` � ������������, ������������ ��� ��� ������� ����������, ��� � ��� ������� ���������� (������ ��� `implementation`):

```groovy
dependencies {
    implementation 'net.neoforged:neoforge:20.6.43-beta'
}
```

> **����������**
> MDK NeoForge ������������� ������ NeoForge ����� ���� `gradle.properties`. ���������� ������ NeoForge ����� ����� � ����� [������ ��������](<!-- URL ������ �������� NeoForge -->).