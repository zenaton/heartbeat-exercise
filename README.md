### Purpose

The purpose of this hiring exercise is to test out your level in Elixir/OTP and see if you are a real problem solver which is one of Zenaton core values.

### General acceptance criteria

- All code is in `git` repo (candidate can use his/her own github account).
- OTP application is `--sup` mix project
- Engine application name is `:zenaton_engine` (main Elixir module is `ZenatonEngine`).
- Agent application name is `:zenaton_agent`  (main Elixir module is `ZenatonAgent`)
- Agent Interface is just set of public functions of `ZenatonAgent` (no API endpoint, no REST/SOAP )
- Applications should **not** use any database / disc storage. All needed data should be stored only in application memory.
- Candidate can use any Elixir or Erlang library he/she wants to.(but app can be written in pure Elixir/ Erlang/ OTP)
- Code accuracy also matters. Readable, safe, refactorable code is plus.

### Heartbeat project

The hearbeat is a feature implemented between one engine and **n** agents. It is made to improve monitoring, resiliency to any network or infrastructure issues.

The main work is that agents send a `state`, with additional information and the engine answers back with an `action` and `data`.

You are free to choose the connection protocol to make this possible (HTTP/TCP/Erlang message).

Possible state for an agent:
- **Starting**: transient state between command listen and listening
- **Stopping**: transient state between command unlisten and disconnection
- **Listening**: connected to queues
- **Sleeping**: disconnected from queues

**WARNING**: You don't have to work on the queues connection, it is not the purpose of this exercise.

#### Engine

the Engine **MUST** stores agents identificator in memory with his current state.

the Engine **MUST** stores `HEART_BEAT_PERIOD` in memory which can be changed via Ex shell (value in sec).

the Engine **MUST** also have a `messages_in_adhoc` Map. Eg. `messages_in_adhoc: %{"agent_1" => 3}` where 3 represents the number of message available for agents

When Engine receives:
`state` === `starting`
**Then:** action=`listen` with `heart_beat_period`

When Engine receives:
`state` === `sleeping` AND `messages_in_adhoc` related to the agent id 
**Then:** action=`listen` with `heart_beat_period`

When Engine receives:
`state` === `listening` AND agent not active for more than `HEART_BEAT_PERIOD` related to the agent id 
**Then:** action=`sleep`

#### Agent