#Ajax Validation for Angularjs#

ngRemoveValidate makes it easy for you to validate form fields agents data from your server. For example, a sign up form may need to check if the email entered is already registered before submitting the form.

**Features:**

- Drop in solution for Ajax validation of any text or password input

- Works with Angular's built in validation and cab be accessed at `formName.inputName.$error.ngRemoteValidate`

- Throttles server requests (default 400ms) and can be set with `ng-remote-throttle="550"`

- Allows HTTP method definition (default POST) with `ng-remote-method="GET"`

- Send other form values to include in validation with `ng-remote-reference="input_name"`

##Getting started - Example##

**Adding ngRemoteValidate to your project**

```
bower install ng-remote-validate
```

*OR*

Grab either the minified version or the standard source from the release folder and add it to your project.

```html
<script type="text/javascript" src="../your/path/ngRemoteValidate.js"></script>
```

**Include ngRemoteValidate in you Angular app**

```javascript
var app = angular.module( 'myApp', [ 'remoteValidation' ] );
```

**Using it in your forms**

This will be a basic change password form that requires the user to enter their current password as well as the new password.
```html
<h3>Change password</h3>
<form name="changePasswordForm">
    <label for="currentPassword">Current</label>
    <input type="password" 
           name="currentPassword" 
           placeholder="Current password" 
           ng-model="password.current" 
           ng-remote-validate="/customer/validpassword" 
           required>
    <span ng-show="changePasswordForm.currentPassword.$error.required && changePasswordForm.confirmPassword.$dirty">
        Required
    </span>
    <span ng-show="changePasswordForm.currentPassword.$error.ngRemoteValidate">
        Incorrect current password. Please enter your current account password.
    </span>

    <label for="newPassword">New</label>
    <input type="password"
           name="newPassword"
           placeholder="New password"
           ng-model="password.new"
           required>
    
    <label for="confirmPassword">Confirm</label>
    <input ng-disabled=""
           type="password"
           name="confirmPassword"
           placeholder="Confirm password"
           ng-model="password.confirm"
           ng-match="password.new"
           required>
    <span ng-show="changePasswordForm.confirmPassword.$error.match">
        New and confirm do not match
    </span>
    
    <div>
        <button type="submit" 
                ng-disabled="changePasswordForm.$invalid" 
                ng-click="changePassword(password.new, changePasswordForm);reset();">
            Change password
        </button>
    </div>
</form>
```

##Options##
There are a few defaults that can be overwritten with options. They are:

- `ng-remote-validate` takes a string or an Array of strings i.e. `ng-remote-validate="/url/one"` or `ng-remote-validate="[ '/url/one', '/url/two' ]"`
- `ng-remote-throttle` (default: 400) Users inactivity length before sending validation requests to the server
- `ng-remote-method` (default: 'POST') Type of request you would like to send
- `ng-remote-reference` takes a string or an Array of strings i.e. `ng-remote-reference="input_name"` or `ng-remote-reference="[ 'input_name', 'other_input_name' ]"`

**Example using all**
```html
<input type="password" 
       name="currentPassword" 
       placeholder="Current password" 
       ng-model="password.current" 
       ng-remote-validate="/customer/validpassword"
       ng-remote-throttle="550"
       ng-remote-method="GET"
       ng-remote-reference="username"
       required>

<input type="text" 
       name="email" 
       placeholder="Email address" 
       ng-model="email" 
       ng-remote-validate="[ '/customer/email-registered', '/customer/email-restricted' ]"
       ng-remote-throttle="800"
       ng-remote-method="POST"
       ng-remote-reference="[ 'email_verify', 'username' ]"
       required>
```

ngRemote will also add a class named `ng-processing` to the input while the Ajax request is processing and also sets a $processing property on your model property. You can add some css to your project to show an animation in the input like so:

```css
input.ng-processing {
    background-image: url(../img/loader-small.gif) !important;
    background-position: right center !important;
    background-repeat: no-repeat;
}
```

```html
<span class="message" ng-show="formName.inputName.$processing">validating...</span>
```

##Data sent to server##
```javascript
{
    value_to_validate: '' //Is the value to be validated on the server
}
```

If you used ng-remote-reference, those values are sent with the element's name as the key, for example:

```javascript
{
    value_to_validate: '', //Is the value to be validated on the server
    input_name: '', //Value of input_name
    other_input_name: '' //Value of other_input_name
}
```

##Expected server response##

ngRemoteValidate wants a specific JSON response from your servers. The response should look as follows:

```javascript
{
    isValid: bool, //Is the value received valid 
    value: 'myPassword!' //value received from server
}
```
