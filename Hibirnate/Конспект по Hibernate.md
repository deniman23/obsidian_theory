**Hibernate** - это реализация Объектной Реляционной Модели с исходным кодом.
# 1. Начинаем работать с ORM
## 1. Основы объектно-реляционного отображения
###  1.1 Что такое долговременное хранение?

Это процесс когда данные хранятся не только во время работы приложения, но и после его отключения и восстанавливаются при следующем запуске. 

**Реляционные базы данных**
Система управления Базами Данных - это комплекс программ, позволяющих создать базу данных и манипулировать данными. Главное в СУБД, что данные живут дольше приложения. 

### 1.2. Несоответствие парадигм


![[Pasted image 20240422164546.png]]

Как видно на диаграмме, у пользователя (класс User) может быть несколько платежных реквизитов (класс BillingDetails). Вы можете осуществлять навигацию по связи между классами в обоих направлениях.

Создаются соответствующие классы и SQL-запросы в которых есть создание зависимости двух сущностей.

![[Pasted image 20240422165507.png]]

![[Pasted image 20240422165534.png]]

#### 1.2.1 Проблема детализации

Далее мы усложним задачу добавив сущность Address, которая хранит адреса юзера.  

![[Pasted image 20240422165111.png]]

Мы не создаём новую таблицу ADDRESS и не добавляем ссылку в USER, а создаём новый тип ADDRESS

![[Pasted image 20240422165812.png]]

Теперь имеется выбор между добавлением нескольких столбцов или одного (нового типа данных SQL). Это, очевидно, является ==проблемой детализации== (problem of granularity). В широком смысле детализация относится к размерам типов, с которыми вы работаете.

Новый тип (класс) Address в Java и ADDRESS, новый тип данных SQL, должны обеспечить
взаимодействие. Но вы обнаружите множество проблем, если проверите поддержку типов, определяемых пользователем (User-defined Data Types, UDT), в современных системах управления базами данных SQL. **Поддержка UDT** – это одно из так называемых ==объектно-реляционных расширений== традиционного SQL.

По этой и многим другим причинам использование UDT или типов Java в базах данных SQL еще не стало общепринятой практикой в отрасли; маловероятно, что вы столкнетесь со старой схемой, в которой широко применяются UDT. Следовательно, вы не сможете, и не будете, хранить экземпляры нового класса Address в одном столбце, имеющем тот же тип данных, что и в Java.

Более практичное решение этой проблемы: создать несколько столбцов предопределенных типов (логического, числового, строкового), встроенных производителем. Согласно ему, таблицу USERS можно определить следующим образом:

![[Pasted image 20240422170555.png]]


#### 1.2.2 Проблема идентичности

К сожалению, такая реализация усложняет смену имени пользователя – требуется обновить не только запись в таблице USERS, но и все значения внешнего ключа во многих строках таблицы BILLINGDETAILS. Далее в этой книге для решения данной задачи мы предложим использовать суррогатный ключ, когда не удается найти хороший естественный ключ.

![[Pasted image 20240422173058.png]]
![[Pasted image 20240422173303.png]]
#### 1.2.3 Ассоциации

Ассоциации в ООП языках представляют через объектные ссылки. Ссылки должны быть двунаправленные, а значит должны быть указатели в двух классах. Это вид ==многие к одному==. 

![[Pasted image 20240422173900.png]]

Ассоциации в Java могут иметь вид ==многие ко многим==. Классы, например, могли
быть определены следующим образом:

![[Pasted image 20240422174351.png]]

Что бы сделать ассоциацию ==многие ко многим== в SQL нам надо создать новую таблицу для ссылок.

![[Pasted image 20240422174918.png]]


### 1.3 ORM и JPA

**Объектно-реляционное отображение** – это автоматическое (и прозрачное) сохранение объекта из Java-приложения в таблицах базы данных SQL с использованием метаданных, описывающих отображение между классами приложения и схемой базы данных SQL. Простыми словами это сохранение Java объектов из программы в таблицу SQL, со всеми зависимостями.

Преимущества Hibernate.
* **Продуктивность** – Hibernate берет большую часть рутинной работы на себя, позволяя сконцентрироваться на проблеме предметной области. Не важно, какую стратегию разработки приложения вы предпочитаете – сверху вниз, начиная от предметной модели, или снизу вверх, начиная с существующей схемы базы данных, – Hibernate вместе с подходящими инструментами значительно сократит время разработки.
* **Простота сопровождения** – автоматизация объектно-реляционного отображения с Hibernate способствует уменьшению количества строк кода, делая систему более понятной и удобной для рефакторинга. Hibernate образует прослойку между предметной моделью и схемой SQL, предохраняя каждую модель от влияния незначительных изменений в другой.
* **Производительность** – несмотря на то что механизм хранения, реализованный вручную, может работать быстрее, так же как ассемблерный код будет выполняться быстрее Java-кода, автоматизированные решения, подобные Hibernate, позволяют использовать множество оптимизаций, работающих всегда. Одним из примеров может служить эффективное и легко настраиваемое кэширование на уровне приложения. Это означает, что разработчик сможет потратить больше энергии на то, чтобы вручную оптимизировать несколько оставшихся проблемных мест, вместо того чтобы предварительно оптимизировать все сразу.
* **Независимость от поставщика** – Hibernate может помочь снизить некоторые риски, связанные с зависимостью от поставщика. Даже если вы не планируете менять используемую СУБД, инструменты ORM, поддерживающие несколько различных СУБД, предоставляют вам некоторый уровень переносимости. Кроме того, независимость от СУБД обеспечивает такой способ разработки, когда инженеры используют легковесную локальную базу данных, а для тестирования и эксплуатации разворачивают приложение в другой системе.

**Java Persistance API** - это спецификация которая служит мостом между ORM Java и реляционными базами данных, позволяя читать добавлять данные и т д. 
**JPA реализует концепцию ORM**, что позволяет отображать объекты Java на таблицы базы данных и наоборот, без необходимости писать сложный SQL-код.

**Hibernate реализует JPA и поддерживает все стандарты.**

В Java Persistence API (JPA) уровень хранения описывается как мост между объектными моделями (программа Java) и реляционными моделями (программа базы данных). JPA предоставляет набор классов и методов для постоянного хранения данных в базе данных. Вот основные принципы работы уровня хранения в JPA:

1. Объектно-реляционное отображение (ORM): JPA позволяет отображать объекты Java на таблицы реляционной базы данных и наоборот, что упрощает сохранение и извлечение данных.
2. EntityManager: В JPA для управления сущностями и их жизненным циклом используется класс EntityManager, который предоставляет API для создания, поиска, обновления и удаления сущностей.
3. JPQL и Criteria API: JPA включает Java Persistence Query Language (JPQL) и Criteria API для формирования запросов к базе данных, что позволяет разработчикам писать тип безопасные запросы, не зависящие от конкретной базы данных.
4. Кэширование: JPA поддерживает кэширование первого и второго уровней для улучшения производительности приложения за счет сокращения количества запросов к базе данных.
5. Транзакции: JPA управляет транзакциями на уровне EntityManager, что обеспечивает согласованность данных и возможность отката изменений при возникновении ошибок.
6. Наследование и полиморфизм: JPA поддерживает наследование и полиморфизм, позволяя отображать иерархии классов Java на реляционные структуры данных.
7. Аннотации и XML: Метаданные для отображения могут быть определены с помощью аннотаций в коде Java или в XML-файлах, что дает гибкость в конфигурации.

Эти принципы позволяют JPA обеспечивать эффективное взаимодействие между Java-приложением и базой данных, минимизируя необходимость в ручном написании SQL-кода и управлении ресурсами базы данных

Не все структуры из Java можно засунуть в реляционную модель, например граф надо будет сначала разобрать и только потом сохранить в столбцы  

В JPA отправной точкой является *единица хранения*, которая объединяет отображение классов в предметной области и соединение с базой данных.
## 2. Модели предметной области и метаданные 

#### 2.1 Архитектура

![[Pasted image 20240423141341.png]]

* **Уровень представления** – логика пользовательского интерфейса находится на самом верху. Код, отвечающий за представление, управление страницей и навигацию, находится в уровне представления. Код пользовательского интерфейса может обращаться напрямую к бизнес-сущностям общей модели предметной области и отображать их на экране вместе с элементами управления для выполнения различных действий. В некоторых архитектурах экземпляры бизнес-сущностей могут быть недоступны коду пользовательского интерфейса напрямую: например, когда уровень представления работает на другом компьютере. В таких случаях уровню представления может потребоваться собственная специальная модель передачи данных, представляющая лишь подмножество модели предметной области, подходящей для передачи.
* **Уровень бизнес-логики** – конкретные формы этого уровня сильно различаются в зависимости от приложения. Считается, что уровень бизнес-логики отвечает за реализацию любых бизнес-правил или системных требований, которые воспринимаются пользователями как относящиеся к предметной области. Этот уровень обычно содержит некий управляющий компонент, который знает, когда и какое бизнес-правило применять. В одних системах этот уровень имеет свое внутреннее представление сущностей предметной области. В других – он зависит от реализации предметной модели, общей для всех уровней приложения.
* **Уровень хранения** – уровень длительного хранения данных состоит из набора классов и компонентов, ответственных за сохранение и извлечение данных из одного или нескольких хранилищ. Этому уровню требуется модель сущностей предметной области, состояния которых предполагается хранить. Уровень хранения – то место, где больше всего используются JPA и Hibernate.
* **База данных** – как правило, является внешней и используется несколькими приложениями. Если используется база данных SQL, у нее есть схема и, возможно, хранимые процедуры для выполнения бизнес-логики в непосредственной близости к данным.
* **Вспомогательные и служебные классы** – в каждом приложении имеется набор инфраструктурных вспомогательных и служебных классов, которые используются во всех уровнях приложения (как, например, класс Exception для обработки ошибок). Эти общие инфраструктурные элементы не образуют отдельного уровня, т. к. для них не выполняются правила зависимости между уровнями в многоуровневой архитектуре.
#### 2.2 Реализация предметной модели и метаданные 
Когда такие области ответственности, как хранение данных, управление транзакциями
или авторизация, просачиваются в классы предметной модели, – это пример утечки функциональности (leakage of concerns).

* уровень представления может получать доступ к экземплярам и атрибутам сущностей предметной модели для вывода представлений;
* контроллеры уровня бизнес-логики также могут обращаться к состоянию сущностей предметной модели и вызывать методы сущностей для выполнения бизнес-логики;
* уровень хранения может сохранять экземпляры в базе данных и извлекать их из нее.

**Прозрачность** - это полное разделение ответственности классов объектной модели от уровня хранения. Т е классом должно быть без разницы как они будут храниться и можно сфокусироваться на бизнес логике 

---
**Метаданные** 
Использование **метаданных** в Hibernate играет ключевую роль в процессе объектно-реляционного отображения (ORM). Метаданные Hibernate описывают, как объекты приложения соотносятся с таблицами базы данных и как свойства объектов соотносятся с столбцами таблиц. Эти метаданные могут быть представлены в различных форматах, включая XML-конфигурации и аннотации в коде Java.

 С Hibernate 3.0 и выше, аннотации стали предпочтительным способом определения метаданных. Аннотации, такие как **@Entity, @Table, @Id, @Column**, и другие, используются для аннотирования классов и полей, чтобы указать Hibernate, как отображать объекты на базу данных.
  
---

# 2. Стратегии отображения 
## 2.1 Отображение хранимых классов

Главным аспектом Hibernate будет являться хорошо детализированные модель(больше классов чем таблиц). Например не использовать email как строковое значение класса User, а создать отдельный класс Email с методами sendEmail() и т д. 

Так же для корректного отображения надо выбрать **первичный ключ**, который должен быть уникален естественно(т е в следствии логики приложения) или созданный(например id). Ключ должен быть не изменяемым и не может иметь значение null.
Над полем которое является первичным ключом указывается аннотация **@Id** и **@GeneratedValue** чтобы система сама сгенерировала значения. Для генерации лучше использовать UUID а не AUTO
 

Отображение коллекций и связей между сущностями

Hibernate позволяет отображать коллекции с помощью аннотаций @OneToMany и @ManyToOne. Сортировать благодаря @SortComparator и @OrderBy. Т е для сохранения коллекции с пользовательскими типами используй **@OneToMany** или **@ManyToOne** , а если коллекция стандартного типа то используй **@EllementCollection**. 

@ElementCollection используется для определения коллекции значений или встраиваемых типов, которые не являются сущностями. Это может быть полезно, например, когда у вас есть сущность User, которая имеет коллекцию телефонных номеров или адресов, и вы хотите сохранить эти значения в отдельной таблице базы данных

@CollectionTable используется вместе с @ElementCollection для указания имени таблицы и столбца присоединения (join column), который будет использоваться для хранения коллекции. Это позволяет вам настроить имя таблицы и сопоставление внешних ключей для коллекции

Пример использования этих аннотаций:

```
@Entity
public class User {
@Id
private Long id;
private String name;

@ElementCollection
@CollectionTable(name = "user_phones", joinColumns = @JoinColumn(name = "user_id"))
private Set<String> phoneNumbers = new HashSet<>();
}
```



Контейнер (bag) – неупорядоченная коллекция, позволяющая хранить повторяющиеся
элементы, подобно коллекции с интерфейсом java.util.Collection. Любопытно, но в библиотеке Java Collections реализация контейнера отсутствует. Вы инициализируете свойство объектом ArrayList, а Hibernate игнорирует индексы элементов при сохранении и загрузке элементов.

Для хранения **Map** можно использовать **@ElementCollection @CollectionTable** и указать **@MapKeyColumn(name = "key")**. Key будет столбец где хранить ключи и потом через **@Column** указать где в какой таблице хранить значения значение.  

Для отображения коллекции используй @OneToMany и т д и указывать в их описании такие параметры как FetchType и CascadeType. Он используются для управлением поведения сущностей в hibernate.

**FetchType** определяет, когда связанные сущности должны быть загружены из базы данных:

**CascadeType** определяет, как операции, выполняемые над одной сущностью, должны распространяться на связанные сущности. 

Аннотация **@Embeddable** в Java Persistence API (JPA) используется для объявления класса, который может быть встроен в другие сущности. Это означает, что объекты такого класса могут быть частью таблицы другой сущности без необходимости создавать отдельную таблицу для них.