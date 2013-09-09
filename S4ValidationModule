angular.module('S4ValidationModule', [])
    .factory('s4HttpInterceptor', function ($q, $rootScope) {
        return function (promise) {
            return promise.then(
                function (response) {
                    // on success
                    return response;
                },
                function (response) {


                    response.ResponseStatus = {};
                    response.ResponseStatus.Errors = [];

                    response.ResponseStatus.Errors.push({FieldName: 'model.NestedModels[0].propA', ErrorCode: 'myCode1', Message: 'not working' });

                    if (response.hasOwnProperty('ResponseStatus') &&
                        response['ResponseStatus'].hasOwnProperty('Errors') &&
                        response['ResponseStatus']['Errors'].length > 0) {

                        var ValidationErrors = response['ResponseStatus']['Errors'];
                        var NumberOfValidationErrors = ValidationErrors.length;

                        for (var i = 0; NumberOfValidationErrors > i; i++) {
                            var validationError = ValidationErrors[i]
                            $rootScope.$broadcast('ValidationError.' + validationError.FieldName,
                                validationError.ErrorCode, validationError.Message);
                        }
                    }
                    return $q.reject(response);
                }
            );
        }
    })
    .directive('s4ValidateField', function () {
        return {
            restrict: 'A',
            require: 'ngModel',
            link: function (scope, el, attr, ngModel) {

                var modelName = (attr['s4ValidateField']) ? attr['s4ValidateField'] : attr.ngModel;
                var hasToolTip = attr.hasOwnProperty('tooltip');

                scope.$on('ValidationError.' + modelName, function (event, errorCode, message) {
                    ngModel.$setValidity(errorCode, false);

                    if (hasToolTip)
                        attr.$set('tooltip', message);

                    var updateWatch = scope.$watch(attr.ngModel, function (newValue, oldValue) {
                        if (newValue != oldValue) {
                            ngModel.$setValidity(errorCode, true);

                            if (hasToolTip) {
                                $(el).bind('blur', function () {
                                    attr.$set('tooltip', '');
                                    $(el).unbind('blur');
                                });
                            }
                            updateWatch(); // un-register watch
                        }
                    });
                });
            }
        };
    })
    .directive('s4ValidateObject', function () {
        return {
            restrict: 'E',
            require: 'ngModel',
            scope: true,
            template: '<alert ng-repeat="alert in Alerts" type="alert.type" close="CloseAlert($index)">{{alert.msg}}</alert>',
            replace: false,
            link: function (scope, el, attr, ngModel) {

                var modelName = (attr['s4ValidateObject']) ? attr['s4ValidateObject'] : attr.ngModel;

                scope.$on('ValidationError.' + modelName, function (event, errorCode, message) {
                    ngModel.$setValidity(errorCode, false);
                    scope.AddAlert(message);

                    var updateWatch = scope.$watch(attr.ngModel, function (newValue, oldValue) {
                        if (newValue != oldValue) {
                            ngModel.$setValidity(errorCode, true);
                            scope.AcknowledgeModelUpdate();
                            updateWatch(); // un-register watch
                        }
                    }, true);
                });
            },
            controller: function ($scope, $element, $attrs) {
                $scope.Alerts = [];

                $scope.AddAlert = function (message) {
                    $scope.Alerts.push({ type: 'error', msg: message});
                };

                $scope.CloseAlert = function (index) {
                    $scope.Alerts.splice(index, 1);
                };

                $scope.AcknowledgeModelUpdate = function () {
                    var len = $scope.Alerts.length;
                    for (var i = 0; len > i; i++) {
                        var alert = $scope.Alerts[i];
                        delete alert.type;
                    }
                };

                $scope.$on('ResetValidationObjects', function () {
                    $scope.Alerts = [];
                });

            }
        };
    });
