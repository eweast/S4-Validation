Service Stack Server-Side Validation for AngularJS (S4Validation)
===============================


*** Work in Progress *** Not complete, not even spell checked.


When using Service Stack <a href="https://github.com/ServiceStack/ServiceStack/wiki/Validation#fluentvalidation-for-request-dtos">Fluent Validation</a>, validation errors for request DTO are returned as in the following JSON example.  

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

This module provides the 's4-validate-field' directive to match `FieldName` with the approprate form element and its corrosponding ng-model. By default, the directive will look for a `FieldName` with the same name as the ng-model. 

```html
<input type='text' ng-model='Age' s4-validate-field/>
```

However, When the ng-model does not match the request DTO properties exactly, due a nested objects within ng-repate, wrapper, or other mapping, a string can be passed to `s4-validate-field` to map the approprate DTO property.

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





