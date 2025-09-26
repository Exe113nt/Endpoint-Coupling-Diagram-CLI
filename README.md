# Endpoint-Coupling-Diagram-CLI

ECD CLI — это утилита командной строки для генерации диаграмм связей эндпоинтов между микросервисами.

Инструмент позволяет наглядно представить ландшафт интеграций: показать, как именно сервисы взаимодействуют между собой, какие эндпоинты обращаются к внешним операциям и как складывается общая архитектурная картина.

![Диаграмма связей](https://github.com/Exe113nt/Endpoint-Coupling-Diagram-CLI/blob/3c39efe026de45347a0bdc7fabda58e2048806a8/diagram.png)

## 🔮 Когда особенно полезно
- В масштабных системах с десятками микросервисов, где единая схема связей теряет читаемость.
- Для аналитиков и архитекторов, которым важно быстро сориентироваться в интеграциях и зависимостях.
- При подготовке документации по API, чтобы избежать ручного рисования схем в Confluence или других редакторах.

## ✨ Возможности

📊 Генерация изображения схемы связей между сервисами посредством движка PlantUML.

⚙️ Конфигурируемая структура описания эндпоинтов — можно добавлять произвольные атрибуты в зависимости от специфики проекта.

🗺 Фокус на подмножестве сервисов — диаграмму можно строить не по всему проекту, а только по выбранному набору микросервисов.

🎨 Кастомные скины (через skins.puml) для стилизации методов, авторизации, статусов.

🚨 Подсветка устаревших эндпоинтов и использование атрибутов (auth, csrf, issuer) прямо на схеме.


## 📂 Пример описания сервиса

```yaml
#services/EntityService.yaml

service: EntityService          # имя сервиса на схеме
alias: ES                       # алиас для связей
color: "#E9FFE9"                # цвет узла на диаграмме

controllers:
  OrderService:
    endpoints:
      - method: POST
        path: /api/v1/entities
        operation: GetEntityList
        auth: required
        issuer: user-sso
        deprecated: false
        csrf: on
        links:
          - "AS#GetentityExtraData"   # ссылка на операцию другого сервиса
```
Атрибут links указывает на операции в других сервисах через АлиасСервиса#Операция.

Структура описания эндпоинта может быть расширена под нужды проекта.

## 🎨 Стилизация
Утилита поддерживает подключение кастомного файла с определениями и легендой. Это позволяет гибко управлять отображением атрибутов.
Пример skins.puml
```puml
!define AUTH_NONE 🔓
!define ISSUER_USER_SSO 👤
!define ISSUER_LDAP_SSO 👷
!define AUTH_REQUIRED 🛡
!define AUTH_OPTIONAL ⭕
!define DEPRECATED <color:red>DEPRECATED</color>

!define GET    <b><back:LightBlue><color:Black> GET    </color></back></b>
!define POST   <b><back:LightGreen><color:Black> POST   </color></back></b>
!define PATCH  <b><back:Orange><color:Black> PATCH  </color></back></b>
!define DELETE <b><back:Tomato><color:White> DELETE </color></back></b>

legend top left
  **LEGEND**
  AUTH_NONE: No authorization
  ISSUER_USER_SSO: User authorization
  ISSUER_LDAP_SSO: LDAP authorization
  AUTH_REQUIRED: Auth is required
  AUTH_OPTIONAL: Auth is optional
  ⚔️: CSRF On
  <s>Strikethrough</s>: Deprecated
endlegend

!define CSRF ⚔️
```

## ⚙️ Автоматизация

Управление YAML-описаниями сервисов может использоваться прямо в процессе разработки: при изменении эндпоинтов обновляется описание соответствующего сервиса.

В рамках CI/CD пайплайна можно настроить автоматическую перегенерацию диаграммы, чтобы архитектурная схема всегда синхронизировалась с кодовой базой.

Таким образом, описание сервисов поддерживается всегда актуальным и достоверным без дополнительных затрат времени и ручных действий.
