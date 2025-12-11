✔️ String functions
✔️ Numeric functions
✔️ Collection functions (list/map/set)
✔️ Encoding functions

Each function includes a **short explanation + easy example**.

---

# 🌱 **1. STRING FUNCTIONS**

String functions help you manipulate text values.

---

## 🔹 **lower()**

Converts string to lowercase.

```hcl
lower("HELLO")
```

Output:

```
"hello"
```

---

## 🔹 **upper()**

Converts string to uppercase.

```hcl
upper("hello")
```

Output:

```
"HELLO"
```

---

## 🔹 **trim()**

Removes spaces from both ends.

```hcl
trim("  hello  ")
```

Output:

```
"hello"
```

---

## 🔹 **replace()**

Replace a substring.

```hcl
replace("hello world", "world", "Terraform")
```

Output:

```
"hello Terraform"
```

---

## 🔹 **format()**

Like printf → insert values into text.

```hcl
format("Instance-%s-%d", "dev", 1)
```

Output:

```
"Instance-dev-1"
```

---

## 🔹 **substr()**

Extract part of a string.

```hcl
substr("terraform", 0, 4)
```

Output:

```
"terr"
```

---

## 🔹 **join()**

Convert list → string (with separator).

```hcl
join(",", ["a", "b", "c"])
```

Output:

```
"a,b,c"
```

---

## 🔹 **split()**

Convert string → list.

```hcl
split(",", "a,b,c")
```

Output:

```
["a","b","c"]
```

---

# 🔢 **2. NUMERIC FUNCTIONS**

---

## 🔹 **min()**

Minimum of values.

```hcl
min(5, 10, 2)
```

Output:

```
2
```

---

## 🔹 **max()**

Maximum of values.

```hcl
max(5, 10, 2)
```

Output:

```
10"
```

---

## 🔹 **ceil()**

Round *up* to nearest integer.

```hcl
ceil(4.3)
```

Output:

```
5
```

---

## 🔹 **floor()**

Round *down*.

```hcl
floor(4.8)
```

Output:

```
4
```

---

## 🔹 **parseint()**

Convert string → number.

```hcl
parseint("42", 10)
```

Output:

```
42
```

---

# 🧺 **3. COLLECTION FUNCTIONS (list/map/set)**

These functions help manipulate lists, maps, or sets.

---

## 🔹 **length()**

Returns number of elements.

```hcl
length(["a", "b", "c"])
```

Output:

```
3
```

---

## 🔹 **contains()**

Check if list contains a value.

```hcl
contains(["a","b","c"], "b")
```

Output:

```
true
```

---

## 🔹 **element()**

Access list element by index.

```hcl
element(["dev","stage","prod"], 1)
```

Output:

```
"stage"
```

---

## 🔹 **keys()**

Returns keys of a map.

```hcl
keys({ a = 1, b = 2 })
```

Output:

```
["a", "b"]
```

---

## 🔹 **values()**

Returns values of a map.

```hcl
values({ a = 1, b = 2 })
```

Output:

```
[1, 2]
```

---

## 🔹 **lookup()**

Fetch a value from a map (with default fallback).

```hcl
lookup({a = 1, b = 2}, "b", 0)
```

Output:

```
2
```

If key not found → return default.

---

## 🔹 **merge()**

Merge two or more maps.

```hcl
merge(
  {a = 1},
  {b = 2},
  {c = 3}
)
```

Output:

```
{a = 1, b = 2, c = 3}
```

---

## 🔹 **sort()**

Sort list values.

```hcl
sort(["c","a","b"])
```

Output:

```
["a","b","c"]
```

---

## 🔹 **toset() / tolist() / tomap()**

Convert one type to another.

```hcl
toset(["a","b","b"])
```

Output (duplicates removed):

```
["a","b"]
```

---

# 🔐 **4. ENCODING FUNCTIONS**

Used for converting data types to/from encoded formats.

---

## 🔹 **base64encode()**

Encode string in base64.

```hcl
base64encode("hello")
```

Output:

```
"aGVsbG8="
```

---

## 🔹 **base64decode()**

Decode base64 string.

```hcl
base64decode("aGVsbG8=")
```

Output:

```
"hello"
```

---

## 🔹 **jsonencode()**

Convert Terraform object → JSON string.

```hcl
jsonencode({
  name = "john"
  age  = 30
})
```

Output:

```
"{\"name\":\"john\",\"age\":30}"
```

---

## 🔹 **jsondecode()**

Convert JSON string → Terraform object.

```hcl
jsondecode("{\"name\":\"john\"}")
```

Output:

```
{
  name = "john"
}
```

---

# 🎯 SUMMARY TABLE

| Function Group | Common Functions                   | Purpose              |
| -------------- | ---------------------------------- | -------------------- |
| **String**     | lower, upper, replace, join, split | Manipulate text      |
| **Numeric**    | min, max, floor, ceil              | Math operations      |
| **Collection** | length, contains, keys, merge      | Work with lists/maps |
| **Encoding**   | base64encode, jsonencode           | Convert formats      |

---

