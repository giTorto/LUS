# Exercise 2: Verify user age
In this folder you can find a rasa project that allows to verify the age of a user.
The project is based on rasa init. 

The extension of the standard rasa init project consists of the following changes:
* added an action to verify the age
* change the stories to ask age in the hello/welcome message
* configure the action endpoint (please see endpoints.yml)
* extended the NLU with additional intents and entities


## Set Up
To run this example you need to install the following packages:
```shell
rasa==2.6.1
rasa-sdk==2.6.0
```

After the packages are installed you need to open a terminal page and run:
```shell
rasa run actions
```

This command will instance a webserver exposing the action endpoint.
After that you have to train the system with the given command:
```shell
rasa train
```

When the agent is trained you can test it with `rasa shell` command.


## Changes explained
In the following section there is an explanation of the changes performed.

### Added custom action
In the file actions/actions.py, you can find the implementation of a simple action that verifies the age of the user and it outputs a prompt.
The action has been added to the domain.yml file.
```yaml
actions:
  - action_verify_age
```

### Stories modification
In the stories.yml file is possible to see the changes performed to the stories. As visible in the file, now in each story
when the welcome message is present we take the following steps:
```yaml
- story: happy path
  steps:
  - intent: greet
  - action: utter_greet
  - action: utter_ask_age
  - intent: inform_age
  - action: action_verify_age
  - action: utter_ask_fine
```

### Action Endpoint Configuration
We added an action endpoint to the endpoints.yml file. This is necessary in order to allow the agent to find the action server.
```yaml
action_endpoint:
  url: "http://localhost:5055/webhook"
```

### NLU Extension
To support the extended logic and in order to be able to capture the user age, we extended the nlu.yml file with the following lines.
```yaml

- intent: inform_age
  examples: |
    - I'm [24](age_number) years old
    - [32](age_number) years old
    - [45](age_number)

- regex: age_number
  examples: |
    - \d{1,2}
```
Please consider that currently in the configuration we don't have RegexExtractor. However we have a RegexFeaturizer.
The RegexFeaturizer combined with DIETClassifier provides an additional feature to the extractor. [here](https://rasa.com/docs/rasa/nlu-training-data#regular-expressions-for-entity-extraction)
The idea is that both the DIETClassifier and CRFEntityExtractor available in rasa learn the use of the regular expression in context.