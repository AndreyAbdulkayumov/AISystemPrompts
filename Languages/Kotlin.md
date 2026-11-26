# Стиль написания кода на Kotlin (Android) с Jetpack Compose

Ассистент ДОЛЖЕН следовать официальному Kotlin Style Guide при написании любого кода на Kotlin.

## SOLID

- Следовать принципам SOLID
- Single Responsibility Principle:
  класс должен иметь одну ответственность
- Open/Closed Principle:
  расширение предпочтительнее изменения
- Liskov Substitution Principle:
  реализации интерфейсов должны быть взаимозаменяемы
- Interface Segregation Principle:
  избегать "толстых" интерфейсов
- Dependency Inversion Principle:
  зависимости передавать через абстракции,
  а не создавать внутри класса
- Предпочитать композицию наследованию
- Избегать overly abstract architecture
- Не создавать интерфейсы без необходимости

## Именование

- Классы, объекты, Composable-функции — PascalCase:
  `UserRepository`, `AuthViewModel`, `UserCard`
- Composable-функции именуются существительным, а не глаголом:
  `UserCard` вместо `ShowUserCard`
- Функции, переменные, параметры — camelCase:
  `fetchUser()`, `isLoading`
- Константы — SCREAMING_SNAKE_CASE:
  `MAX_RETRY_COUNT`
- Пакеты — только строчные буквы без подчёркиваний:
  `com.example.feature.auth`

## Форматирование

- Отступ — 4 пробела, не табуляция
- Максимальная длина строки — 100 символов
- Открывающая фигурная скобка — на той же строке, что и объявление
- Пустая строка между методами и логическими блоками кода

## Kotlin-идиомы

- Предпочитать `val` вместо `var` везде, где значение не меняется
- Использовать `data class` для моделей данных
- Использовать `sealed class` или `sealed interface`
  для представления состояний и результатов
- Использовать `object` для синглтонов вместо статических членов
- Предпочитать extension functions вместо утилитарных классов
- Использовать `when` вместо цепочки `if-else if`
- Избегать `!!`
- Использовать immutable-модели и коллекции по умолчанию
- Изменение состояния выполнять через `copy()`

## Null-безопасность

- Nullable-типы допускаются только там,
  где `null` является осмысленным значением
- Оператор `!!` ЗАПРЕЩЁН
- Для значений по умолчанию использовать `?:`
- Использовать ранний выход через:
  `?: return`
  `?: throw`

## Корутины и Flow

- Избегать `GlobalScope`
- Использовать `viewModelScope` или `lifecycleScope`
- Не переключать dispatcher вручную без необходимости
- Тяжёлые и IO-операции выполнять на `Dispatchers.IO`
- Использовать `rememberCoroutineScope`
  для запуска корутин вне composable-контекста
- Ошибки корутин обрабатывать через:
  `try/catch`
  `CoroutineExceptionHandler`

Flow:
- Для состояния экрана использовать `StateFlow`
- Для одноразовых событий использовать:
  `SharedFlow` или `Channel`
- `StateFlow` создавать через:
  private `MutableStateFlow`
  public `asStateFlow()`
- Использовать `collectLatest`
  для отменяемых операций
- Использовать `combine`
  вместо ручной синхронизации потоков
- Не собирать Flow напрямую в `init`
  без необходимости

## Архитектура MVVM с Jetpack Compose

### Слои и ответственность

- Composable-функции отвечают только
  за отображение `UiState`
  и передачу событий в ViewModel
- ViewModel отвечает за состояние экрана
  и взаимодействие с UseCase
- ViewModel НЕ ДОЛЖНА содержать ссылки на:
  `Composable`
  `Context`
  `Activity`
  `Fragment`
  `View`
- UseCase инкапсулирует завершённый
  сценарий бизнес-логики
- Repository отвечает за доступ к данным
  и скрывает источник данных
- Repository НЕ ДОЛЖЕН содержать UI-логику
- Model — `data class`,
  описывающий структуру данных
- Model может содержать
  простые вычисляемые свойства,
  но не бизнес-операции
- Зависимости между слоями:
  `Composable → ViewModel → UseCase → Repository`

### Запрещено

- business logic внутри Composable
- direct repository access из UI
- mutable public state
- singleton object для хранения UI state
- context leakage
- LiveData
- `findViewById`
- Synthetic View Binding
- `AsyncTask`
- RxJava,
  если проект основан на coroutines
- XML UI для новых экранов вместо Compose

## Структура проекта (feature-based)

com.example.app
├── feature/
│   ├── auth/
│   │   ├── AuthScreen
│   │   ├── AuthViewModel
│   │   ├── AuthUiState
│   │   ├── AuthUseCase
│   │   ├── AuthRepository
│   │   └── User
│   └── profile/
│       ├── ProfileScreen
│       ├── ProfileViewModel
│       ├── ProfileUiState
│       ├── ProfileUseCase
│       ├── ProfileRepository
│       └── Profile
└── core/
    ├── network/
    ├── database/
    ├── ui/
    └── util/

## Состояние экрана

- `UiState` ДОЛЖЕН быть immutable
- `UiState` описывается через `data class`
- Статусы состояния представляются через:
  `sealed class`
  `sealed interface`
- Состояние передаётся через `StateFlow`
- В Composable использовать:
  `collectAsStateWithLifecycle()`
- Ошибки отображаются через `UiState`
- Одноразовые события
  (`navigation`, `snackbar`)
  передавать через:
  `SharedFlow`
  `Channel`

## Jetpack Compose

### UI

- Использовать Material 3
- Не использовать Material 2
- Цвета, typography и shapes
  получать через `MaterialTheme`
- Базовая структура экрана —
  через `Scaffold`

### Структура Composable-функций

- Разделять stateful/stateless composable
- Stateless composable предпочтительнее
- `Modifier` ДОЛЖЕН быть
  первым необязательным параметром:

  `fun UserCard(
      user: User,
      modifier: Modifier = Modifier
  )`

- Каждая composable-функция
  ДОЛЖНА иметь `@Preview`
- Для сложных экранов создавать
  preview-компоненты с fake data
- Крупные composable-функции
  разбивать на мелкие компоненты

### Состояние и рекомпозиция

- Использовать state hoisting
- Использовать `remember`
  для состояния между рекомпозициями
- Использовать `rememberSaveable`
  для сохранения состояния
- Использовать `derivedStateOf`
  для вычисляемых значений
- `CompositionLocal`
  использовать только
  для сквозных зависимостей
- Избегать создания объектов
  внутри composable без `remember`
- Не выполнять тяжёлые вычисления
  внутри composable
- Для списков использовать:
  `LazyColumn`
  `LazyRow`
- Предпочитать stable/immutable модели

### Побочные эффекты

- `LaunchedEffect` —
  для запуска корутин
- Ключ `LaunchedEffect`
  ДОЛЖЕН точно отражать зависимость
- `SideEffect` —
  для синхронизации с внешним кодом
- `DisposableEffect` —
  для ресурсов,
  требующих очистки

## Навигация

- Использовать Navigation Compose
- Не передавать `NavController`
  глубоко в дерево composable
- Навигационные события
  инициируются из ViewModel
- Аргументы экранов
  должны быть типизированы

## Accessibility

- Иконки и изображения
  ДОЛЖНЫ иметь `contentDescription`
- Декоративные элементы
  ДОЛЖНЫ иметь:
  `contentDescription = null`

## Dependency Injection (Hilt)

- Использовать Hilt
  как основной DI-фреймворк
- Каждый класс получает зависимости
  через конструктор
- ViewModel ДОЛЖНА использовать:
  `@HiltViewModel`
- Зависимости передаются через:
  `@Inject constructor`
- Repository и UseCase
  предпочтительно создавать через:
  `@Inject constructor`
- Hilt-модули использовать только
  для зависимостей,
  которые нельзя внедрить напрямую
- Использовать минимально необходимую
  область видимости:
  `@ViewModelScoped`
  `@ActivityRetainedScoped`
  `@Singleton`
  только для глобальных объектов

## Обработка ошибок

- Repository НЕ ДОЛЖЕН выбрасывать
  необработанные исключения наружу
- Использовать:
  `Result`
  `sealed class`
  или аналогичный тип результата
- Ошибки должны быть обработаны
  и преобразованы в UI state

## Тестирование

- ViewModel тестируется изолированно
- UseCase тестируется
  без Android framework
- Composable тестируется через:
  `compose-ui-test`
- Зависимости подменяются через:
  fake/mock реализации
- Тесты должны быть детерминированными
  и не зависеть от времени или сети