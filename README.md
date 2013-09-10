Service Stack Server-Side Validation for AngularJS 
===============================


*** Work in Progress *** Not complete, not even spell checked.

The ultimate goal of "servicestack-angular-validation" is to wire together Service Stack and Angular's validation capabilties with as little configuaration as possible while relying on both frameworks to do the heavy lifting.


When using Service Stack <a href="https://github.com/ServiceStack/ServiceStack/wiki/Validation#fluentvalidation-for-request-dtos">Fluent Validation</a>, validation errors are returned within the `Errors` array as the following examples shows.  

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

Of course, the 'FieldNames' returned by Service Stack will match the request DTO property names.

ServiceStack-Angular-Validaiton consists of a single factory for intercepting $http error responses and two directives for wiring `FieldName`s to the approprate element/ngModel.

The $http interceptor identifies $http error responses containting non-empty `Errors` array. Each object within the array are simply dispatched as events via Angular's <a href='http://code.angularjs.org/1.1.5/docs/api/ng.$rootScope.Scope#$broadcast'/>$broadcast method</a> on the <a href="http://docs.angularjs.org/api/ng.$rootScope"/>$rootScope</a>. The events' names are derived from the error's `FieldName` so creating a lister on the directive (or elsewhere) is straightfoward. 


Both directives provided by ServiceStack-Angular-Validaiton use event listeners using Angular's <a href="http://code.angularjs.org/1.1.5/docs/api/ng.$rootScope.Scope#$on"/>$on</a> method. By default, the directive will listen for the name of the element's `ng-model`. 

```html
<input type='text' ng-model='Age' s4-validate-field/>
```

However, When the ng-model does not match the request DTO properties exactly, due a nested objects within ng-repeat, a wrapper, or other mapping, a string can be passed to map the approprate DTO property.

```c#
public class UserRequest // <-- the request DTO 
{
    public User user { get; set;}
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

```c#
public class ItemsRequest // <-- the request DTO 
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


When matched, the ng-model is set to invalid (via Angular's <a href="http://docs.angularjs.org/api/ng.directive:ngModel.NgModelController#$setValidity"/> `$setValidity` method</a>. Angular will then add `ng-invalid` to the form element's class automatically. TODO... continue.





