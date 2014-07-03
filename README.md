iOS Styleguide
==============

Objective-C style guide, best practices, and project structure. For now, it's going to be a knowledge dump.

--------------

- [Objective-C Style Guide](styleguide.md)
- [Stack](stack.md)
- [Project Structure](structure.md)
 


##Protocols & Delegates
###Overview
The delegate pattern is the primary method of communication going up a view / controller / model stack. It relieves the need for notifications (which do have their place), and provides a clear structure for module communication. 

###Rules
- Bubble up. 
  - Protocols should be defined on the object that others are subscribing to. 
  - For example, to communicate from a view (child) to its view controller (parent), the protocol should be defined on the view. 
  - Also works with child / parent view controllers, as well as model protocols.
- Prefix delegate methods w/ name
  - For example, `- (void)aboutViewDidSelectXButton`.
  - Gets verbose, but keeps things readable and tracable.

###Example
In this example, the GreatView needs to alert its parent view controller (GreatViewController) of changes in a search bar.

In GreatView.h, define the protocol:
```
@protocol GreatViewDelegate <NSObject>

- (void)greatViewFilterSearchBarDidBeginEditing:(BOOL)editing;
- (void)greatViewFilterSearchBarDidChangeText:(NSString*)text;

@end

@interface GreatView : UIView

@property (strong, nonatomic) id<GreatViewDelegate> delegate;

@end
```

In GreatView.m, send messages to the GreatViewDelegate:
```
- (BOOL)searchBarShouldBeginEditing:(UISearchBar *)searchBar
{
    [self.delegate greatViewLocationsFilterSearchBarDidBeginEditing:YES];
    return YES;
}

- (BOOL)searchBarShouldEndEditing:(UISearchBar *)searchBar
{
    [self.delegate greatViewLocationsFilterSearchBarDidBeginEditing:self.isSearching];
    return YES;
}

- (void)searchBar:(UISearchBar *)searchBar textDidChange:(NSString *)searchText
{
    [self.delegate greatViewLocationsFilterSearchBarDidChangeText:searchText];
}

```

In GreatViewController.m, subscribe to the delegate, then act upon the delegate methods
```
@interface GreatViewController () <GreatViewDelegate>
@end

@implementation GreatViewController

- (void)loadView
{
    self.view = [[GreatView alloc] initWithFrame:[[UIScreen mainScreen] applicationFrame]];
    self.view.delegate = self;
}

...

#pragma mark - Great View Delegate
- (void)greatViewFilterSearchBarDidBeginEditing:(BOOL)editing
{
    //do stuff
}

- (void)greatViewFilterSearchBarDidChangeText:(NSString *)text
{
    //do more stuff
}

```


