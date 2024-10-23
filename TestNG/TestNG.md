# Конспект по TestNG

## Основные концепции TestNG

1. **Аннотации**:
•  `@Test`: Основная аннотация для обозначения тестового метода.

•  `@BeforeSuite`, `@AfterSuite`: Методы, выполняемые до и после всех тестов в наборе.

•  `@BeforeClass`, `@AfterClass`: Методы, выполняемые до и после всех тестов в классе.

•  `@BeforeMethod`, `@AfterMethod`: Методы, выполняемые до и после каждого тестового метода.

•  `@DataProvider`: Используется для передачи данных в тестовые методы.


2. **Конфигурационные файлы**:
•  `testng.xml`: Файл конфигурации, который позволяет группировать тесты, задавать приоритеты, параметры и параллельное выполнение тестов.


3. **Параметризация тестов**:
•  Использование аннотации `@Parameters` для передачи параметров из XML-файла.

•  Использование аннотации `@DataProvider` для передачи данных в тестовые методы.


4. **Группировка тестов**:
•  Аннотация `@Test` поддерживает атрибут `groups`, который позволяет группировать тесты и выполнять их вместе.


5. **Зависимости тестов**:
•  Атрибут `dependsOnMethods` в аннотации `@Test` позволяет задавать зависимости между тестами.

**Mock-объекты** (или просто моки) — это искусственные объекты или функции, которые имитируют поведение реальных объектов. Они широко используются в тестировании программного обеспечения для изоляции тестируемого кода от внешних зависимостей



## Пример использования аннотаций

```java
import org.testng.annotations.Test;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.AfterClass;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Parameters;
import org.testng.Assert;

public class ExampleTest {

@BeforeClass
public void setUp() {
// Код инициализации
}

@Test(groups = {"group1"})
public void testMethod1() {
Assert.assertTrue(true);
}

@Test(groups = {"group2"}, dependsOnMethods = {"testMethod1"})
public void testMethod2() {
Assert.assertTrue(true);
}

@Test(dataProvider = "dataMethod")
public void testWithDataProvider(int number, String name) {
Assert.assertNotNull(name);
}

@DataProvider
public Object[][] dataMethod() {
return new Object[][] { { 1, "John" }, { 2, "Jane" } };
}

@AfterClass
public void tearDown() {
// Код завершения
}
}
