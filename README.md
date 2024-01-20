Stream (Best performance):
```kotlin
val largeData: Parcelable = //...
// or
val largeData: Serializable = //...
 
// send    
Intent(context, SomeActivity::class.java).apply {
    putExtra(LARGE_DATA_SLICE, largeData.parcelableSlice())
}

// receive
intent.getParcelableExtra<ParcelableInputStream<Parcelable>>(LARGE_DATA_SLICE)?.read()
```

Slice (Worst performance):
```kotlin
val largeData: Parcelable = //...
// or
val largeData: Serializable = //...
 
// send    
Intent(context, SomeActivity::class.java).apply {
    putExtra(LARGE_DATA_STREAM, largeData.parcelableInputStream())
}

// receive
intent.getParcelableExtra<ParcelableSlice<Parcelable>>(LARGE_DATA_STREAM)?.join()
```

Pipe (Most unstable):
```kotlin
val largeData: Parcelable = //...
// or
val largeData: Serializable = //...

val (reader, writer) = ParcelableStreamPipe<Parcelable>()

// send
Thread {
  writer.write(largeData)
}.start()
    
Intent(context, SomeActivity::class.java).apply {
    putExtra(LARGE_DATA_PIPE, reader)
}

// receive
Thread {
  intent.getParcelableExtra<ParcelableInputStream<Parcelable>>(LARGE_DATA_PIPE)?.read()
}.start()
```