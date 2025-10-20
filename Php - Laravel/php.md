````markdown
# PHP Cheat Sheet

## Basics
```php
<?php
echo "Hello World";    // Output string
print "Hello World";   // Output string (slightly slower)

// Comments
// Single-line
# Another single-line
/*
Multi-line comment
*/
?>
````

---

## Variables & Data Types

```php
<?php
$var = "Hello";      // String
$number = 123;       // Integer
$float = 3.14;       // Float
$bool = true;        // Boolean
$array = [1, 2, 3];  // Indexed array
$assoc = ["key" => "value"]; // Associative array
$null = null;        // Null

// Type casting
$int = (int) "123";
$str = (string) 123;
?>
```

---

## Constants

```php
<?php
define("PI", 3.1415);
const GREETING = "Hello!";
echo PI;
?>
```

---

## Strings

```php
<?php
$str = "Hello World";
strlen($str);                  // Length
strtoupper($str);              // Uppercase
strtolower($str);              // Lowercase
substr($str, 0, 5);            // Substring
strpos($str, "World");         // Find position
str_replace("World","PHP",$str); // Replace
trim(" Hello ");               // Remove spaces
explode(" ", $str);           // Convert to array
implode("-", ["a","b","c"]);  // Convert array to string
?>
```

---

## Numbers

```php
<?php
abs(-10);          // Absolute
round(3.6);        // Round
ceil(3.2);         // Round up
floor(3.8);        // Round down
rand(1,100);       // Random number
number_format(12345.678, 2); // Format number
?>
```

---

## Arrays

```php
<?php
$fruits = ["apple", "banana", "orange"];

// Access
echo $fruits[0];

// Associative
$person = ["name"=>"John", "age"=>25];
echo $person["name"];

// Array functions
count($fruits);          
array_push($fruits,"mango"); 
array_pop($fruits);      
array_shift($fruits);    
array_unshift($fruits,"kiwi"); 
in_array("apple",$fruits); 
array_keys($person);      
array_values($person);    
array_merge($fruits, ["grape"]); 
sort($fruits);            
rsort($fruits);           
asort($person);           // Sort by value
ksort($person);           // Sort by key
?>
```

---

## Operators

```php
<?php
// Arithmetic
+, -, *, /, %, **

// Assignment
=, +=, -=, *=, /=, %=

// Comparison
==, ===, !=, !==, <, >, <=, >=

// Logical
&&, ||, !

// Increment / Decrement
++, --
?>
```

---

## Conditional Statements

```php
<?php
$age = 20;

if($age >= 18){
    echo "Adult";
} elseif($age > 12){
    echo "Teenager";
} else {
    echo "Child";
}

// Ternary
echo ($age>=18) ? "Adult" : "Not Adult";

// Null coalescing (PHP 7+)
$username = $_POST['user'] ?? 'Guest';

// Switch
$color = "red";
switch($color){
    case "red": echo "Red"; break;
    case "blue": echo "Blue"; break;
    default: echo "Other";
}
?>
```

---

## Loops

```php
<?php
// For
for($i=0; $i<5; $i++){
    echo $i;
}

// While
$i = 0;
while($i<5){
    echo $i;
    $i++;
}

// Do-While
$i = 0;
do{
    echo $i;
    $i++;
} while($i<5);

// Foreach
$fruits = ["apple", "banana"];
foreach($fruits as $fruit){
    echo $fruit;
}

// Foreach associative array
$person = ["name"=>"John", "age"=>25];
foreach($person as $key=>$value){
    echo "$key: $value";
}
?>
```

---

## Functions

```php
<?php
function add(int $a, int $b): int {
    return $a + $b;
}

// Anonymous function
$square = function($x){ return $x*$x; };

// Arrow function (PHP 7.4+)
$square2 = fn($x) => $x*$x;

// Variadic functions
function sum(...$numbers){
    return array_sum($numbers);
}

// Default arguments
function greet($name="Guest"){
    return "Hello, $name";
}
?>
```

---

## Superglobals

```php
$_GET      // URL parameters
$_POST     // Form data
$_REQUEST  // GET + POST
$_SERVER   // Server info
$_SESSION  // Session variables
$_COOKIE   // Cookies
$_FILES    // File uploads
$_ENV      // Environment variables
```

---

## Forms & Input

```php
<form method="post" action="process.php">
    <input type="text" name="username">
    <input type="submit" value="Submit">
</form>

<?php
$username = $_POST['username'] ?? '';
echo htmlspecialchars($username); // Prevent XSS
?>
```

---

## File Handling

```php
<?php
$content = file_get_contents("file.txt"); // Read
file_put_contents("file.txt", "Hello");   // Write
$file = fopen("file.txt","r");            // Open file
fread($file, filesize("file.txt"));       
fclose($file);
file_put_contents("file.txt","Hello\n",FILE_APPEND); // Append
unlink("file.txt");                        // Delete file
?>
```

---

## Sessions & Cookies

```php
<?php
session_start();
$_SESSION['user'] = "John";

setcookie("username","John",time()+3600); // 1 hour
?>
```

---

## Classes & OOP (Enhanced)

```php
<?php
// Class, Inheritance, Access Modifiers, Static, Constants, Interfaces, Traits
interface Talkable {
    public function talk(): string;
}

trait Logger {
    public function log($msg){
        echo "[LOG]: $msg";
    }
}

class Person implements Talkable {
    public $name;
    protected $age;
    private $salary;
    const SPECIES = "Homo sapiens";
    static $count = 0;

    use Logger;

    public function __construct(string $name, int $age){
        $this->name = $name;
        $this->age = $age;
        self::$count++;
    }

    public function greet(): string {
        return "Hello, ".$this->name;
    }

    public function talk(): string {
        return "I can talk";
    }

    private function secret(): string {
        return "Secret";
    }

    public static function getCount(): int {
        return self::$count;
    }

    // Magic Methods
    public function __toString(): string {
        return $this->name;
    }

    public function __destruct(){
        // Cleanup
    }
}

// Inheritance
class Employee extends Person {
    public $position;

    public function __construct($name, $age, $position){
        parent::__construct($name, $age);
        $this->position = $position;
    }

    public function greet(): string {
        return "Hi, I'm $this->name, working as $this->position";
    }
}

$emp = new Employee("John", 30, "Developer");
echo $emp->greet();
echo Employee::getCount();
?>
```

---

## Error Handling

```php
<?php
try{
    if(!file_exists("file.txt")){
        throw new Exception("File not found");
    }
} catch(Exception $e){
    echo $e->getMessage();
} finally {
    echo "Always runs";
}

// Error reporting
error_reporting(E_ALL);
ini_set('display_errors', 1);
?>
```

---

## Useful Functions

```php
isset($var)      // Check if set
empty($var)      // Check if empty
is_array($var)   
is_string($var)
is_int($var)
var_dump($var)   // Print info
print_r($var)    // Print arrays
json_encode($arr)
json_decode($json,true)
header("Location: index.php") // Redirect
die("Stop script");
?>
```

---

## Database (MySQLi)

```php
<?php
$conn = new mysqli("localhost","user","pass","db");
if($conn->connect_error){
    die("Connection failed: ".$conn->connect_error);
}

$sql = "SELECT * FROM users";
$result = $conn->query($sql);

if($result->num_rows > 0){
    while($row = $result->fetch_assoc()){
        echo $row['username'];
    }
}

$conn->close();
?>
```

---

## Namespaces (PHP 5.3+)

```php
<?php
namespace MyApp\Models;

class User {
    public function info(){
        return "User info";
    }
}

// Usage
use MyApp\Models\User;
$user = new User();
echo $user->info();
?>
```
