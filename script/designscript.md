# Script Path

## Make your first script.


## Function Flags

🚩**IS_ASK**

Recording the state of each script items. 

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 0 | Haven't been asked.|
| 1 | Have been asked. |

🚩**KEYWORD**

Sentences and words used for choosing dialogue branches.

Usage:

1. Use ';' for adding a new keyword。 

2. Different usages based on MATCH_TYPE。

	* **Case: Fuzzy Match**

		Every keywords would be transformed into vectors and be calculated similarity with the INPUT sentence.
The branch which are __*most similar to the INPUT sentence*__ and __*over the threshold*__ would be chosen.

	* **Case: Exact Match**

		If the INPUT sentence __*contains any keyword*__ , the branch with the keyword would be chosen.

🚩**ID**

Each script item owns an identical number.

🚩**IS_ROOT**

Root is the entry of script items.

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 0 | Is branch.|
| 1 | Is root. |

🚩**TAG**

Branch number of script items. Started from '1'(Root would be '0')

🚩**SPEAK_TEXT**

It is the sentence which would output in this node. If it's seperated by ";", means that there are two sentences. Output will be one of them.

🚩**Next_TAG**

Branch information of the next branch. There are three formats:

1. Number of next branch

	- If there is only one branch with the number, it will be the next node. 
	- If there are several branches with the same number, it will choose one based on MATCH_TYPE with their keywords.

2. "end" 

	- Current script item is finished.

3. "end";[script name];[ID]

	- Current script item is finished  and the other item, belong to current script or other scripts, is reopenned.
	
	- It's able to make sequences of script items.

🚩**IS_LIMIT**

Restricted item util lifting by it's upper item. 

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 0 | Ready for asking.|
| 1 | Restricted. |

🚩**VOLUME**

Robot Speak Config

Noted that different robots have a different range of parameter value, please check the correspond robot interface.

🚩**SPEED**

Robot Speak Config

Noted that different robots have a different range of parameter value, please check the correspond robot interface.

🚩**PITCH**

Robot Speak Config

Noted that different robots have different range of parameter value, please check the correspond robot interface.

🚩**ACTION_TYPE**

Robot Action Template

Noted that different robots have different functions and action combinations, please check the correspond robot interface.

🚩**TIMELIMIT**

Item would be restricted until time's up. 

🚩**SPEC_PRIORITY**

For special situations like asking assigned item no matter how or stopping the dialogue system.

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 1 | If the item is in ready state(not restricted), be set to the highest priority for asking.|
| 2 | Terminate the dialogue system.|

🚩**LOOP_MAX**

The max looping times of the branch.

🚩**AFTER_LOOP_MAX**

If the branch choosing of the conversation is up to the max of looping times, selects the assigned branch.

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 0 | Other loop options|
| 1 | The final option be chosen.|

🚩**MATCH_TYPE**

The method that choose the branch.

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 1 | Fuzzy match: Based on the similarity of INPUT sentence and keywords of branches.|
| 2 | Exact match: Choose the branch whether INPUT sentence contains any keyword or not.|

🚩**IF_NONE_MATCH**

If the round of the dialogue isn't matching any choice of branch, select the branch with this flag.

Symbolics:

| Syntax | Description |
| :---: |   :----|
| 0 | Other options|
| 1 | The option be chosen.|

🚩***QNAME**

Script name.

Noted that this flag is automatically rendered by the system, do not need to define in the script file.
