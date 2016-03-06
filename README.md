# jquery-event-validate

An event based validation plugin used for client-side validation of html forms.

## Design Considerations

**Validations** Unlike all other form validations this plugin primary focus is the validation rules and not the form inputs. During validations the plugin will loop the defined fields config looking for any inputs which might exist and if and only if the form input exists validate the input's value. All other plugins loop the inputs and validate the form inputs. This plugin loops the validations and could careless if a form input exists or not.

**Decoupled Decoration**  Form and input decorations should not be part of a validation plugin (e.g. seperation of concerns). See jquery-event-decoration. The validation code should be loosely coupled with the decoration code by DOM events. The validation code triggers events and the decoration code listens to these events and decorates the DOM accordingly.

**Delagated Events** All other form validation plugins attach keyup events directly to the form inputs to provide live validation to the user. These event listens should be delegated to the plugin container (ie normally the form tag) to allow for changes to the form DOM from the insertions, removals and modifications of form inputs via form control plugins and dynamic HTML features.

## Advantages

These design considerations have the following advantages:

* You can change the form DOM at anypoint. It does not matter if you invoke this plugin before form control plugins or after form control plugins which might modify the DOM.
* You can define validation fields and validators in advance of the form inputs actually entering the DOM.
* If your app code removes a form control and later re-inserts the form control no change is required to be made to the validation configuration.


## Usage

```javascript

var config = {
	fields: {
		field1: {
			selector: '', //any jquery selector, defaults to '[name="field1"]'
			container: '.form-group', //any jquery selector
			validators: {
				required: {
					enabled: true, //true, false, selector (radio#other:checked), callback
					message: 'Please enter an amount.'
				},
				pattern: {
					enabled: true,
					regexp: "[0-9]",
					message: 'You have entered an invalid character.'
				},
				callback: {
					enable: true,
					method: function(...){

					},
					message: 'Some string or function'
				}
			}
		},
		...
};

var form = $('form');
form.jevValidate(config);

//monitor all validation events
var allEvents = $.jevValidate.allEvents; //array of event names
form.on(allEvents, function(event, data){
	console.log(event, data)
});

//decorate the form
form.on(['jev.form.success', 'jev.form.failure'], function(event, data){
	console.log(event, data)
	//todo: decorate form/inputs here
});
```

## Events EMITTED

The following events are emitted by the plugin on the form container.

- 'jev.form.success', {values: values}
- 'jev.form.failure', {failures: [{message: '', value: '', element: ''}], success: [{value: '', element: ''}]}
- 'jev.field.success', {value: value, element: element}
- 'jev.field.failure', {value: value, element: element, validator: validator}
- 'jev.field.enabled', {validator: validator}
- 'jev.field.disabled', {validator: validator}
- 'jev.field.initalized', {field data}

## Others

- http://formvalidation.io/
- http://jqueryvalidation.org/
	- Issue: multiple inputs with same name
		- http://stackoverflow.com/questions/931687/using-jquery-validate-plugin-to-validate-multiple-form-fields-with-identical-nam/4136430#4136430
		- https://github.com/jzaefferer/jquery-validation/pull/717
		- http://blog.kyawzinwin.me/jquery-validation-for-array-of-input-elements/
		- http://www.codeboss.in/web-funda/2009/05/27/jquery-validation-for-array-of-input-elements/
	- Issue: any input (ie tagsinput) without name attribute will throw an error
		- ignore: '*:not([name])'
		- https://github.com/jzaefferer/jquery-validation/labels/name-attribute
- http://www.formvalidator.net/

##

