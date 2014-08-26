NSFoundation: Basic Objects
=======================================

Covering core objective-c objects used in iOS applications.

## NSString (NSMutableString)

**String literals**

Use of the `@` symbol followed by string in double quotes:

```objective-c
@"string literal"
```

They are no different than objects. Use them for variable assignment or use them directly with other objects.

We cannot use indexing operators such as `[]` to get characters in a string. Instead we must use methods on `NSString`.

**Key methods**

```objective-c
- (unichar)characterAtIndex:(NSUInteger)index;
- (NSUInteger)length;
```

**Example methods**

```objective-v
- (NSString *)lowercaseString
- (NSString *)description
```

**NSLog**

The logging facility, which we’ll get into more below. We discuss it here because logging involves string. Log a string. Note the compiler complaint. The compiler wants a formatted string.

```objective-c
+ (id)stringWithFormat:(NSString *)format, ...
```

**String formatting identifiers**

As with `printf` from C based programming languages.

- `%@` for objects 
- `%f` for CGFloat 
- `%d` or `%ld` for NSInteger
- `%u` or `%lu` for NSUInteger
- `%C` for unichar

String formatting: if Xcode complains, allow it to make the recommended fix

**Testing object equality**

There is no operator overloading in obj-c. Using the equals operator (`==`) will check the memory addresses and not the strings themselves. You must use the various `isEqualTo:` methods to check object equality in obj-c.

```objective-c
- (BOOL)isEqualToString:(NSString *)aString
```

**Combining strings**

Because there is no operator overloading in obj-c methods must be used for basic string operations such as concatenation.

```objective-c
- (NSString *)stringByAppendingString:(NSString *)aString
- (NSString *)stringByAppendingFormat:(NSString *)format ...
```

Examples

...

**Working with paths**

...

```objective-c
- (NSString *)stringByAppendingPathComponent:(NSString *)aString
- (NSString *)lastPathComponent
```

Examples

...

**Converting a string to a number primitive**

...

```objective-c
- (NSInteger)integerValue
```

**Substrings**

```objective-c
- (NSString *)substringWithRange:(NSRange)aRange
- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask
```

**Mutable strings**

Rarely used. Might be useful if you’re in a tight loop where you’re modifying a single string many times before returning it for further use.

Be aware of object copying rules. Copying an object always produces an immutable copy. When you copy an instance of NSMutableString, you get an NSString back.

This is the case with all mutable classes. Copying any mutable class produces an immutable copy of the original. This is important when we deal with collection classes later.

It is important for those memory hints we discussed earlier. This will crash your app:

```objective-c
// header
@property (copy, nonatomic) NSMutableString *string;

// implementation
self.string = [NSMutableString stringWithString:@"Some Text"];
[self.string appendString:@"More Text"];
```

with the following message:

Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'Attempt to mutate immutable object with appendString:

Why?

**Localizable strings**

You should not hardcode strings that will be presented to the user. In the future your application may be translated into other languages. We need an easy way to ensure that user facing strings can be translated.

Use the function `NSLocalizedString(key, comment)` and hardcode your string as the key. Later on you can then provide localized versions of the *Localizable.strings* file in your project and the system will automatically use the translated version of the string in place of the original.

## NSNumber

In addition to numeric primitives such as `float`, `int`, and `double` and their foundation counterparts such as `NSInteger` and `CGFloat`, obj-c provides a number class for encapsulating numeric primitives in object instances.

Why would you want to do this ... ?

**Numeric literals**

What is the difference between `3.14` and `@3.14`? Normally you’ll use the numeric primitives. They are easier to work with, for example, testing equality. However, storing numeric data is simpler with objects as there are built-in means of persisting objects, and you will use objects exclusively with the collection classes.

If you examine the class documentation you’ll notice that most `NSNumber` methods deal with wrapping primitive data types and making them accessible again, eg:

```objective-c
 NSNumber *number = [NSNumber numberWithInteger:42];
 NSInteger integer = [number integerValue];
 ```
 
 Note the `isEqualTo:` variant for `NSNumber` objects:
 
 ```objective-c
 - (BOOL)isEqualToNumber:(NSNumber *)aNumber
 ```

Just like it is generally easy to convert a string to a number using the `NSString` methods or string formatting, it is also easy to convert an `NSNumber` to a string:

```objective-c
- (NSString *)stringValue
```

Go over the difference between using a primitive property and an object property. Note the absence of the pointer symbol * and the missing memory management hint.

```objective-c
@property (strong, nonatomic) NSNumber * numberObject;
@property (nonatomic) NSInteger numberPrimitive;
```

## NSDate (NSTimeInterval, NSDateComponents)

`NSDate` provides a simple API for getting dates and for getting dates in the future:

```objective-c
NSDate *today = [NSDate date];
```

Other methods:

```objective-c
+ (id)dateWithTimeIntervalSinceNow:(NSTimeInterval)seconds;
+ 
```

Time intervals are specified in seconds, and `NSTimeInterval` is type defined to `double`, so you can use decimals for milli- and microseconds.


Dates are timezone agnostic. Log the date. To display a date to a user generally you’ll use `NSDateFormatter` and `NSTimezone` to put it in the appropriate format. To get the components from a date such as the day, month, minute and hour you’ll use the `NSDateComponents` class. 

We won’t cover these classes here, but be aware of them.

## NSURL

Encapsulates URL information including internet addresses and local file paths. Generally you’ll create a URL object with a string literal, or you’ll get in from another method.

```objective-c
+ (id)URLWithString:(NSString *)URLString
```

You’ll use URLs as building blocks for other operations, for example, those involing `NSURLRequest`. Go over the documentation to see the methods.

As for `NSURL` itself you’ll rarely need to do more than get the complete string for the URL or get file path information:

```objective-c
- (NSString *)absoluteString
- (BOOL)isFileURL
- (NSString *)path
```

<!-- IN CLASS EXERCISES -->
<!-- Add some properties for these objects, use them in actions and in our custom classes. -->

<hr>

NSFoundation: Collections
=======================================

...

## NSArray (NSMutableArray)

An array is an ordered collection of objects and is represented by the NSArray class in Obj-C. Unlike Java, an array in Obj-C is not limited to a collection of similar objects, such as an array of strings or an array of numbers. In Obj-C an array is a collection of any type of object and can include a mix of numbers, strings, other array, dictionaries, or any other class, including classes of your own creation.

**Building arrays**

There are two ways to create arrays. You can use a class or instance methods, or you can use a literal notation like we’ve seen with strings.

In the past methods were always used, and you will still encounter code that uses these methods:

```objective-c
+ (id)arrayWithObject:(id)anObject
+ (id)arrayWithObjects:(id)firstObj, ...

- (id)initWithObjects:(id)firstObj, ...
```

For example,

```objective-c
NSArray *array = [NSArray arrayWithObjects:aDate, aValue, aString, nil];
NSArray *array = [[NSArray alloc] initWithObjects:aDate, aValue, aString, nil];
```

**Nil**

Notice how the list is nil terminated. `nil` with a lowercase 'n' is like `NULL` and is a primitive value in Obj-C. It evaluates to false. Unlike many programming languages, it is ok to send messages to `nil`, and in fact this is idiomatic. That means you don’t necessarily need to check the return value of a method that might return a `nil` value before calling a method on it. Of course, you might still need to check for `nil` anyway as part of the application logic.

Also notice that are arrays are not statically typed to a single kind of object, as in Java. Arrays may contain any blend of objects in Objective-C.

**Array literals**

The newer array literals syntax allows you to create arrays more simply. Use the `@` symbol as you do for string literals and bracket the objects in the array:

```objective-c
NSArray *array = @[aDate, aValue, aString];
```

Note that it is not necessary to `nil` terminate an array literal, and in fact doing so will prevent your application from compiling. 

Also note that `nil` and other primitive values cannot appear in an array. `nil` is a primitive, not an object, and arrays can only contain objects. Attempting to populate an array with `nil` will result in an exception being thrown and your application crashing.

You can embed other literals inside the array literal, such as numeric and string literals, to quickly build arrays out of basic objects:

```objective-c
NSArray *array = @[ [NSDate date], @3.14, @"A string"];
```

Whereas in the past, in order to do this you would have had to write:

```objective-c
NSArray *array = [NSArray arrayWithObjects:[NSDate date], [NSNumber numberWithDouble:3.14], @"A string", nil];
```

The recommended practice is to now use array literals when creating arrays, although you will still see the older method based code.

**Accessing elements**

As with array creation you may access arrays with methods or with a standard subscripting syntax. Modern objective-c prefers subscripting:

```objective-c
id anObject = array[0];
```

The common array methods, including accessing an element at an index, are:

```objective-c
- (NSUInteger)count
- (id)objectAtIndex:(NSUInteger)index
```

When you have an object and want information about its position in the array, use:

```objective-c
- (BOOL)containsObject:(id)anObject
- (NSUInteger)indexOfObject:(id)anObject
```

There are a number of variations on these methods allowing you to restrict the range you’d like to examine and so on. Refer to the documentation.

**Array enumeration**

There are three ways to enumerate an array in Obj-C. You may use the classic `for` loop or you may use what is called fast enumeration with a `for ... in` loop. A third approach uses blocks, an advanced topic we will address in later chapters.

To enumerate an array with a `for` loop:

```objective-c
for ( NSInteger i = 0; i < [array count]; i++ ) {
    NSString *aString = array[i];
    // use aString
}
```

To enumerator an array with a `for ... in` loop:

```objective-c
for ( NSString *aString in myArray ) {
    // use aString
}
```

The first approach is clearly more verbose than the second. In general you will use fast enumeration with a `for ... in` loop but sometimes you may need to track the index for other reasons, so be familiar with both.

**Mutable arrays**

Arrays are not mutable by default. Once you create an array you cannot add or remove objects from it. If you want to work with a mutable array you must use the `NSMutableArray` class instead.

You create a mutable array using the class methods or two stage creation:

```objective-c
NSMutableArray *myArray = [NSMutableArray arrayWithObjects:aDate, aValue, aString, nil];

NSMutableArray *myArray = [[NSMutableArray alloc] initWithObjects:aDate, aValue, aString, nil];
```

Notice that the methods are the same as though used for `NSArray`; only the class has changed. `NSMutableArray` subclasses `NSArray` and overrides these methods. In fact anything you can do with an immutable array you can also do with a mutable array.

Because the mutable array allows for mutation, you can add, insert, remove and replace objects:

```objective-c
- (void)addObject:(id)anObject
- (void)insertObject:(id)anObject atIndex:(NSUInteger)index
- (void)replaceObjectAtIndex:(NSUInteger)index withObject:(id)anObject
- (void)removeObjectAtIndex:(NSUInteger)index
```

There are a number of variations on these methods. Refer to the documentation.

## NSDictionary (NSMutableDictionary)

Cocoa supports hashes through `NSDictionary`. A hash is a collection of keys and values, with each key mapped to a single value. Keys in a dictionary must be unique: you can only have one copy of a specific key at a time. But values can appear as many times as you like.

Question: under what conditions might this occur?

**Creating dictionaries**


As with arrays, dictionaries can contain any kind of key and any kind of value, and the types can be mixed. With a caveat, keys must be capable of being copied. Basic objects such as strings, numbers, dates and so on can all be copied, but interface objects such as views, buttons and labels cannot be copied. In general, you will use strings as keys.

As with strings, we now have dictionary literals which look a lot like hashes in ruby or python. But again, you will encounter a lot of old code which builds dictionaries using the class or instance methods, so you should be familiar with these. There are a number of ways to do this, but my preferred method is:

```objective-c
+ (id)dictionaryWithObjectsAndKeys:(id)firstObject , ...
```

For example:

```
NSDictionary *dict = [NSDictionary dictionaryWithObjectsAndKeys:@"value1", @"key1", @"value2", @"key2", nil];
```

Each entry in the dictionary is an value-key pair. So that `key1` points to `value1` and `key2` points to `value2`. Make sure you don’t confuse the order!

**Dictionary literals**

The new dictionary literals are similar to array literals and look very much like ruby or python hashes/dictionaries. Again, use the @ symbol and this time surround your key value pairs with curly braces. Key-value pairs are separated by commas and are distinguished using the colon:

```objective-c
NSDictionary *dictionary = @{ @"key1" : @"value1", @"key2" : @"value2" };
```

Because dictionaries can contain any kind of value, including strings, numbers, array, and even other dictionaries, dictionary literals can become quite complex. Break the keys and values by line -- Obj-C doesn’t care, and it starts to look a lot like JSON, or the javascript object notation language:

```objective-c
NSDictionary *dictionary = @{
	@"key1" : @"value1",
	@"key2" : @"value2",
	@"key3" : @3.14,
	@"key4" : @[ @"this", @"is", @"an", @"array", @"of", @"string" ],
	@"key5" : @{
		@"another" : @"dictionary",
		@"embedded" : @"in",
		@"the" : @"first"
	}
};
```

**Accessing elements**

Once you’ve created a dictionary you can pull objects out of it with methods or using a named subscript syntax. The methods are:

```objective-c
- (NSUInteger)count
- (id)objectForKey:(id)aKey
```

For example, this method returns the `@"value2"` string:

```objective-c
NSString *value = [dict objectForKey:@"key2"];
```

Dictionaries also support the subscripting syntax for accessing elements:

```objective-c
NSString *value = dict[@"key2"];
```

**Dictionary enumeration**

You can pull out all the keys or values for a dictionary with:

```objective-c
- (NSArray *)allKeys
- (NSArray *)allValues
```

Generally you won’t need to do this. Dictionary enumeration, on the other hand, is quite common, and there is a fast enumeration equivalent for dictionaries:

```objective-c
NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:@"value1", @"key1", @"value2", @"key2", nil];
    
for ( id aKey in dictionary ){
    id aValue = [dictionary objectForKey:aKey];
    // use the key and value
}
```

Dictionary enumeration iterates through the dictionary’s keys, which you can then use to get the values. This is equivalent to doing:

```objective-c
for ( id aKey in [dictionary allKeys] ) { ...
```

**Mutable dictionaries**

Like arrays, dictionaries are by default immutable. If you want a dictionary that you can add and remove key-value pairs from, create a mutable dictionary using the alloc-init pair or using a convenience method:

```objective-c
NSMutableDictionary *mutableDictionary = [[NSMutableDictionary alloc] init];
NSMutableDictionary *mutableDictionary = [NSMutableDictionary dictionary];
```

You alter a mutable dictionary using the following methods:

```objective-c
- (void)setObject:(id)anObject forKey:(id < NSCopying >)aKey
- (void)removeObjectsForKeys:(NSArray *)keyArray
```

Notice the use of id here and the `NSCopying` protocol. A key can be any object type as long as it conforms to the `NSCopying` protocol, which is another way saying, as long as it can be copied. Notably, interface elements do not conform to this protocol and so cannot be used as keys in dictionaries.