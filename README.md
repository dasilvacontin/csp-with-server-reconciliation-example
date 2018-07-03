# CSP with Server Reconciliation Example

Note / context: client inputs are sent when the client advances/predicts the next turn.
No clock syncronization is needed between client and server.

## Glossary

Turn: each of the states the game goes through. Executing the logic function advances the current turn to the next one.

Turn length: the time that each game turn lasts.

## Problems you might face during implementation

- Getting the server state for a turn you haven't predicted already.
    
    Think about it again. If we haven't predicted the turn we are getting a state for, it means we are still playing/showing the previous server state we got. For this to happen, simplifying, the ping has to be low, lower than the turn length. In reality, depending on _when_ your game advances to the next turn, it doesn't need to be so low.

    It is most likely caused by having a logic loop that is not synced up with when you should actually advance turn / send inputs. Most likely scenario: you are advancing the logic in your render loop, a.k.a `requestAnimationFrame` loop.

    Instead, calculate the time when you should actually advance to the next turn, and set up a timer for that. This time is `ping` dependant, since we advance client execution to the server so that the inputs get there on the same turn / game-time. Otherwise, set up a very frequent loop, and check whether you should advance turn in it.

- How to calculate which turn should the client be on.

    This is analogous to other questions such as "when should the client advance turn?", "when should it sent its inputs?", etc (since it sends its inputs when it advances to the next turn).

    When you get the server state, obtain the current time. This turn/state was sent by the server `ping` ms ago (`Date.now() - ping`), so that is actually the turn's timestamp (`serverStateTs` in code). We can now answer "when should we have sent the inputs for this turn?", and we call it `clientStateTs`. Since we want the inputs to get to the server in time of their use in the correct turn, we have to take into account the travel time, `ping`. Also, since `ping` varies over time, it would be advisable to add a buffer to the calculation. So we get:
    
    `clientStateTs = serverStateTs - ping - arrivalErrorMarginMs`

    where `arrivalErrorMarginMs` is `Math.min(10, TURN_LENGTH/2)` (in case the `TURN_LENGTH` is very small, the buffer can only be half its length, and in that case we'll be aiming right between the previous and the next turn calculation).

