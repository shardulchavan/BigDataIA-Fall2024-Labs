## TapeAgents 
A new holistic agent framework that supports practitioners at both the agent development and data-driven agent optimization stages.

## Tape
A comprehensive, structured, granular, semantic-level log of the agent session

## Nodes
Agents are built from Nodes. They are the basic atoms of intelligence. It can be one LLM call and the processing of the call’s output.

Nodes generate new tape entries that we call steps.

## Steps
**Thought steps** to express reasoning and **action steps** to request external inputs.
Examples of what an agent can do in a step include making a long-term plan, reasoning about how to fulfill the plan or how to use a tool, requesting a tool call.

Examples- 

From TapeAgents/examples/gaia_agent/steps.py 

**Thoughts**

Chain of thoughts of logical reasoning to find the answer.

    class ReasoningThought(GaiaThought):
        kind: Literal["reasoning_thought"] = "reasoning_thought"
        reasoning: list[str] = Field(description="reasoning sentences that describe how to move forward with the task")
    
**Actions**

Action that provides parameters for a search function call. Could search in the web_search or wikipedia. Search results will be ordered by relevance from top to bottom

    class SearchAction(GaiaAction):
        kind: Literal["search_action"] = "search_action"
        source: str = Field(description="source to search in, could be web_search or wikipedia")
        query: str = Field(description="search query")
    
The **environment** responds to the action steps at the end of the tape with **observation steps** that it likewise
 appends to the tape.

 ## Environment 

 The main method of an environment object is:
       
    def react(self, tape)-> Tape.
    
 The environment.react searches for the unfulfilled actions in the tape and adds the corresponding observation steps to the tape.

 The orchestrator invokes the agent and the environment in an alternate fashion and maintains full control over their interactions.

 ![image](https://github.com/user-attachments/assets/5a342914-85d4-416c-9a02-8c57646ed74c)

## Agents

The **agents** in TapeAgents read the tape to make the LLM prompt and then process the LLM output to append
new steps to the tape.
 TapeAgent agent generates steps and makes a new tape by appending the generated steps to the input tape.
 Specifically, agent.run(tape) runs an iterative reasoning loop that, at every iteration, selects a node, lets it make the prompt
 and generates the next steps. By default, the
 agent will run its nodes sequentially. The loop continues as long as the nodes only
 generate thoughts. When a node produces an action, the agent
 stops and returns a new tape with the generated steps from
 all iterations appended to it. More precisely, agent.run(tape)
 returns an AgentStream object for streaming events like partial
 tapes and steps. An agent may have subagents for whom this agent is the
 manager.

 ![image](https://github.com/user-attachments/assets/8a98be1b-a2eb-45f5-b669-42c09b186894)
