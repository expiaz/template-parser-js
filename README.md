Chino is a little template parser for javascript who can be used front as well as server side.


## Basic usage
```javascript
var chino = require('chino');
var engine = new chino();
engine.render('index.chino',{name:'userName'});
```

## API


## Parsing rules

### Expresion
There is only if and for expressions on chino. Expression can add contexts taht variables on their block can use.
Expressions are surrounded by <%%>

Markdown | Usage
---------|------
<%for {{array}} as {{single_element}}%>...<%endfor%> | iterate through the array, if the current element is an object, a context is added.
<%if {{varName}}%>...<%endif%> | check the condition, if true execute the code inside. If varName is an object (not an array), a context is added.

### Variables
Basically chino will search for a given variable on every context that expressions have added, beginning from the last to the first. Stopping when finded or replaced by '' when not.
variable are surrounded by {{}} as in mustache.

Markdown | Usage
---------|------
{{varName}} | simply replace the varName with the one provided on the vars parameter
{{>varName}} | attempt to inject the varName.chino template in the base template (injections)
{{&varName}} | will only search for this variable on the actual context. Replaced by '' if not finded.
{{!varName}} | inverse the value of varName (true become false), quite useful for 'if'.
{{:varName}} | only used for 'if'/'for' expressions, will not add the varName context if it's an object or an array.

##Examples

_main.js_
```javascript
var chino = require('chino');
var engine = new chino();
engine.render('Greet.chino',{user:{username:'John Doe'}});
```

_Greet.chino_
```html
<div>
    <h1>Welcome</h1>
    <%if {{user}}%>
        <!--
        'if' add user context
        actual context : {username:'John Doe'}
        result : <span>John Doe</span>
        -->
        <span>{{username}}</span>
    <%endif%>
    <%if {{!user}}%>
        <span>Guest</span>
    <%endif%>
</div>
```

Result:
```html
<div>
    <h1>Welcome</h1>
    <span>John Doe</span>
</div>
```


####Same example with :,&

_Greet.chino_
```html
<div>
    <h1>Welcome</h1>
    <%if {{:user}}%>
        <!--
        'if' does not add user context because of ':'
        username is linked to context with '&'
        actual context : {user:{username:'John Doe'}}
        result : <span></span>
        -->
        <span>{{&username}}</span>
    <%endif%>
    <%if {{:user}}%>
         <!--
         'if' does not add user context because of ':'
         actual context : {user:{username:'John Doe'}}
         result : <span></span>
         -->
         <span>{{username}}</span>
    <%endif%>
    <%if {{:user}}%>
        <!--
        'if' does not add user context because of ':'
        actual context : {user:{username:'John Doe'}}
        result : <span>John Doe</span>
        -->
        <span>{{user.username}}</span>
    <%endif%>
    <%if {{user}}%>
         <!--
         'if' add the user context
         actual context : {username:'John Doe'}
         user.username is not in actual context, chino reach the next one which is {user:{username'John Doe'}} and find the user.username value
         result : <span>John Doe</span>
         -->
         <span>{{user.username}}</span>
    <%endif%>
    <%if {{user}}%>
         <!--
         'if' add the user context
         actual context : {username:'John Doe'}
         username is linked to context with '&'
         user.username is not in actual context so it's replaced by ''
         result : <span></span>
         -->
         <span>{{&user.username}}</span>
    <%endif%>
</div>
```