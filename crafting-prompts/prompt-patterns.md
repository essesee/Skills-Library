# Prompt Patterns Reference

## 1. Persona Pattern
**Fundamental statements:** Act as Persona X. Perform task Y.

Replace "X" with a specific persona (e.g., "speech language pathologist", "nutritionist") and specify a task for that persona to perform.

**Examples:**
- Act as a computer that has been the victim of a cyber attack. Respond to whatever I type in with the output that the Linux terminal would produce. Ask me for the first command.
- Act as the lamb from the Mary Had a Little Lamb nursery rhyme. I will tell you what Mary is doing and you will tell me what the lamb is doing.
- Act as a gourmet chef. I am going to tell you what I am eating, and you will tell me about my eating choices.

---

## 2. Question Refinement Pattern
**Fundamental statements:** From now on, whenever I ask a question, suggest a better version of the question to use instead. (Optional) Prompt me if I would like to use the better version instead.

**Examples:**
- From now on, whenever I ask a question, suggest a better version of the question to use instead.
- From now on, whenever I ask a question, suggest a better version of the question and ask me if I would like to use it instead.
- Whenever I ask a question about who is the greatest of all time (GOAT), suggest a better version of the question that puts multiple players' unique accomplishments into perspective. Ask me for the first question to refine.

---

## 3. Cognitive Verifier Pattern
**Fundamental statements:** When you are asked a question, follow these rules: Generate a number of additional questions about X. Combine the answers to the individual questions to produce Y.

Replace "X" with a topic that would help more accurately answer the question, and "Y" with the specific information you want in the final answer.

**Examples:**
- When you are asked to create a recipe, follow these rules. Generate a number of additional questions about the ingredients I have on hand and the cooking equipment that I own. Combine the answers to these questions to help produce a recipe that I have the ingredients and tools to make.
- When you are asked to plan a trip, follow these rules. Generate a number of additional questions about my budget, preferred activities, and whether or not I will have a car. Combine the answers to these questions to better plan my itinerary.

---

## 4. Audience Persona Pattern
**Fundamental statements:** Explain X to me. Assume that I am Persona Y.

Replace "Y" with an appropriate persona (e.g., "have limited background in computer science", "a healthcare expert") and specify the topic X.

**Examples:**
- Explain large language models to me. Assume that I am a bird.
- Explain how the supply chains for US grocery stores work to me. Assume that I am Genghis Khan.

---

## 5. Flipped Interaction Pattern
**Fundamental statements:** I would like you to ask me questions to achieve X. You should ask questions until condition Y is met or to achieve this goal (alternatively, forever). (Optional) Ask me the questions one at a time.

Replace "X" with a goal (e.g., "creating a meal plan") and "Y" with a stopping condition (e.g., "until you have sufficient information about my audience and goals").

**Examples:**
- I would like you to ask me questions to help me create variations of my marketing materials. You should ask questions until you have sufficient information about my current draft messages, audience, and goals. Ask me the first question.
- I would like you to ask me questions to help me diagnose a problem with my Internet. Ask me questions until you have enough information to identify the two most likely causes. Ask me one question at a time. Ask me the first question.

---

## 6. Game Play Pattern
**Fundamental statements:** Create a game for me around X OR we are going to play an X game. [One or more fundamental rules of the game]

Replace "X" with a game topic (e.g., "math", "cave exploration") and provide rules.

**Examples:**
- Create a cave exploration game for me to discover a lost language. Describe where I am in the cave and what I can do. I should discover new words and symbols for the lost civilization in each area of the cave I visit. Each area should also have part of a story that uses the language. I should have to collect all the words and symbols to be able to understand the story. Tell me about the first area and then ask me what action to take.
- Create a group party game for me involving DALL-E. The game should involve creating prompts on a topic that you list each round. Everyone creates a prompt and generates an image. People vote on the best prompt based on the image it generates. At the end of each round, ask me who won and list the current score. Describe the rules and then list the first topic.

---

## 7. Template Pattern
**Fundamental statements:** I am going to provide a template for your output. X is my placeholder for content. Try to fit the output into one or more of the placeholders that I list. Please preserve the formatting and overall template that I provide. This is the template: PATTERN with PLACEHOLDERS.

Replace "X" with an appropriate placeholder marker (e.g., "CAPITALIZED WORDS", `<angle brackets>`).

**Examples:**
- Create a random strength workout for me today with complementary exercises. I am going to provide a template for your output. CAPITALIZED WORDS are my placeholders for content. Try to fit the output into one or more of the placeholders that I list. Please preserve the formatting and overall template that I provide. This is the template: NAME, REPS @ SETS, MUSCLE GROUPS WORKED, DIFFICULTY SCALE 1-5, FORM NOTES
- Please create a grocery list for me to cook macaroni and cheese from scratch, garlic bread, and marinara sauce from scratch. I am going to provide a template for your output. `<dish>` are my placeholders for content. This is the template: Aisle `<number>`: `<items>` (`<dish(es) used in>`)

---

## 8. Meta Language Creation Pattern
**Fundamental statements:** When I say X, I mean Y (or would like you to do Y).

Replace "X" with a statement, symbol, or word, and map it to a meaning Y.

**Examples:**
- When I say "variations (something)", I mean give me ten different variations of [something].
  - Usage: "variations (company names for a company that sells software services for prompt engineering)"
  - Usage: "variations (a marketing slogan for pickles)"
- When I say Task X [Task Y], I mean Task X depends on Task Y being completed first.
  - Usage: "Describe the steps for building a house using my task dependency language."
  - Usage: "Provide an ordering for the steps: Boil Water [Turn on Stove], Cook Pasta [Boil Water], Make Marinara [Turn on Stove], Turn on Stove [Go Into Kitchen]"

---

## 9. Recipe Pattern
**Fundamental statements:** I would like to achieve X. I know that I need to perform steps A, B, C. Provide a complete sequence of steps for me. Fill in any missing steps. (Optional) Identify any unnecessary steps.

Replace "X" with a task and specify the steps you know are required.

**Examples:**
- I would like to purchase a house. I know that I need to perform steps: make an offer and close on the house. Provide a complete sequence of steps for me. Fill in any missing steps.
- I would like to drive to NYC from Nashville. I know that I want to go through Asheville, NC on the way and that I don't want to drive more than 300 miles per day. Provide a complete sequence of steps for me. Fill in any missing steps.

---

## 10. Alternative Approaches Pattern
**Fundamental statements:** If there are alternative ways to accomplish a task X that I give you, list the best alternate approaches. (Optional) Compare/contrast the pros and cons of each approach. (Optional) Include the original way I asked. (Optional) Prompt me for which approach I would like to use.

**Examples:**
- For every prompt I give you, if there are alternative ways to word a prompt that I give you, list the best alternate wordings. Compare/contrast the pros and cons of each wording.
- For anything that I ask you to write, determine the underlying problem that I am trying to solve and how I am trying to solve it. List at least one alternative approach to solve the problem and compare/contrast the approach with the original approach implied by my request.

---

## 11. Ask for Input Pattern
**Fundamental statements:** Ask me for input X.

Replace "X" with a specific input type such as "a question", "an ingredient", or "a goal".

**Examples:**
- From now on, I am going to cut/paste email chains into our conversation. You will summarize what each person's points are in the email chain. You will provide your summary as a series of sequential bullet points. At the end, list any open questions or action items directly addressed to me. My name is Jill Smith. Ask me for the first email chain.
- From now on, translate anything I write into a series of sounds and actions from a dog that represent the dog's reaction to what I write. Ask me for the first thing to translate.

---

## 12. Menu Actions Pattern
**Fundamental statements:** Whenever I type: X, you will do Y. (Optional: additional menu items) Whenever I type Z, you will do Q. At the end, you will ask me for the next action.

Replace "X" with a command pattern and specify the action Y it triggers.

**Example:**
- Whenever I type "add FOOD", you will add FOOD to my grocery list and update my estimated grocery bill. Whenever I type "remove FOOD", you will remove FOOD from my grocery list and update my estimated grocery bill. Whenever I type "save" you will list alternatives to my added FOOD to save money. At the end, you will ask me for the next action. Ask me for the first action.

---

## 13. Fact Check List Pattern
**Fundamental statements:** Generate a set of facts that are contained in the output. The set of facts should be inserted at POSITION in the output. The set of facts should be the fundamental facts that could undermine the veracity of the output if any of them are incorrect.

Replace POSITION with where to insert the facts (e.g., "at the end of the output").

**Example:**
- Whenever you output text, generate a set of facts that are contained in the output. The set of facts should be inserted at the end of the output. The set of facts should be the fundamental facts that could undermine the veracity of the output if any of them are incorrect.

---

## 14. Tail Generation Pattern
**Fundamental statements:** At the end, repeat Y and/or ask me for X.

Replace "Y" with what the model should repeat (e.g., "repeat my list of options") and "X" with what it should ask for (e.g., "for the next action"). These statements usually go at the end of the prompt or second-to-last.

**Examples:**
- Act as an outline expander. Generate a bullet point outline based on the input that I give you and then ask me for which bullet point you should expand on. Create a new outline for the bullet point that I select. At the end, ask me for what bullet point to expand next. Ask me for what to outline.
- From now on, at the end of your output, add the disclaimer "This output was generated by a large language model and may contain errors or inaccurate statements. All statements should be fact checked." Ask me for the first thing to write about.

---

## 15. Semantic Filter Pattern
**Fundamental statements:** Filter this information to remove X.

Replace "X" with a definition of what to remove (e.g., "names and dates", "costs greater than $100").

**Examples:**
- Filter this information to remove everything but the first sentence of each paragraph.
- Filter this email to remove redundant information.
