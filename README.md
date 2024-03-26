Now in Android App
==================
# Before you begin
**[Jetpack Compose](https://developer.android.com/jetpack/compose) is a modern toolkit designed to simplify UI development. It combines a reactive programming model with the conciseness and ease of use of the Kotlin programming language. It is fully declarative, meaning you describe your UI by calling a series of functions that transform data into a UI hierarchy. When the underlying data changes, the framework automatically re-executes these functions, updating the UI hierarchy for you.**
## What we'll do
- What Compose is
- How to build UIs with Compose
- How to manage state in composable functions
- How to create a performant list
- How to add animations
- How to style and theme an app
  
You'll build an app with an onboarding screen, and a list of animated expanding items:

## Screenshots
![phone](https://github.com/oybekjon94/ComposeBasics/assets/91370134/37e5ba1c-f428-409f-97bf-f938fc74f050)
## Composable functions
**A composable function** is a regular function annotated with `@Composable`. This enables your function to call other `@Composable` functions within it. You can see how the `Greeting` function is marked as `@Composable`. This function will produce a piece of UI hierarchy displaying the given input, `String`. `Text` is a composable function provided by the library.

```
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(
            text = "Hello $name!",
            modifier = modifier
        )
    }
}
```

** Modifiers
Most Compose UI elements such as `Surface` and `Text` accept an optional `modifier` parameter. Modifiers tell a UI element how to lay out, display, or behave within its parent layout. You may have already noticed that the `Greeting` composable already has a default modifier, which is then passed to the `Text`.

For example, the `padding` modifier will apply an amount of space around the element it decorates. You can create a padding modifier with `Modifier.padding()`. You can also add multiple modifiers by chaining them, so in our case we can add the padding modifier to the default one: `modifier.padding(24.dp)`.

```
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(
            text = "Hello $name!",
            modifier = modifier.padding(24.dp)
        )
    }
}
```
![image](https://github.com/oybekjon94/ComposeBasics/assets/91370134/fe75236b-bda4-400d-85f1-6163f2c928f1)

## Creating columns and rows
The three basic standard layout elements in Compose are Column, Row and Box.

![image](https://github.com/oybekjon94/ComposeBasics/assets/91370134/fec669e3-45c8-4967-ba66-28e2dbc7fc6b)

They are Composable functions that take Composable content, so you can place items inside. For example, each child inside of a `Column` will be placed vertically.

Now try to change Greeting so that it shows a column with two text elements like in this example:

```
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Column(modifier = modifier.padding(24.dp)) {
            Text(text = "Hello ")
            Text(text = name)
        }
    }
}
```

![image](https://github.com/oybekjon94/ComposeBasics/assets/91370134/5258e999-10a8-41c5-a2d9-da9fa114cdb7)

## Adding a button
![image](https://github.com/oybekjon94/ComposeBasics/assets/91370134/c0dd0db8-86cf-467c-a422-41846d2443c5)

`Button` is a composable provided by the material3 package which takes a composable as the last argument. Since trailing lambdas can be moved outside of the parentheses, you can add any content to the button as a child. For example, a `Text`:
```
Button(
    onClick = { } // You'll learn about this callback later
) {
    Text("Show less")
}
```

> [!Note]
>  Compose provides different types of `Button` according to the [Material Design](https://m3.material.io/components/buttons/implementation/android) Buttons spec —`Button`, `ElevatedButton`, `FilledTonalButton`, `OutlinedButton`, and `TextButton`. In your case, you'll use an `ElevatedButton` that wraps a `Text` as the `ElevatedButton` content.

## Creating a performant lazy list
Change the default list value in the `Greetings` parameters to use another list constructor which allows to set the list size and fill it with the value contained in `its` lambda (here $it represents the list index):
```
names: List<String> = List(1000) { "$it" }
```
This creates 1000 greetings, even the ones that don't fit in the screen. Obviously this is not performant. You can try to run it on an emulator (warning: this code might freeze your emulator).

To display a scrollable column we use a `LazyColumn`. `LazyColumn` renders only the visible items on screen, allowing performance gains when rendering a big list.

> [!Note]
> LazyColumn and LazyRow are equivalent to RecyclerView in Android Views.

```
@Composable
private fun Greetings(
    modifier: Modifier = Modifier,
    names: List<String> = List(1000) { "$it" } 
) {
    LazyColumn(modifier = modifier.padding(vertical = 4.dp)) {
        items(items = names) { name ->
            Greeting(name = name)
        }
    }
}
```

> [!Note]
> LazyColumn doesn't recycle its children like RecyclerView. It emits new Composables as you scroll through it and is still performant, as emitting Composables is relatively cheap compared to instantiating Android Views.

## Persisting state
Our app has two problems:

Persisting the onboarding screen state
If you run the app on a device, click on the buttons and then you rotate, the onboarding screen is shown again. The remember function works only as long as the composable is kept in the Composition. When you rotate, the whole activity is restarted so all state is lost. This also happens with any configuration change and on process death.

Instead of using `remember` you can use `rememberSaveable`. This will save each state surviving configuration changes (such as rotations) and process death.

Now replace the use of `remember` in `shouldShowOnboarding` with `rememberSaveable`:
```
 import androidx.compose.runtime.saveable.rememberSaveable
    // ...

    var shouldShowOnboarding by rememberSaveable { mutableStateOf(true) }
```
