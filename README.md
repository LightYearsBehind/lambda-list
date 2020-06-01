# λ List
Flattened Android `RecyclerView.Adapter` for speedy adaptation.

## Gradle
```gradle
implementation "com.geniewits.lambdalist:lambda-list:1.0.0"
```

## Exposed Functions/Variables
```kotlin
/*
 * Accessible by child class to allow manipulation of the items.
 */
protected val items: MutableList<T>
```

```kotlin
/*
 * Non-bounded function to get item from any position.
 *
 * Note: check boundary before calling this
 */
getItem(position): T
```

```kotlin
/*
 * Chainable mapping of item to RecyclerView.ViewHolder.
 *
 * match    return TRUE if this is the item to map to the following RecyclerView.ViewHolder.
 * create   return a new RecyclerView.Holder for the matched item.
 * bind     bind the item to the created RecyclerView.ViewHolder.
 */
map(
    match:  (item: T) -> Boolean = { true },
    create: (parent: ViewGroup) -> RecyclerView.ViewHolder,
    bind:   (holder: RecyclerView.ViewHolder, item: T) -> Unit
): LambdaAdapter<T>
```

## Custom LambdaAdapter
If required, extends `LambdaAdapter` to apply custom functions (eg. insert/delete/update).
```kotlin
class SimpleLambdaAdapter<T> : LambdaAdapter<T>() {
    fun addItem(item: T) {
        items.add(item)
        notifyItemInserted(items.size)
    }
    
    fun deleteItem(item: T) {
        val index = items.indexOf(item)
        if (index != -1) {
            items.removeAt(index)
            notifyItemRemoved(index)
        }
    }
    
    fun updateItem(oldItem: T, newItem: T) {
        val index = items.indexOf(oldItem)
        if (index != -1) {
            items.removeAt(index)
            notifyItemChanged(index)
        }
    }
}
```

## Initialization
```kotlin
val adapter = LambdaAdapter<Any>(items)
```

## Mapping
- Single view type
```kotlin
adapter.map(
    create  = { MyViewHolder(it) { onClick(it) } },
    bind    = { holder, item -> (holder as MyViewHolder).onBind(item) }
)
```

- Multiple view types
```kotlin
adapter
    .map(
        { it is A },
        { ViewHolderA(it) },
        { holder, item -> (holder as ViewHolderA).onBind(item) }
    )
    .map(
        { it is B },
        { ViewHolderB(it) },
        { holder, item -> (holder as ViewHolderB).onBind(item) }
    )
```

- Multiple view types with placeholder
```kotlin
adapter
    .map(...)
    .map(
        create  = { PlaceholderViewHolder() }
        bind    = { holder, item -> (holder as PlaceholderViewHolder).onBind(item) }
    )
```

