# Zaawansowane koncepty języka C#

### 1. Delegaty, lambdy i funkcje anonimowe



##### Delegaty umożliwiają zapisanie metody do zmiennej, i/lub przekazanie jej jako parametr do innej funkcji.

```c#
class Program
{
	// Definicja delegatu
	public delegate int MyDelegate(int a, int b);

	static void Main(string[] args)
	{
		// Przypisanie metody do zmiennej
        var someVariable = new MyDelegate(AddTwoNumbers);
        Console.WriteLine(someVariable(1, 2)); 
			
        // Wywołanie funkcji - argument to zmienna typu delegat
        SomeFunction(someVariable);
            
        // Wywołanie funkcji - argument to inna metoda
        SomeFunction(AddTwoNumbers);
	}
		
		// Funkcja przyjmująca delegat
        public static void SomeFunction(MyDelegate myDelegate)
        {
            Console.WriteLine(myDelegate(2, 3));
        }
        
       // Przekazywana metoda
        public static int AddTwoNumbers(int a, int b)
        {
            return a + b;
        }
        
    }
```



#### Anonimowe funkcje

```c#
class Program
{
    //Definicja delegatu
	public delegate int MyDelegate(int a, int b);
    static void Main(string[] args)
    {
        //Przypisanie do delegatu funkcji anonimowej
        MyDelegate someVariable = delegate (int a, int b) { return a + b; };

        Console.WriteLine(someVariable(1,2));
        SomeFunction(someVariable);
    }

    public static void SomeFunction(MyDelegate someFunction)
    {
        Console.WriteLine(someFunction(2, 3));
    }

}
```



#### Lambdy

```c#
class Program
{
    //Definicja delegatu
	public delegate int MyDelegate(int a, int b);
    static void Main(string[] args)
    {
        //Przypisanie do delegatu lambdy
        						//(int a, int b) => { return a + b; };
        MyDelegate someVariable = (a,b) => a + b;

        Console.WriteLine(someVariable(1,2));
        SomeFunction(someVariable);
    }

    public static void SomeFunction(MyDelegate someFunction)
    {
        Console.WriteLine(someFunction(2, 3));
    }

}
```



#### Func i Action

Jeśli funkcja przyjmuję parametr i coś zwraca używamy Func, a jeśli nic nie zwraca używamy Action.

##### Użycie Func

```c#
class Program
{
    static void Main(string[] args)
    {
        //Funkcja przyjmuję 2 inty i zwraca inta
        Func<int, int, int> someVariable = (a,b) => a + b;

        Console.WriteLine(someVariable(1,2));
        SomeFunction(someVariable);
    }

    public static void SomeFunction(Func<int, int, int> someFunction)
    {
        Console.WriteLine(someFunction(2, 3));
    }

}
```



##### Użycie Action

```    c#
class Program
{
	static void Main(string[] args)
    {
    	//Funkcja przyjmuję 2 inty i nic nie zwraca
        Action<int, int> someVariable = (a, b) => { Console.WriteLine(a + b); };
            
        someVariable(2, 3);
        SomeFunction(someVariable);
    }
    
    public static void SomeFunction(Action<int, int> someFunction)
    {
         someFunction(3, 4);
     }

}
```



## 2. Klasy anonimowe i linq

###### Klasy anonimowe - deklaracja

```c#
var a = new
{
	ValueOfSomething = 2,
	ValueOfSomethingElse = "Ala ma kota"
};

Console.WriteLine(a.ValueOfSomething);
```

##### Linq - ułatw sobie życie

###### Nasze dane

```c#
var listOfInts = new List<int>
{
	2, 5, 8, 9, 3
};

var listOfStrings = new List<string>
{
	"Ala", "ma", "czarnego", "kota"
};

var listOfUsers = new List<User>
{
	new User
	{
		Name = "Ala",
		Age = 21,
        Email = "ala@koty.pl"
	},
	new User
	{
		Name = "Kuba",
		Age = 24,
		Email = "fajny@email.com"
	},
	new User
	{
		Name = "Robert",
		Age = 210,
		Email = "fajny@email.com"
	}
};
```

###### Typowy kod

```c#
//Podejście klasyczne
foreach (var item in listOfInts)
{
	if(item>5)
	{
		Console.WriteLine(item);
	}
}

//Wykorzystanie linq
foreach (var item in listOfInts.Where(x => x>5))
{
	Console.WriteLine(item);
}


//Podejście klasyczne
foreach (var item in listOfInts)
{
	Console.WriteLine(item);
}

//Wykorzystanie linq
listOfInts.ForEach(x => 
{
	Console.WriteLine(x); 
});


// -------- Przykładowe wykorzystanie linq

//Średnia wartość
var avg = listOfInts.Average(x => x);

//Lista obiektów spełniających warunek
var someUsers = listOfUsers.Where(x => x.Age > 22).ToList();

//Czwarty element spełniający warunek
var someValue = listOfInts.Where(x => x > 5).Skip(3).FirstOrDefault();

//Lista zawierająca tylko 2 i 3 element
var someStrings = listOfStrings.Skip(1).Take(2).ToList();

//Konwersja int na string
var intsConvertedToStrings = listOfInts.Select(x => x.ToString()).ToList();

//Konkatenacja
var strings = listOfStrings.Select(x => x + "something").ToList();

//Konwersja po między klasami
var viewModels = listOfUsers.Select(x => new UserViewModel
{
	Age = x.Age,
    Email = x.Email,
    Name = x.Name,
    Title = GetUserTitle(x.Age)
}).ToList();

//Kolejny przykład - dzięki wykorzystaniu klasy anonimowej metoda GetUserTitle() została wywołana tylko raz po mimo 5 przypisań. 
var viewModels = listOfUsers
    .Select(x => new
    {
        UserData = x,
        Title = GetUserTitle(x.Age)
    })
    .Select(x => new UserViewModel
    {
        Age = x.UserData.Age,
        Email = x.UserData.Email,
        Name = x.UserData.Name,
        Title = x.Title,
        Title2 = x.Title,
        Title3 = x.Title,
        Title4 = x.Title,
    })
    .ToList();
```



## 3. Extensions methods

###### Dopisywanie nowej metody do klasy

```c#
//Utworzenie klasy statycznej umożliwia dopianie metod do innych klas
public static class MyClass
{
    //Dopisanie metody do Klasy List<string>
	public static void SomeFunction(this List<string> someList)
    {
    	someList.ForEach(x => { Console.WriteLine(x); });
    }
	
    //Dopisanie metody do typu generycznego string
    public static void SomeFunction(this string someString)
    {
    	Console.WriteLine(someString);
    }
}


//Przykład wykorzystania
class Program
{
	static void Main(string[] args)
    {
    	var listOfStrings = new List<string>
        {
        	"aaa",
            "bbb",
            "ccc"
        };

        var someString = "abc";

        listOfStrings.SomeFunction();
        someString.SomeFunction();
    }
}
```



## 4. IEnumerable vs IQueryable vs ICollection vs IList

###### IEnumerable<T> implementuje:

- IEnumerator<T> GetEnumerator()
- IEnumerator IEnumerable.GetEnumerator()

Pozwala to używać pętli foreach



###### ICollection<T> implementuje:

- Count
- IsReadOnly
- Add(T item)
- Clear()
- Contains(T item)
- CopyTo(T[] array, int arrayIndex)
- Remove(T item)

+Metody IEnumerable



###### IList<T> implementuje:

- T this[int index]
- IndexOf(T item)
- Insert(int index, T item)
- RemoveAt(int index)

+Metody IEnumerable i ICollection



###### IQueryable<T> jest zwracane przez linq.

