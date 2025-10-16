````markdown
# Kotlin and Jetpack Compose Cheat Sheet

This cheat sheet is tailored for beginners in Kotlin and Android Studio, focusing on key topics: Variables & Loops, Functions and Lambdas, Null-safety, Data Classes & Copy, and Collections. It includes key concepts with brief explanations, syntax examples, and excerpts or adaptations from sample code demonstrating Compose for UI, state management, and basic interactions.

For each topic, the following is provided:
- **Syntax Overview**: Basic rules and examples.
- **From Sample Code**: Relevant snippets from the example code.
- **Tips**: Beginner-friendly advice.

At the end, the full sample code is organized by file for easy reference.

## 1. Variables & Loops

### Syntax Overview
- **Variables**: Use `val` for immutable (read-only) and `var` for mutable.
  - Declaration: `val name: Type = value` or `var name = value` (type inference).
- **Loops**:
  - `for` loop: `for (item in collection) { ... }`
  - `while` loop: `while (condition) { ... }`
  - `do-while`: `do { ... } while (condition)`
  - Ranges: `for (i in 1..10) { ... }` or `for (i in 10 downTo 1) { ... }`

### From Sample Code
The sample code uses mutable state variables with Compose's `rememberSaveable`:
```kotlin
var name by rememberSaveable { mutableStateOf("") }  // Mutable string variable for user input
var count by rememberSaveable { mutableStateOf(0) }  // Mutable int for counter
```
No loops in the sample code, but one could be added in `CompteurSection` to display a list of counts if needed.

### Tips
- Prefer `val` over `var` to avoid bugs.
- In Compose, use `by` delegation with `mutableStateOf` for reactive UI updates.
- Loops are great for iterating over collections (see below).

## 2. Functions and Lambdas

### Syntax Overview
- **Functions**: `fun functionName(param: Type): ReturnType { ... }`
  - Single-expression: `fun add(a: Int, b: Int) = a + b`
- **Lambdas**: Anonymous functions, often used as arguments.
  - Syntax: `{ param -> body }` or `{ it * 2 }` (implicit `it` for single param).
  - Higher-order functions take lambdas, e.g., `list.map { it + 1 }`

### From Sample Code
Composable functions are regular functions with `@Composable`:
```kotlin
@Composable
fun DireBonjourSection(modifier: Modifier = Modifier) {  // Function with default param
    // ...
}
```
Lambdas in button clicks:
```kotlin
Button(
    onClick = { greetingName = name.takeIf { it.isNotBlank() } },  // Lambda for button action
    enabled = name.isNotBlank()
) {
    Text(stringResource(R.string.btn_greet))
}
```
IconButton uses lambdas too:
```kotlin
IconButton(onClick = { if (count > 0) count-- }) {  // Conditional lambda
    Icon(Icons.Filled.Remove, contentDescription = "Diminuer")
}
```

### Tips
- Composables are functions that build UI.
- Lambdas are common in event handlers like `onClick`.
- Use `it` in simple lambdas to keep code concise.

## 3. Null-Safety

### Syntax Overview
- Kotlin prevents null pointer exceptions at compile time.
  - Nullable type: `String?` (can be null).
  - Safe call: `nullableVar?.method()` (returns null if var is null).
  - Elvis operator: `val result = nullable ?: default` (use default if null).
  - Non-null assertion: `nullable!!` (risky, throws if null).
  - `let`, `also`, etc., for safe operations: `nullable?.let { ... }`

### From Sample Code
Nulls are handled elegantly:
```kotlin
var greetingName by rememberSaveable { mutableStateOf<String?>(null) }  // Nullable String
```
```kotlin
greetingName = name.takeIf { it.isNotBlank() }  // Returns null if blank
```
```kotlin
if (greetingName != null) {  // Explicit null check
    AssistChip(
        onClick = { greetingName = null },
        label = { Text("Bonjour $greetingName") }  // Safe to use since checked
    )
}
```

### Tips
- Always mark types as nullable (`?`) if they can be null.
- Use `takeIf` or `takeUnless` for conditional assignments.
- Avoid `!!` unless it's certain the value is not null.

## 4. Data Classes & Copy

### Syntax Overview
- **Data Class**: `data class Person(val name: String, val age: Int)`
  - Auto-generates `equals()`, `hashCode()`, `toString()`, `copy()`.
- **Copy**: `val newPerson = person.copy(age = 30)` (creates a copy with changes).

### From Sample Code
No data classes in the sample code, but one could be used for state, e.g.:
```kotlin
data class GreetingState(val name: String?, val count: Int)  // Hypothetical
// Usage: val state = GreetingState(null, 0)
// val newState = state.copy(name = "Alice")
```
This would be useful for expanding an app to manage more structured data.

### Tips
- Great for simple data holders like API responses or UI states.
- `copy()` is immutable-friendly; changes don't mutate the original.

## 5. Collections

### Syntax Overview
- **Lists**: `listOf(1, 2, 3)` (immutable), `mutableListOf()` (mutable).
- **Maps**: `mapOf("key" to value)`
- **Sets**: `setOf(1, 2, 3)`
- Operations: `filter`, `map`, `forEach`, etc., often with lambdas.
  - Example: `list.filter { it > 0 }.map { it * 2 }`

### From Sample Code
No collections in the sample code yet, but a list of greetings could be added:
```kotlin
val greetings = mutableListOf<String>()  // Mutable list
// Add: greetings.add("Bonjour $name")
// Display in a Column: greetings.forEach { Text(it) }
```
`Column` and `Row` in Compose act like collections of UI elements.

### Tips
- Use immutable collections unless modification is needed.
- Chain operations for concise code: `collection.filter {...}.map {...}`
- In Android, prefer `ArrayList` for mutable lists if performance matters.

## Additional Tips for Apps
- **Compose Basics**: The sample code uses `Column`, `Row`, `Modifier`, state with `rememberSaveable` – a solid foundation. Consider adding navigation or lists next.
- **Android Studio**: Use previews (`@Preview`) to test UI without running the app.
- **Practice Ideas**: Add a loop to display multiple counters, or a data class for user profiles.
- **Resources**: Check Kotlin docs or Compose tutorials for more.

## Full Sample Code Reference
Below is the sample code, organized by file/package in code blocks for easy copying.

### MainActivity.kt (package com.solicode.practice)
```kotlin
package com.solicode.practice

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import com.solicode.practice.ui.MainScreen
import com.solicode.practice.ui.theme.PracticeTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent { PracticeTheme { MainScreen() } }
    }
}

@Composable
fun App() {
    PracticeTheme {
        MainScreen()
    }
}

@Preview(showBackground = true)
@Composable
fun PreviewApp() {
    App()
}
```

### MainScreen.kt (package com.solicode.practice.ui)
```kotlin
package com.solicode.practice.ui

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun MainScreen(modifier: Modifier = Modifier) {
    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(16.dp)
            .verticalScroll(rememberScrollState()),
        verticalArrangement = Arrangement.spacedBy(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        DireBonjourSection()   // Children composables that the Column will display
        CompteurSection()
    }
}
```

### DireBonjourSection.kt (package com.solicode.practice.ui)
```kotlin
package com.solicode.practice.ui

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.material3.AssistChip
import androidx.compose.material3.Button
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.Modifier
import androidx.compose.runtime.*
import androidx.compose.ui.res.stringResource
import com.solicode.practice.R

@Composable
fun DireBonjourSection(modifier: Modifier = Modifier) {
    var name by rememberSaveable { mutableStateOf("") }
    var greetingName by rememberSaveable { mutableStateOf<String?>(null) }

    Column(modifier = modifier.fillMaxWidth()) {
        OutlinedTextField(
            value = name,
            onValueChange = { name = it },
            label = { Text(stringResource(R.string.label_firstname)) },
            placeholder = { Text(stringResource(R.string.ph_firstname)) }
        )

        Button(
            onClick = { greetingName = name.takeIf { it.isNotBlank() } },
            enabled = name.isNotBlank()
        ) {
            Text(stringResource(R.string.btn_greet))
        }

        if (greetingName != null) {
            AssistChip(
                onClick = { greetingName = null },  // Optional: Clear on click
                label = { Text("Bonjour $greetingName") }
            )
        }
    }
}
```

### CompteurSection.kt (package com.solicode.practice.ui)
```kotlin
package com.solicode.practice.ui

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.material.icons.Icons
import androidx.compose.material3.AssistChip
import androidx.compose.material3.Button
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.Modifier
import androidx.compose.runtime.*
import androidx.compose.ui.res.stringResource
import com.solicode.practice.R
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.material.icons.filled.KeyboardArrowDown
import androidx.compose.material.icons.filled.KeyboardArrowUp
import androidx.compose.material3.MaterialTheme
import androidx.compose.ui.Alignment

@Composable
fun CompteurSection(modifier: Modifier = Modifier) {
    var count by rememberSaveable { mutableStateOf(0) }

    Row (
        modifier = modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.Start
    ) {
        IconButton(onClick = { if (count > 0) count-- }) {
            Icon(Icons.Filled.Remove, contentDescription = "Diminuer")
        }
        Text(text = "$count", style = MaterialTheme.typography.headlineSmall)
        IconButton(onClick = { count++ }) {
            Icon(Icons.Filled.Add, contentDescription = "Augmenter")
        }
    }
}
```
