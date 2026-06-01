## 📌 Оглавление
- [238. Product of Array Except Self [Medium]](#238-product-of-array-except-self-medium)
- [13. Roman to Integer [Easy]](#13-roman-to-integer-easy)
- [151. Reverse Words in a String [Medium]](#151-reverse-words-in-a-string-medium)
- [134. Gas Station [Medium]](#134-gas-station-medium)
- [14. Longest Common Prefix [Easy]](#14-longest-common-prefix-easy)
- [58. Length of Last Word [Easy]](#58-length-of-last-word-easy)
- [125. Valid Palindrome [Easy]](#125-valid-palindrome-easy)
---
## 238. Product of Array Except Self [Medium]

### 🎯 Суть задачи  
Дан массив `nums`. Нужно для каждого `i` вернуть произведение **всех** элементов массива, кроме `nums[i]`. Нельзя использовать деление, нужно `O(n)` по времени. 

Input: nums = [1,2,3,4]

Output: [24,12,8,6]

---

### 🧠 Ключевая идея  
Произведение всех элементов, кроме `nums[i]`, можно представить как:

> произведение всех элементов **слева** от `i`  
> умножить на произведение всех элементов **справа** от `i`.

Вместо того чтобы каждый раз пересчитывать всё с нуля, мы заранее считаем:
- `prefix[i]` — произведение элементов слева до `i`,
- `postfix[i]` — произведение элементов справа после `i`.

---

### 🧩 Паттерн

**Паттерн: Prefix / Suffix**

“Левая часть” и “правая часть” не зависят от самой позиции i, их можно посчитать заранее за O(n)

**RES[i] = f(всё слева от i)   ⨁   g(всё справа от i)**

_Примеры использования:_
- сумма/произведение всех элементов кроме текущего,
- минимум/максимум слева/справа,
- максимальная разница между элементами.

---

### ⏱ Сложность  

- Время: `O(n)` — три линейных прохода по массиву.  
- Память: `O(n)` доп.памяти на `prefix` и `postfix`

---

### 📋 Алгоритм 

1. Проходим массив с начала и считаем произведения всех элементов до текущего.
2. Проходим массив с конца и считаем произведения всех элементов после текущего
3. Для каждого индекса i: res[i] = prefix[i-1] * postfix[i+1].
4. Обрабатываем границы:
   - если слева (`i == 0`) или справа (`i == n-1`), нет элемента берём `1` как нейтральный элемент умножения

---

### ✔️ Мой код (C#)  

```csharp
public int[] ProductExceptSelf(int[] nums)
{
    int n = nums.Length;
    int[] prefix = new int[n];
    int[] postfix = new int[n];
    int[] res = new int[n];

    int product = 1;
    for (int i = 0; i < n; i++)
    {
        product *= nums[i];
        prefix[i] = product;
    }

    product = 1;
    for (int i = n - 1; i >= 0; i--)
    {
        product *= nums[i];
        postfix[i] = product;
    }

    for (int i = 0; i < n; i++)
    {
       if(i == 0)  res[i] = postfix[i+1];
       else if (i+1 == n)  res[i] = prefix[i-1];
       else res[i] = prefix[i-1] * postfix[i+1];
    }

    return res;
}
```

## 13. Roman to Integer [Easy]

### 🎯 Суть задачи  
Дана строка `s`, представляющая римское число. Нужно преобразовать её в целое число.

Input: s = "III"  
Output: 3  

Input: s = "IV"  
Output: 4  

Input: s = "MCMXCIV"  
Output: 1994  

---

### 🧠 Ключевая идея  
Идём по строке слева направо и сравниваем текущий символ со следующим:

> если текущий < следующего → вычитаем  
> иначе → прибавляем  

---

### 🧩 Паттерн  

**Паттерн: Greedy + сравнение соседних элементов**

---

### ⏱ Сложность  

- Время: `O(n)` — один проход по строке  
- Память: `O(1)`  

---

### 📋 Алгоритм  

1. Создать `Dictionary<char, int>` с значениями римских символов  
2. Пройти по строке слева направо  
3. Для каждого символа:
   - взять его значение  
   - если есть следующий символ:
     - если current < next → вычесть  
     - иначе → прибавить  
   - если это последний символ → просто прибавить  

---

### ✔️ Мой код (C#)  

```csharp
 Dictionary<char, int> romanSymbols = new() 
 {
     { 'I', 1 }, { 'V', 5 }, { 'X', 10 }, { 'L', 50 }, { 'C', 100 }, { 'D', 500 }, { 'M', 1000 }
 };

public int RomanToInt(string s)
{
    int finalValue = 0;

    for (int i = 0; i < s.Length; i++)
    {
        int current = romanSymbols[s[i]];

        if (i + 1 < s.Length && current < romanSymbols[s[i + 1]])
            finalValue -= current;
        else
            finalValue += current;
    }

    return finalValue;
}
```

## 151. Reverse Words in a String [Medium]

### 🎯 Суть задачи  
Дана строка `s`. Нужно развернуть порядок слов, удалить лишние пробелы, оставить между словами только один пробел.

Input: s = `"  hello   world  "`  
Output: `"world hello"`

---

### 🧠 Ключевая идея  

1. Использовать встроенные string methods:
   - разбить строку на слова + убрать пустые элементы, развернуть, соединить обратно.

2. Алгоритмический подход:
   - идти по строке справа налево, вручную находить слова, добавлять их в результат в нужном порядке.

---

### 🧩 Паттерн  

**Паттерн: String Parsing / Two Pointers**

Используется, когда:
- нужно вручную обрабатывать строку,
- пропускать символы,
- выделять слова/токены,
- контролировать пробелы или разделители.

---

# Вариант 1 — String Methods

### 🧠 Идея  

Используем встроенные методы C#:
- `Split` с параметром `RemoveEmptyEntries`
- `Reverse`
- `Join`

---

### ⏱ Сложность  

- Время: `O(n)`  
- Память: `O(n)`  
  (создаются массив слов и reversed sequence)

---

### ✔️ Мой код (C#)

```csharp
return string.Join(' ', s.Split(' ', StringSplitOptions.RemoveEmptyEntries).Reverse());
```

# Вариант 2 — Manual Parsing

### 🧠 Идея  

Идём по строке справа налево:
- Пропускаем пробелы, находим конец слова
- Идём влево до пробела, находим начало слова
- Добавляем пробел между словами, но не перед первым
- Добавляем слово в StringBuilder
- Повторяем
  
---

### ⏱ Сложность  

- Время: `O(n)`  
- Память: `O(n)`  

---

### ✔️ Мой код (C#)

```csharp
 public static string ReverseWords(string s)
 {
     StringBuilder sb = new();

     int i = s.Length - 1;
     while (i >= 0)
     {
         //пропускаем пробелы справа
         while (i >= 0 && s[i] == ' ')
         {
             i--;
         }

         //запоминаем конец слова
         int endIndex = i;

         //ищем начало слова
         while (i >= 0 && s[i] != ' ')
         {
             i--;
         }

         int startIndex = i + 1;

         //добавляем пробел между словами, но не перед первым
         if (sb.Length > 0)
         {
             sb.Append(' ');
         }

         // добавляем найденное слово
         for (int k = startIndex; k <= endIndex; k++)
         {
             sb.Append(s[k]);
         }
     }

     return sb.ToString();
 }
```

## 134. Gas Station [Medium]

### 🎯 Суть задачи  
Есть круг из заправок.

- `gas[i]` — сколько бензина можно получить на станции `i`
- `cost[i]` — сколько бензина нужно, чтобы доехать из станции `i` до следующей

Нужно найти индекс станции, с которой можно проехать полный круг.  
Если такой станции нет — вернуть `-1`.

Input:  
gas = `[1,2,3,4,5]`  
cost = `[3,4,5,1,2]`

Output:  
`3`
<img width="1693" height="929" alt="ChatGPT Image 24 мая 2026 г , 09_02_32" src="https://github.com/user-attachments/assets/3f3bc7cf-6a95-43af-8190-59e69b94a48d" />

---

### 🧠 Ключевая идея  

На каждой станции: сначала заправляемся, потом тратим бензин на дорогу до следующей станции.

```text
tank = tank + gas[i] - cost[i]
```
---

### 🧩 Паттерн  

**Паттерн: Greedy**

Если при движении от текущего `start` бак стал отрицательным, значит:
- текущий старт невозможен,
- и все станции между `start` и текущей тоже невозможны.
---

### 🧠 Идея  

Вместо проверки каждого старта отдельно:

- идём по массиву один раз,
- поддерживаем текущий баланс бензина (`tank`),
- если бензина не хватило:
  - текущий старт невозможен,
  - все станции между стартом и текущей тоже невозможны,
  - начинаем с новой станции.
- `total` отвечает на вопрос "хватает ли бензина вообще на весь круг?"
---

### ⏱ Сложность  

- Время: `O(n)`  
- Память: `O(1)`

---

### ✔️ Мой код (C#)

```csharp
public int CanCompleteCircuit(int[] gas, int[] cost)
{
    int total = 0;
    int tank = 0;
    int start = 0;

    for (int i = 0; i < gas.Length; i++)
    {
        int diff = gas[i] - cost[i];

        total += diff;
        tank += diff;

        if (tank < 0)
        {
            start = i + 1;
            tank = 0;
        }
    }

    return total >= 0 ? start : -1;
}
```
## 14. Longest Common Prefix [Easy]

### 🎯 Суть задачи  

Нужно найти самый длинный общий префикс для массива строк.

Если общего префикса нет — вернуть пустую строку.

Input:

```text
["flower","flow","flight"]
```

Output:

```text
"fl"
```

---

### 🧠 Ключевая идея  

Берём первое слово как эталон и проверяем его буквы по индексам.

Как только:
- строка закончилась
ИЛИ
- буква не совпала

→ общий префикс закончился.

---

### 🧩 Паттерн  

**Паттерн: Vertical Scanning**

---

### ⏱ Сложность  

- Время: `O(n * m)`
  - `n` — количество строк
  - `m` — длина общего префикса / самой короткой строки

- Память: `O(m)`
  - `StringBuilder` для результата

---

### 📋 Алгоритм  

1. Берём первое слово, проходим по каждой букве
2. Для каждой буквы:
   - проверяем все остальные строки
3. Если:
   - буквы с индексом `i` не существует (строка закончилась)
   - или буква существует, но не совпадает
   → возвращаем текущий результат

```csharp
if (i >= strs[j].Length || strs[j][i] != letter)
```

4. Если все строки совпали:
   - добавляем букву в префикс

---

### ✔️ Мой код (C#)

```csharp
public string LongestCommonPrefix(string[] strs)
{
    StringBuilder sb = new();
    string firstWord = strs[0];

    for (int i = 0; i < firstWord.Length; i++)
    {
        char letter = firstWord[i];

        for (int j = 1; j < strs.Length; j++)
        {
            if (i >= strs[j].Length || strs[j][i] != letter)
            {
                return sb.ToString();
            }
        }

        sb.Append(letter);
    }

    return sb.ToString();
}
```
---

## 58. Length of Last Word [Easy]

### 🎯 Суть задачи

Нужно вернуть длину последнего слова в строке.

Input:

```text
"Hello World"
```

Output:

```text
5
```

---

### 🧠 Ключевая идея  

1. ⭐️ Алгоритмически пройти строку с конца
2. Использовать string methods (`Split`)
   
---

### 🧩 Паттерн  

**Паттерн: Two Pointers / String Traversal**

Идём по строке с конца:
- пропускаем пробелы,
- считаем буквы последнего слова.

---

## Вариант 1 — Алгоритмический

### 🧠 Идея  

Идём с конца строки, используем `while` (удобен, когда заранее неизвестно количество шагов):

1. Пропускаем пробелы справа
2. Считаем буквы последнего слова

---

### ⏱ Сложность  

- Время: `O(n)` в худшем случае
- Память: `O(1)`

---

### ✔️ Мой код (C#)

```csharp
public int LengthOfLastWord(string s)
{
    int i = s.Length - 1;
    int count = 0;

    // пропускаем пробелы справа
    while (i >= 0 && s[i] == ' ')
    {
        i--;
    }

    // считаем буквы последнего слова
    while (i >= 0 && s[i] != ' ')
    {
        count++;
        i--;
    }

    return count;
}
```

---

## Вариант 2 — String Methods

### ⏱ Сложность  

- Время: `O(n)`
- Память: `O(n)`

- `Split` проходит по всей строке, находит разделители, создаёт массив строк, создаёт отдельную строку для каждого слова. Даже если нужен только последний элемент.

---

### ✔️ Мой код (C#)

```csharp
var words = s.Split(' ', StringSplitOptions.RemoveEmptyEntries);
return words[words.Length - 1].Length;
```

---

## 125. Valid Palindrome [Easy]

### 🎯 Суть задачи  

Нужно проверить, является ли строка палиндромом (игнорируя пробелы, символы, регистр букв).

Input:

```text
"A man, a plan, a canal: Panama"
```

Output:

```text
true
```

---

### 🧠 Ключевая идея  

Очищаем строку (оставить только буквы и цифры, привести всё к одному регистру), проверяем симметричность двумя указателями.

---

### 🧩 Паттерн  

**Паттерн: String Filtering + Two Pointers**

---

## ⏱ Сложность  

- Время: `O(n)`
- Память: `O(n)` (можно `O(1)` память, если не создавать новую строку)

---

### ✔️ Мой код (C#)

```csharp
public bool IsPalindrome(string s)
{
    StringBuilder sb = new();
    foreach (char c in s)
    {
        if (char.IsLetterOrDigit(c))
        {
            sb.Append(char.ToLowerInvariant(c));
        }
    }

    int left = 0;
    int right = sb.Length - 1;

    while (left < right)
    {
        if (sb[left] != sb[right])
        {
            return false;
        }

        left++;
        right--;
    }

    return true;
}
```

---
