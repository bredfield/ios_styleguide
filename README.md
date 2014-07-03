iOS Styleguide
==============

Objective-C style guide, best practices, and project structure. For now, it's going to be a knowledge dump with no particular structure. Deal with it.

##MVC
###Overview
Apple doesn't provide a great base for an logical, model-view-controller organized code base. Typical iOS projects consist of huge view controllers, which house view controller, view, and data logic. Implementing a strict MVC structure has to be slightly forced, but ultimately provides tremendous benefits (organization, reusability/modularity, testability) to a project.

###Models
A model is essentially an interface around a data structure.  

###Views

###Controllers

###Rules



##Protocols & Delegates
###Overview
The delegate pattern is the primary method of communication going up a view / controller / model stack. It relieves the need for notifications (which do have their place), and provides a clear structure for module communication. 

###Rules
- Bubble up. 
  - Protocols should be defined on the object that others are subscribing to. 
  - For example, to communicate from a view (child) to its view controller (parent), the protocol should be defined on the view. 
  - Also works with child -> parent view controllers, as well as models.
- Prefix delegate methods w/ name
  - For example, `- (void)someViewDidDoXAction`.
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


