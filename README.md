Service Stack Server-Side Validation for AngularJS (S4-Validation)
===============================


*** Work in Progress ***

The ultimate goal of S4 Validation is to wire together Service Stack and Angular's validation capabilties with as little configuaration as possible while relying on both frameworks to do the heavy lifting.



## How to Use










## How It Works


When using Service Stack with <a href="https://github.com/ServiceStack/ServiceStack/wiki/Validation#fluentvalidation-for-request-dtos">Fluent Validation</a>, validation errors are returned within the `Errors` array as the following examples shows.  

```javascript
// Source: https://github.com/ServiceStack/ServiceStack/wiki/Validation#fluentvalidation-for-request-dtos
{
    "ErrorCode": "GreaterThan",
    "Message": "'Age' must be greater than '0'.",
    "Errors": [
        {
            "ErrorCode": "GreaterThan",
            "FieldName": "Age",
            "Message": "'Age' must be greater than '0'."
        },
        {
            "ErrorCode": "NotEmpty",
            "FieldName": "Company",
            "Message": "'Company' should not be empty."
        }
    ]
}
```

Of course, the `FieldName`s returned by Service Stack will match the request DTO property names.

S4-Validation consists of a single factory for intercepting $http error responses and two directives for wiring `FieldName`s to the approprate element/ngModel.

The $http interceptor identifies $http error responses containting a non-empty `Errors` array. The interceptor dispatches and event for each object within the array via Angular's <a href='http://code.angularjs.org/1.1.5/docs/api/ng.$rootScope.Scope#$broadcast'>$broadcast method</a> on the <a href="http://docs.angularjs.org/api/ng.$rootScope">$rootScope</a>. The event contains the `ErrorCode` and `Message` as arguements. The events' names are derived from the error's `FieldName` ("ValidationError. + `FieldName`") so creating a listener for the event on the directive (or elsewhere) is straightfoward. 


Both directives provided by S4-Validation use event listeners using Angular's <a href="http://code.angularjs.org/1.1.5/docs/api/ng.$rootScope.Scope#$on">$on</a> method. By default, the directive will listen for the name of the element's `ng-model`. 

Example
```html
<input type='text' ng-model='Age' s4-validate-field/>
```

However, when the ng-model name does not match the request DTO properties exactly a string representing the DTO property name can be passed to the directive.

Example 1
```c#
public class InsertUserRequest // <-- the request DTO 
{
    public User user { get; set;}
    public string foo { get; set; }
    public string bar { get; set; }
}

public class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```
```html
<input type='text' ng-model='Name' s4-validate-field='user.Name'/>
<input type='text' ng-model='Age' s4-validate-field='user.Age'/>
```

Example 2
```c#
public class InsertItemsRequest // <-- the request DTO 
{
    public List<Item> Items { get; set;}
}

public class Items
{
    public string PropertyA { get; set; }
    public string PropertyB { get; set; }
}

```
```html
<div ng-repeat='item in items'>
  <input type='text' ng-model='item.PropertyA' s4-validate-field='Items[{{$index}}].PropertyA'/>
  <input type='text' ng-model='item.PropertyB' s4-validate-field='Items[{{$index}}].PropertyB'/>
</div>
```

Once the directive catches the validation event, the corrosponding `ng-model` is set to invalid via Angular's <a href="http://docs.angularjs.org/api/ng.directive:ngModel.NgModelController#$setValidity">`$setValidity` method</a>. Angular will then add `ng-invalid` to the form element's class automatically.
```css
input.ng-invalid,
textarea.ng-invalid,
select.ng-invalid {
    border-color: #ee5f5b;
    border-width: 2px;
}
```

In addition, the directive will add the user friendly error `message` to the element's <a href="http://angular-ui.github.io/bootstrap/#/tooltip">UI-Bootstrap tooltip</a> if the tooltip directive is present. Obviously, <a href="http://angular-ui.github.io/bootstrap/">UI-Bootstrap</a> is required. 
```html
  <input type='text' ng-model='propA' s4-validate-field tooltip/>
```

The `ng-model` is set back to valid once the user makes a change to that model. In addition, the error message displayed by the tooltip is removed once the `ng-model` is changed and its related element loses the focus. This should prevent the user from becoming confused by error indicators and error messages that would otherwise not go away until the form is re-submitted.

Example of result: http://jsfiddle.net/ACehg/1

## Stuff Mentioned That I Do Not Own

AngularJS by Google @ http://angularjs.org/
Service Stack by Demis Bellot @ http://www.servicestack.net/ AND https://github.com/ServiceStack/ServiceStack
Fluent Validation by Jeremy Skinner @ http://fluentvalidation.codeplex.com/ AND http://github.com/JeremySkinner/FluentValidation
UI Bootstrap by the Angular-UI Team @ http://angular-ui.github.io/bootstrap/ AND http://github.com/angular-ui/bootstrap



