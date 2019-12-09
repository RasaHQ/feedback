# Story Structure Validation Tool

## Introduction

Some of you may know this situation: You’ve inserted a new bot action into one of your stories, or just added a dozen new training stories, and this somehow makes your bot perform worse? This can happen if your stories are inconsistent (I’ll explain this in detail below). 
To make it easier for you to find and correct such inconsistencies, we are developing a new functionality into `rasa data validate`.

### The problem we’re trying to solve
The purpose of Rasa Core is to predict the correct next bot action, given the dialogue state, that is the history of intents, entities, slots, and actions. 
Crucially, Rasa Core assumes that for any given dialogue state, exactly one next action is the correct one. 
If your stories don’t reflect that, Rasa Core cannot learn the correct behaviour.

Take, for example, the following two stories:
```
## Story 1
* greet
  - utter_greet
* inform_happy
  - utter_happy
  - utter_goodbye

## Story 2
* greet
  - utter_greet
* inform_happy
  - utter_goodbye
```
These two stories are inconsistent, because Rasa Core cannot know if it should predict `utter_happy` or `utter_goodbye` after `inform_happy`, as there is nothing that would distinguish the dialogue states at `inform_happy` in the two stories and the subsequent actions are different in Story 1 and Story 2. The stories would be consistent, however, if Story 2 was
```
## Story 2
* greet{"name": "Bruce Wayne"}
  - utter_greet
* inform_happy
  - utter_goodbye
```
and the Rasa Core policy that is used for prediction takes at least 3 prior story steps into account (i.e. `max_history=3` or more). 
This works, because the fact that the entity name was mentioned in the first user utterance is part of the dialogue state at `inform_happy`.
While this problem is easy to see and fix in the example above, you will have a hard time spotting such inconsistencies in 100+ stories that have many more turns. 
Especially considering that you might have glued stories together with checkpoints, and you also have to remember which slots are featurized (and thus part of the dialogue state) and which slots are not. 
Thus, we are now experimenting with a new tool that finds these inconsistencies for you.

### The proposed solution
The command line tool `rasa data validate` has existed for some time now, and it already helps you with finding unused intents and other things. 
We are now expanding this tool to validate if your stories are consistent.
For the example above (with the new Story 2), if you type `rasa data validate stories --max-history 1` in your project folder you would get
```
CONFLICT after intent 'inform_happy':
  utter_goodbye predicted in 'Story 2'
  utter_happy predicted in 'Story 1'
```
whereas if you type `rasa data validate stories --max-history 3`, you get
```
No story structure conflicts found.
```
You can now decide if you want to increase the max_history of your policies, or if you want to change one of your stories.
Note, that the names of your stories have to be unique. Otherwise, the script will tell you about the duplicate names and not continue until you have fixed this.

## Setup

Try it yourself! 
Just follow these steps to explore the examples in this feedback repo:

1. [Install rasa from source](https://rasa.com/docs/rasa/user-guide/installation/#building-from-source)
2. In the Rasa repo, `~/rasa` say, do `git checkout story-tree-1`
3. Run `pip install -e .` in the Rasa repo
4. Clone this (feedback) repo into a different directory, say `~/feedback`

Once you have installed rasa from source and checked out the `story-tree-1` branch, you can also test the tool on your favourite project.

## Feedback

You could help us a great deal by giving us feedback on the following questions:
1. Is rasa data validate stories the best place for this functionality?
2. Is it clear from the output what the script has found?
3. Are the conflicts that you find correct?
4. Is it straightforward to find and resolve these conflicts, or would you like more insights from the output?
We are looking forward to your feedback!


