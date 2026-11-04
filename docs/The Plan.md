# Context
This is supposed to be a project for my portfolio, using modern AI frameworks, specifically LangChain, LangGraph and LangFuse.

The ideia is: An AI chatbot, taylor made for player of Riftbound, the league of legends trading card game. The game can have some very complicated rules, with a very large and complicated document, as well as a few blog posts, describing said rules. The goal, then, is for the chatbot to know all of the rules and cards (presumably through a RAG of said documents) to be able to explain any doubts players might have, similar to what a judge might do in real life.

As such, the chat interface will have some features built around dealing with the TCG:
- Whenever a card is mentioned, the user can hover over it to see that card.
- The user can load in their decklist as an attachment (and we should keep some of their lists stored, for convenience)
- The user can reference cards, similarly to how we reference files in AI coding tools (probably with the same syntax, obviously needs auto complete for the user)

I expect to have a react frontend site and a python backend.

# Use cases
- A player might ask for clarification for how a card works, attaching that card to the conversation so the AI knows exaclty what card it is without ambiguity.
- A player might ask for the outcome of a certain series of plays.
- A player might ask for help with deckbuilding, how to play certain matchups or other general strategy tips.
- A player might ask what the correct play would be for a given situation

## Some important limitations
We have almost no bugdet (apart from an almost endless supply of AI tokens, usable only to build the project, not to actually run the agents the chatbot needs) so we'll be using only free tiers of anything we use. 

The chatbot shouldn't have any specific features that let it *play* the game, it's only here to help actual players.

