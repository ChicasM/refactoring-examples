introduce-null-object:csharp

###

1.ru. Из интересующего вас класса создайте подкласс, который будет выполнять роль Null-объекта.
1.en. From the class in question, create a subclass that will perform the role of null object.
1.uk. З потрібного вам класу створіть підклас, який виконуватиме роль Null-об'єкту.

2.ru. В обоих классах создайте метод <code>IsNull</code>, который будет возвращать <code>true</code> для Null-объекта и <code>false</code> для реального класса.
2.en. In both classes, create the method <code>IsNull</code>, which will return <code>true</code> for a null object and <code>false</code> for a real class.
2.uk. У обох класах створіть метод <code>IsNull</code>, який повертатиме <code>true</code> для Null-об'єкту і <code>false</code> для реального класу.

3.ru. Найдите все места, где код может вернуть <code>null</code> вместо реального объекта. Измените этот код так, чтобы он возвращал Null-объект.
3.en. Find all places where the code may return <code>null</code> instead of a real object. Change the code so that it returns a null object.
3.uk. Знайдіть усі місця, де код може повернути <code>null</code> замість реального об'єкту. Змініть цей код так, щоб він повертав Null-об'єкт.

4.ru. Найдите все места, где переменные реального класса сравниваются с <code>null</code>. Замените такие проверки вызовом метода <code>IsNull</code>.
4.en. Find all places where the variables of the real class are compared with <code>null</code>. Replace these checks with a call for <code>IsNull</code>.
4.uk. Знайдіть усі місця, де змінні реального класу порівнюються з <code>null</code>. Замініть такі перевірки викликом методу <code>IsNull</code>.

5.ru. <ul><li>Если в этих условных операторах при значении не равном <code>null</code> выполняются методы исходного класса, переопределите эти методы в Null-классе и вставьте туда код из <code>else</code> части условия. После этого условный оператор можно будет вообще удалить, а разное поведение будет осуществляться за счёт полиморфизма.</li><li>Если не все так просто, и методы переопределить не получается, посмотрите, можно ли просто выделите операции, которые должны были выполняться при значении равном <code>null</code> в новые методы Null-объекта. Вызывайте эти методы вместо старого кода в <code>else</code> как операции по умолчанию.</li></ul>
5.en. <ul><li>If methods of the original class are performed in these conditionals for values not equal to <code>null</code>, redefine these methods in the null class and put the code from the <code>else</code> part of the conditional code there. Then you can delete the conditional entirely, since differing behavior will be controlled through polymorphism.</li><li>If things are more complicated and redefining the methods is "not in the cards", see whether you can simply move the operations that should be performed for values equal to <code>null</code> to new methods of the Null object. Call these methods instead of the old code in <code>else</code> as default operations.</li></ul>
5.uk. <ul><li>Якщо в цих умовних операторах при значенні, не рівному <code>null</code>, виконуються методи початкового класу, перевизначить ці методи в Null-класі і вставте туди код з  частини умови <code>else</code>. Після цього умовний оператор можна буде взагалі видалити, а різна поведінка здійснюватиметься за рахунок поліморфізму.</li><li>Якщо не все так просто, і методи перевизначити не виходить, подивіться, чи можна просто виділити операції, які повинні були виконуватися при значенні рівному <code>null</code> в нові методи Null-об'єкту. Викликайте ці методи замість старого коду в <code>else</code> як операції за умовчанням.</li></ul>



###

```
public class Company
{
  //…
  private Customer customer;
  
  public Customer Customer
  {
    get{ return customer; }
  }
}

public class Customer
{
  public string Name {
    //…
  }

  public BillingPlan GetPlan() {
    //…
  }
  public PaymentHistory GetHistory() {
    //…
  }
}

public class PaymentHistory
{
  public int WeeksDelinquentInLastYear {
    //…
  }
}

// Somewhere in client code
Customer customer = site.Customer;
string customerName;
if (customer == null)
  customerName = "N/A";
else
  customerName = customer.Name;

//…
BillingPlan plan;
if (customer == null)
  plan = BillingPlan.Basic();
else
  plan = customer.GetPlan();

//…
int weeksDelinquent;
if (customer == null)
  weeksDelinquent = 0;
else
  weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
```

###

```
public class Company
{
  //…
  private Customer customer;
  
  public Customer Customer
  {
    get{ return customer ?? Customer.NewNull(); }
  }
}

public class Customer
{
  public virtual bool IsNull
  {
    get{ return false; }
  }
  public virtual string Name {
    //…
  }

  public static Customer NewNull()
  {
    return new NullCustomer();
  }
  public virtual BillingPlan GetPlan() {
    //…
  }
  public virtual PaymentHistory GetHistory() {
    //…
  }
}
public class NullCustomer: Customer
{
  public override bool IsNull
  {
    get{ return true; }
  }
  public override string Name
  {
    get{ return "N/A"; }
  }

  public override BillingPlan GetPlan()
  {
    return BillingPlan.Basic();
  }
  public override PaymentHistory GetHistory()
  {
    return PaymentHistory.NewNull();
  }
}

public class PaymentHistory
{
  public virtual bool IsNull
  {
    get{ return false; }
  }
  public virtual int WeeksDelinquentInLastYear {
    //…
  }

  public static PaymentHistory NewNull()
  {
    return new NullPaymentHistory();
  }
}
public class NullPaymentHistory: PaymentHistory
{
  public override bool IsNull
  {
    get{ return true; }
  }
  public override int WeeksDelinquentInLastYear
  {
    get{ return 0; }
  }
}

// Somewhere in client code
Customer customer = site.Customer;
string customerName = customer.Name;

//…
BillingPlan plan = customer.GetPlan();

//…
int weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
```

###

Set step 1

Select name of "Company"

#|ru| Рассмотрим рефакторинг на примере классов коммерческой компании.
#|en| Let's look at this refactoring, using a commercial company class as an example.
#|uk| Розглянемо рефакторинг на прикладі класів комерційної компанії.

Select name of "Customer"

#|ru| Каждая компания знает своих покупателей (<code>Customer</code>).
#|en| Every business has customers (<code>Customer</code>).
#|uk| Кожна компанія знає своїх покупців (<code>Customer</code>).

Select "Name" in "Customer"
+Select "GetPlan" in "Customer"
+Select "GetHistory" in "Customer"

#|ru| В свою очередь у покупателей есть свои свойства и поведения.
#|en| Customers in turn have their own properties and behaviors.
#|uk| В свою чергу у покупців є свої властивості та поведінки.

Go to "// Somewhere in client code"

#|ru| Клиентский код оперирует этими параметрами, чтобы делать какую-то работу. Например, так выглядит код получения имени текущего клиента компании.
#|en| The client code operates on these parameters in order to do its work. For example, this is code for getting the name of a current customer:
#|uk| Клієнтський код оперує цими параметрами, щоб робити якусь роботу. Наприклад, так виглядає код отримання імені поточного клієнта компанії.

Select "if (customer == null)"

#|ru| Обратите внимание на условный оператор, проверяющий, есть ли клиент в компании. Такая ситуация вполне может приключиться, если компания новая, либо старый покупатель решил сменить поставщика.
#|en| Note the conditional that verifies whether the business has the customer in question. This situation may occur if the business is new or an old customer has decided to change vendors.
#|uk| Зверніть увагу на умовний оператор, перевіряючий, чи є клієнт в компанії. Така ситуація цілком може статись, якщо компанія нова, або старий покупець вирішив змінити постачальника.

#|ru| В коде может быть много таких повторяющихся проверок на <code>null</code>, что сигнализирует о потребности введения Null-объекта.
#|en| The code may contain many such repetitive <code>null</code> verifications, which indicates the need to introduce a null-object.
#|uk| У коді може бути багато таких повторюваних перевірок на <code>null</code>, що сигналізує про потребу введення Null-об'єкта.

#|ru| Первым делом создаём нулевой класс <code>NullCustomer</code> и модифицируем класс <code>Сustomer</code>, чтобы он поддерживал запрос проверки на <code>null</code>.
#|en| First create a <code>null</code>-object class <code>NullCustomer</code> and modify the <code>Customer</code> class so that it supports a query for <code>null</code> verification.
#|uk| Спершу створюємо нульовий клас <code>NullCustomer</code> і модифікуємо клас <code>Сustomer</code> так, щоб він підтримував запит перевірки на <code>null</code>.

Set step 2

Go to beginning of "Customer"

Print:
```

  public virtual bool IsNull
  {
    get{ return false; }
  }
```

Wait 500ms

Go to after "Customer"

Print:
```

public class NullCustomer: Customer
{
  public override bool IsNull
  {
    get{ return true; }
  }
}
```

Wait 500ms

Go to before "GetPlan" in "Customer"

#|ru| Для создания нулевых клиентов введём фабричный метод, благодаря чему клиентам не обязательно будет знать о существовании нулевого класса.
#|en| To create "null" clients, let's introduce a factory method. Thanks to it, client code will not need to know about the existence of the null-object
#|uk| Для створення нульових клієнтів введемо фабричний метод, завдяки чому клієнтам не обов'язково буде знати про існування нульового класу.

Print:
```

  public static Customer NewNull()
  {
    return new NullCustomer();
  }
```

Select "string |||Name|||"
+Select name of "GetPlan"
+Select name of "GetHistory"
+Select "int |||WeeksDelinquentInLastYear|||"

#|ru| Также нужно сделать виртуальными все свойства и методы, которые мы в дальнейшем будем переопределять в нулевых классах.
#|en| You also need to make the virtual all the properties and methods that we will then override in the null classes.
#|uk| Також потрібно зробити віртуальними всі властивості і методи, які ми надалі будемо перевизначати в нульових класах.

Select "public||| |||string Name"
+Select "public||| |||BillingPlan"
+Select "public||| |||PaymentHistory"
+Select "public||| |||int WeeksDelinquentInLastYear"

Replace " virtual "

Wait 500ms

Set step 3

Select "return customer"

#|ru| Теперь мы должны модифицировать все участки кода, где запрашиваются объекты <code>Сustomer</code>, и модифицировать их так, чтобы возвращать нулевого пользователя вместо <code>null</code>.
#|en| Now we should handle all code that returns <code>Customer</code> objects. We should add the checks, which will return our <code>null</code> object instead of <code>null</code> value.
#|uk| Тепер ми повинні модифікувати всі ділянки коду, де э запит щодо об'єктів <code>Сustomer</code>, і модифікувати їх так, щоб повертати нульового користувача замість <code>null</code>.

Print "return customer ?? Customer.NewNull()"


Set step 4

Select "if (|||customer == null|||)"

#|ru| После этого в остальном коде можно заменить все проверки вида <code>Customer == null</code> на вызовы <code>Customer.IsNull</code>.
#|en| Then, in the remaining code, replace all checks of the type <code>Customer == null</code> with calls of <code>Customer.IsNull</code>.
#|uk| Після цього в іншому коді можна замінити всі перевірки виду <code>Customer == null</code> на виклики <code>Customer.IsNull</code>.

Print "customer.IsNull"

#|ru| Это самая сложная часть данного рефакторинга, так как для каждого заменяемого источника <code>null</code> необходимо найти все случаи проверки на <code>null</code> и отредактировать их. Если объект интенсивно передаётся, их может быть нелегко проследить.
#|en| This is the most complex part of the refactoring. For each source of <code>null</code> that you are replacing, you must find all <code>null</code> checks and change them. If an object is passed back and forth between methods, doing so consistently can be difficult.
#|uk| Це найскладніша частина даного рефакторинга, бо для кожного джерела<code>null</code>, що замінюється,  необхідно знайти всі випадки перевірки на <code>null</code> і відредагувати їх. Якщо об'єкт інтенсивно передається, їх може бути нелегко простежити.

#C|ru| После всех замен стоит провести компиляцию и тщательное тестирование.
#S Отлично, все работает, можем продолжать!

#C|en| After all replacements are done, compile and test carefully.
#S Great, it all works! We can continue then.

#C|uk| Після всіх замін варто провести компіляцію і ретельне тестування.
#S Супер, все працює, можемо продовжувати.

Set step 5

#|ru| В данный момент мы ничего не выигрываем от применения <code>isNull</code> вместо <code>== null</code>. Выгода появится тогда, когда код поведения в нулевой ситуации будет перемещён в нулевой класс, а условные операторы вообще убраны.
#|en| We do not yet gain any benefit from using <code>isNull</code> instead of plain <code>== null</code>  checks. The benefit will be visible when the code, which used to work in null cases will be moved straight to the null-object class.
#|uk| В даний момент ми нічого не виграємо від застосування <code>isNull</code> замість <code>== null</code>. Вигода з'явиться тоді, коли код поведінки в нульовій ситуації буде переміщений в нульовий клас, а умовні оператори взагалі видалені.

Select "customerName = "N/A""

#|ru| Итак, начнём перемещать поведения в нулевой класс. И первое, что мы сделаем, это перенесём название покупателя по умолчанию.
#|en| Let's start moving behaviors. The first thing to do is move the default customer name to the null-object class.
#|uk| Отже, почнемо переміщати поведінки в нульовий клас. І перше, що ми зробимо, це перенесемо початкову назву покупця.

Go to the end of "NullCustomer"

Print:
```

  public override string Name
  {
    get{ return "N/A"; }
  }
```

Wait 500ms

Select:
```
string customerName;
if (customer.IsNull)
  customerName = "N/A";
else
  customerName = customer.Name;
```

#|ru| После этого можно убрать проверку на <code>null</code> из части клиентского кода.
#|en| Then remove the check for <code>null</code> from the corresponding part of the client code.
#|uk| Після цього можна прибрати перевірку на <code>null</code> з частини клієнтського коду.

Print:
```
string customerName = customer.Name;
```

#|ru| То же самое можно проделать и с остальными методами, к которым возможно придумать поведение по умолчанию.
#|en| Do the same with the remaining methods for which you can think of a default behavior.
#|uk| Те ж саме можна зробити і з рештою методів, до яких можливо придумати типову поведінку.

Go to the end of "NullCustomer"

Print:
```


  public override BillingPlan GetPlan()
  {
    return BillingPlan.Basic();
  }
```

Wait 500ms

Select:
```
BillingPlan plan;
if (customer.IsNull)
  plan = BillingPlan.Basic();
else
  plan = customer.GetPlan();
```

Replace:
```
BillingPlan plan = customer.GetPlan();
```

Select "customer.GetHistory()"

#|ru| При внимательном рассмотрении последнего участка можно заметить, что там содержится потенциальная ошибка доступа уже к объекту оплат, в случае, когда объект пользователя не имеет никакой истории оплат.
#|en| Careful review of the last bit of code could show a potential access error. It will occur when somebody will access a payment object while user object won't have any payment history.
#|uk| При уважному розгляді останньої ділянки можна помітити, що там міститься потенційна помилка доступу вже до об'єкта оплат, у разі, коли об'єкт користувача не має ніякої історії оплат.

#|ru| Чтобы решить проблему, можно создать нулевой класс ещё и для класса истории оплат.
#|en| To solve the problem, you can create a null-object class for the payment history class as well.
#|uk| Щоб вирішити проблему, можна створити нульовий клас ще й для класу історії оплат.

Go to beginning of "PaymentHistory"

Print:
```

  public virtual bool IsNull
  {
    get{ return false; }
  }
```

Wait 500ms

Go to after "PaymentHistory"

Print:
```

public class NullPaymentHistory: PaymentHistory
{
  public override bool IsNull
  {
    get{ return true; }
  }
}
```

Wait 500ms

Go to end of "PaymentHistory"

Print:
```


  public static PaymentHistory NewNull()
  {
    return new NullPaymentHistory();
  }
```

Go to the end of "NullPaymentHistory"

#|ru| После создания нулевого объекта можно добавить в него поведение по умолчанию.
#|en| Once the null-object has been defined, you can move default behavior to it.
#|uk| Після створення нульового об'єкта можна додати в нього типову поведінка.

Print:
```

  public override int WeeksDelinquentInLastYear
  {
    get{ return 0; }
  }
```

Select "customer.GetHistory()"

#|ru| Теперь о проблеме доступа к нулевому объекту истории оплат можно не волноваться. Но это ещё не всё.
#|en| Now we can rest easy about any potential problem accessing the null-object of the payment history. But there are still other things to take care of.
#|uk| Тепер про проблему доступу до нульового об'єкту історії оплат можна не хвилюватися. Але це ще не все.

#|ru| Мы можем возвращать нулевой объект истории оплат из нулевого объекта покупателей, полностью избавившись от проверок на <code>null</code> в клиентском коде.
#|en| We can return the null-object of the payment history from the null-object of customers, fully ridding the client code of checks for <code>null</code>.
#|uk| Ми можемо повертати нульовий об'єкт історії оплат з нульового об'єкта покупців, повністю позбавившись від перевірок на <code>null</code> в клієнтському коді.

Go to the end of "NullCustomer"

Print:
```

  public override PaymentHistory GetHistory()
  {
    return PaymentHistory.NewNull();
  }
```

Select:
```
int weeksDelinquent;
if (customer.IsNull)
  weeksDelinquent = 0;
else
  weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
```

Replace:
```
int weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
```

#C|ru| Запускаем финальную компиляцию.
#S Отлично, все работает!

#C|en| Let's perform the final compilation and testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальну компіляцію.
#S Супер, все працює.

Set final step

#|ru|Q На этом рефакторинг можно считать оконченным. В завершение, можете посмотреть разницу между старым и новым кодом.
#|en|Q The refactoring is complete! You can compare the old and new code if you like.
#|uk|Q На цьому рефакторинг можна вважати закінченим. На завершення, можете подивитися різницю між старим та новим кодом.