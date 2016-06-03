# Using $filter in Controllers

## Overview

We've looked at using filters in controllers before, but did you know that they also offer a massive performance boost?

## Objectives

- Describe using $filter in Controllers and $digest benefits
- Write a filter inside a Controller and refactor View filter logic

## Filters outside of controllers

When we use a filter on an `ng-repeat`, such as:

```html
<input ng-model="ctrl.search" />
<ul>
	<li ng-repeat="contact in ctrl.contacts | filter:ctrl.search">
		{{ contact.name }}
	</li>
</ul>
```

Everytime that our digest cycle is run, our filter is re-run, even if there are no changes to the `ctrl.search` value. This means there are pointless recalculations done by Angular.

One way that we can stop this is by actually filtering on the `ctrl.contacts` array in our controller. We can then control *when* we filter on the list.

We would take this:

```js
function ContactList() {
	this.contacts = [{
		name: 'Bill Gates',
		email: 'bill@microsoft.com',
		phone: '01234567890',
		username: 'b1lLG4Tes'
	},{
		name: 'Steve Jobs',
		email: 'steve@apple.com',
		phone: '12345678910',
		username: 'steveJOBS!!!!!!'
	},{
		name: 'Joe Bloggs',
		email: 'joe@bloggs.com',
		phone: '13092019340',
		username: 'jb'
	},{
		name: 'President Obama',
		email: 'president@whitehouse.com',
		phone: '75934988239',
		username: 'obamaY0'
	}];

	this.search = '';
}
```

and call `$filter('filter')` on our `contacts` array:

```js
function ContactList($filter) {
	this.contacts = [{
		name: 'Bill Gates',
		email: 'bill@microsoft.com',
		phone: '01234567890',
		username: 'b1lLG4Tes'
	},{
		name: 'Steve Jobs',
		email: 'steve@apple.com',
		phone: '12345678910',
		username: 'steveJOBS!!!!!!'
	},{
		name: 'Joe Bloggs',
		email: 'joe@bloggs.com',
		phone: '13092019340',
		username: 'jb'
	},{
		name: 'President Obama',
		email: 'president@whitehouse.com',
		phone: '75934988239',
		username: 'obamaY0'
	}];

	this.search = '';

	this.filteredList = $filter('filter')(this.contacts, this.search);
}
```

Now we have a filtered list in our `filteredList`. We will then change the `ng-repeat` over to use this list instead.

```html
<input ng-model="ctrl.search" />
<ul>
	<li ng-repeat="contact in ctrl.filteredList">
		{{ contact.name }}
	</li>
</ul>
```

Notice how we don't use a filter in our `ng-repeat` anymore, and it looks a lot neater? Having the filter in the controller allows us to know exactly what our data is going to be when we look at the controller, and it simplifies the view.

Now we need to update the filter when the `ctrl.search` value changes - we can do this using `ng-change`.

```html
<input ng-model="ctrl.search" ng-change="ctrl.refilter()" />
<ul>
	<li ng-repeat="contact in ctrl.filteredList">
		{{ contact.name }}
	</li>
</ul>
```

We're calling a `refilter` function in our controller - let's define that:

```js
function ContactList($filter) {
	this.contacts = [{
		name: 'Bill Gates',
		email: 'bill@microsoft.com',
		phone: '01234567890',
		username: 'b1lLG4Tes'
	},{
		name: 'Steve Jobs',
		email: 'steve@apple.com',
		phone: '12345678910',
		username: 'steveJOBS!!!!!!'
	},{
		name: 'Joe Bloggs',
		email: 'joe@bloggs.com',
		phone: '13092019340',
		username: 'jb'
	},{
		name: 'President Obama',
		email: 'president@whitehouse.com',
		phone: '75934988239',
		username: 'obamaY0'
	}];

	this.search = '';

	this.filteredList = $filter('filter')(this.contacts, this.search);

	this.refilter = function () {
		this.filteredList = $filter('filter')(this.contacts, this.search);
	};
}
```

There's no need for us to explicity write the `$filter` call twice. We can just simply call the `refilter` function in the controller:

```js
function ContactList($filter) {
	this.contacts = [{
		name: 'Bill Gates',
		email: 'bill@microsoft.com',
		phone: '01234567890',
		username: 'b1lLG4Tes'
	},{
		name: 'Steve Jobs',
		email: 'steve@apple.com',
		phone: '12345678910',
		username: 'steveJOBS!!!!!!'
	},{
		name: 'Joe Bloggs',
		email: 'joe@bloggs.com',
		phone: '13092019340',
		username: 'jb'
	},{
		name: 'President Obama',
		email: 'president@whitehouse.com',
		phone: '75934988239',
		username: 'obamaY0'
	}];

	this.search = '';

	this.refilter = function () {
		this.filteredList = $filter('filter')(this.contacts, this.search);
	};

	this.refilter();
}
```

Awesome! Now our filter is in our controller, and is only updated when we want it to be!
<p class='util--hide'>View <a href='https://learn.co/lessons/angular-filter-in-controllers-readme'>Using Filter In Controllers </a> on Learn.co and start learning to code for free.</p>
