## greetings
* greet
  - utter_greet
> check_greet

## happy path
> check_greet
* mood_great
  - utter_happy

## problem
> check_greet
* greet
  - utter_goodbye

## problem 2
> check_greet
* mood_great
  - utter_goodbye

## sad path 1
> check_greet
* mood_unhappy
  - utter_cheer_up
* affirm
  - utter_happy

## sad path 2
> check_greet
* mood_unhappy
  - utter_cheer_up
* deny
  - utter_goodbye

## say goodbye
* goodbye
  - utter_goodbye

## bot challenge
* bot_challenge
  - utter_iamabot
