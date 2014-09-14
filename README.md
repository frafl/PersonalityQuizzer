PersonalityQuizzer - v0.02
=================

A simple API to quickly create a Personality quiz.

<a href="http://stefanhayden.github.io/PersonalityQuizzer/">Try a simple demo here</a>

Requires: <a href="http://jquery.com/">jQuery</a>, <a href="https://github.com/blakeembrey/dombars">DOMBars</a>


include libraries

```html
	<script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>
	<script type="text/javascript" src="vendor/dombars.min.js"></script>
	<script type="text/javascript" src="PersonalityQuizzer.js"></script>
```

Start new PersonalityQuizzer object and fit it with quiz data:

```javascript
	var quiz = new PersonalityQuizzer();
	quiz.set("title","What kind of Git Repo are you?")
	quiz.addQuestion({ 
		color: "#7FD863",
		title: "Tabs or Spaces?",
		answers: [
			{ 
				text: "Tabs",
				result: 1
			},
			{ 
				text: "Spaces",
				result: 2
			},
		]
	});
	quiz.addResults([
		{
			id: 1,
			text: "You are a Tabs repo!"
		},
		{
			id: 2,
			text: "You are a spaces repo!"
		},
	]);
```

## API for PersonalityQuizzer() objects

Parameter | Values | Default | Notes
---------- | --------- | -------- | -------------
setTitle | text | - | sets a name. Mainly for reference in the templates
addQuestions | array | - | an array of question objects
addQuestion | object | - | a <a href="#anatomy-of-a-question-object">question object</a> with child <a href="#anatomy-of-an-answer-object">answer objects</a>
addResults | array | - | an array of <a href="#anatomy-of-a-result-object">result objects</a>
render | - | - | force quiz to render

## PersonalityQuizzer Options
```javascript
var quiz = new PersonalityQuizzer({
	options: {
		append: "body",
		quiz_template: "#quiz_template",
		question_template: "#question_template",
		answer_template: "#answer_template",
		result_template: "#result_template",
		shuffle: true,
		debounce: 10,
		onlyOnce: false,
		method: "simplePoints",
	},
	questions: [ /* questions objects */ ],
	results: [ /* result objects */ ],
});
```
Parameter | Values | Default | Notes
---------- | --------- | -------- | -------------
append | text | body | this is the css selector that the quiz will be appended to.
quiz_template | text | quiz_template | the id of the template
question_template | text | question_template | the id of the template
answer_template | text | answer_template | the id of the template
result_template | text | result_template | the id of the template
shuffle | boolean | true | shuffles the question order.
debounce | number | 10 | stop the template from rerendering to often.
onlyOnce | boolean | false | after finishing the quiz no change of answers is possible
method | text | simplePoints | only one method implemented which only adds all points for each result, thus this allows for Borda count, Range Voting and similar (if each question is seen as a voter); Condorcet methods will follow

## Anatomy of a Question Object

Add as many questions as needed. Each question must have an array of <a href="#anatomy-of-an-answer-object">answer object</a>. 

Other attributes will be passed in to the template.

```javascript
{
	answers: [ /* answer object */ ] /* required */
}
```


## Anatomy of an Answer Object

Each answer object must have one of two things:
Either a result id that corresponds with the <a href="#anatomy-of-a-result-object">result objects</a>. The score is used to weight different answers in favor of different results.
The second option is to provide a result list, i.e. an array of <a href="#anatomy-of-a-result-level-object">levels of results</a> in decreasing order of appropriateness to the answer.

Other attributes will be passed in to the template.

```javascript
{
	result: 1, /* this or a resultList is required, as list has preference over a single result */
	resultList: [ /* result levels objects */ ]
	score: 1 /* optional, only used if no resultList is given */
}
```

## Anatomy of a Result Level Object

A result level object must contain an array of integers called "results". Each element of this set shall be an id corresponding to a <a href="#anatomy-of-a-result-object">result object</a>. Each result in receives score points for this question. Note that you may also assign malus points (i.e. negative points) to a level.

```javascript
{
	score: 1, /* optional */
	results: [/* result ids */]
}
```


## Anatomy of a Result Object

each result object must have an id that will be used in reference by <a href="#anatomy-of-an-answer-object">answer object</a>.

Other attributes will be passed in to the template.

```javascript
{
	id: 1, /* required */
}
```

Add these Handelbars templates to the HTML. Though instead of using the Handelbars library we will use the DOMBars library for easy event binding and live updates. Be sure to include the 3 outlet helpers for questions, answers and results. None of the class names or html in the example below are required. Feel free to customize as much as you like!

```html

<script id="quiz_template" type="text/x-handlebars-template">
	<div class="quiz">
		<div class="questions">
			{{{outlet 'questions'}}}
		</div>
		<div class="results">
			{{{outlet 'results'}}}
		</div>
	</div>
</script>


<script id="question_template" type="text/x-handlebars-template">
	<div class="question" >
		<h1 style="background:{{color}}">{{{title}}}</h1>
		<div class="answers">
				{{{outlet 'answers'}}}
		</div>
	</div>
</script>

<script id="answer_template" type="text/x-handlebars-template">
	
	<div class="answer {{#if selected}}selected{{/if}}" >
		<div class="wrapper">
		<div class="table">
			<div class="text" style="background:{{parent.color}}">
				{{text}}
			</div>
		</div>
		</div>
		<div class="footer">
			<div class="checkbox"></div>
		</div>
	</div>
	
</script>

<script id="result_template" type="text/x-handlebars-template">
		<div class="result {{#if selected}}selected{{/if}}" >
			<div class="wrapper">
				<div class="title">{{title}}</div>
				<p>{{text}}</p>
			</div>
		</div>
</script>
```
