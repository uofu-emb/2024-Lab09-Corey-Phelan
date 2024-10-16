# FSM

## Invariants

### From example:
"An approach signal will always precede a depart signal"
"The power will never be interrupted"
"Trains on each track only run in one direction."

### Additional ones we find:
"Only one train per line can exist between the arriving and departing sensors." (Environmental assumption, supporting a variable number trains per line in the critical zone would require an extended state machine or semaphore)
"The train takes longer than 10 seconds to reach the intersection after passing the arriving sensor." (A violation of this assumption would result in the train crossing the intersection before the arms go down, creating a safety hazard)
"The raising and lowering of the arms is instantaneous." (The scenario doesn't specify any length of time required by)
"Timer event fires 10 seconds after train crosses approach signal (leading edge) or depart signal (falling edge)"
"When a train is crossing the intersection, the arms are down." (Safety issue if violated)
"Sensor is always reliable."

### Counterexample for example FSM
1. nb_approach -> ringing, arms up
2. elapsed -> ringing, arms down
3. sb_approach -> (no transition)
4. nb_depart -> ringing, arms up
5. Southbound train is crossing the intersection after the arms have gone up, violating our invariant.

## Verification

| number | arms_down | alarm_on | northbound_present | southbound_present | north_approach | south_approach | north_depart | south_depart | ringing | safety_hazard |
|--------|-----------|----------|--------------------|--------------------|----------------|----------------|--------------|--------------|---------|---------------|
| 0      | 0         | 0        | 0                  | 0                  | 6              | 5              | 18           | 18           | 19      | -             |
| 1      | 0         | 0        | 0                  | 1                  | -              | -              | -            | -            | -       | 20            |
| 2      | 0         | 0        | 1                  | 0                  | -              | -              | -            | -            | -       | 20            |
| 3      | 0         | 0        | 1                  | 1                  | -              | -              | -            | -            | -       | 20            |
| 4      | 0         | 1        | 0                  | 0                  | 6              | 5              | 18           | 18           | 0       | -             |
| 5      | 0         | 1        | 0                  | 1                  | 7              | 17             | 18           | 16           | 13      | -             |
| 6      | 0         | 1        | 1                  | 0                  | 17             | 7              | 16           | 18           | 14      | -             |
| 7      | 0         | 1        | 1                  | 1                  | 17             | 17             | 16           | 16           | 15      | -             |
| 8      | 1         | 0        | 0                  | 0                  | -              | -              | -            | -            | -       | 21            |
| 9      | 1         | 0        | 0                  | 1                  | -              | -              | -            | -            | -       | 20            |
| 10     | 1         | 0        | 1                  | 0                  | -              | -              | -            | -            | -       | 20            |
| 11     | 1         | 0        | 1                  | 1                  | -              | -              | -            | -            | -       | 20            |
| 12     | 1         | 1        | 0                  | 0                  | -              | -              | -            | -            | -       | 21            |
| 13     | 1         | 1        | 0                  | 1                  | 15             | 17             | 18           | 4            | 13      | -             |
| 14     | 1         | 1        | 1                  | 0                  | 17             | 15             | 4            | 18           | 14      | -             |
| 15     | 1         | 1        | 1                  | 1                  | 17             | 17             | 13           | 14           | 15      | -             |

| number | invariant |
|--------|-----------|
| 16     | The arms must go down before a train can cross the intersection (or depart). |
| 17     | Only one train per line can exist between the arriving and departing sensors. |
| 18     | An approach signal will always precede a depart signal. |
| 19     | Alarm may not ring until it receives an approach signal. (A train cannot depart if it isn't already present.) |
| 20     | Alarm remains on until 10 seconds after no trains are present. |
| 21     | Arms must be up when there are no trains present. |

