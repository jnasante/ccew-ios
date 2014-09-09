Introduction to the iOS Filesystem
=================================

In the next chapter we'll look at using sqlite and modeling our data with a real database. In preparation for that chapter we should familiarize ourselves with the iOS filesystem.

## The Sandbox

Most importantly, the iOS Sandboxes every application. This means that every application runs in its own protected memory and file system environment and cannot access the memory or file system space of other applications except through well defined APIs provided by the iOS.

When an application is first loaded onto an iPhone, a data directory is created for it. This is the application’s personal workspace on the file system, or sandbox. Any data the application persists is automatically stored in this isolated space. If the user deletes the application at some point in the future, all the data associated with the application is deleted as well.

## The Application Bundle

It is also important to know that the application is itself protected from modification.  An application is actually just a directory with a number of files in it: compiled code, resources, storyboard files and so on. Although a running application has read access to this data, it does not have write access. An application cannot modify itself.

Read access is nevertheless significant. We can store resources in the application bundle such as a database file or other data files and access them through system APIs. We'll see that adding a *resource* to an application bundle is as simple as adding a file to our Xcode project.

The sandbox and application bundle are often used in conjunction. The application bundel may contain a default database with a few populated elements. Ultimately the user will need to modify that database, so when the application first launches it copies the database from its bundle to the sandboxed filesystem. Changes are then made to the database in the sandbox.

## Using the Application Bundle

Let's modify the table application we've begun to load an array of strings from the application bundle instead harcoding an empty array into the view controller. We'll load the strings from an xml like file that's easy to create and modify in Xcode.

Add a new file to your project. This time choose *Property List* from the *iOS Resource* section and explicitly select it from the Project Navigator. Click it again to rename it. Name it "DefaultValues.plist".

A property list is a serialized representation of a `NSDictionary` or `NSArray`. There is a root object that represents one or the other and then content in the object. Let's change the root object *Type* to `NSArray`:

[[ image: property list type ]]

Then select that row can click the plus button to add new items to the array. The type should be *String* by default, which is what we want, so you'll just need to double click in the *Value* cell to set the strings. Type in the same strings used when we hardcoded the array:

[[ image: completed property list ]]

**NSBundle**

By default the property list is added to the application bundle. We can access it with methods on the `NSBundle` class. See the documentation for a detailed description of this class. Its primary purpose is to provide access to resources in an application bundle.

Accessing those resources is a two step process. First we should identify the bundle we want to access. We'll be using our own bundle, or the *main bundle*. Second we should identify the resource we want by name and extension, or type. The API will give us back an `NSString` path identifying the location of that resource on the filesystem.

Modify the table view controller's `viewDidLoad` method:

```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
  
    NSString *path = [[NSBundle mainBundle] pathForResource:@"DefaultValues" ofType:@"plist"];
  
    self.items = @[ @"Mark", @"Vanessa", @"James", @"Bart", @"Cathy" ];
  
  	// ...
}
```

`NSArray` then provides a class method for initializing an array from a propertly list file at a given path. It returns an instance of `NSArray` which we can set directly to `self.items`:

```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
  
    NSString *path = [[NSBundle mainBundle] pathForResource:@"DefaultValues" ofType:@"plist"];
    self.items = [NSArray arrayWithContentsOfFile:path];
    
    // ...
}
```

And that's it. You're now accessing resources within the application's bundle. Moreover you can modify the propertly list and when your application is recompiled the changes will be incorporated in the new bundle.

...

## Using the Sandbox

...
