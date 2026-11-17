## Autonomous Preference Learning

The `learning engine` should be capable of `updating persistent preferences` without requiring approval for every change.

However, `autonomous learning` must be `evidence-based`, `explainable`, and `reversible`.

---

#### 1 Learning Inputs
The learning engine can receive several types of feedback signals.

##### A. Explicit Feedback

Explicit feedback - `direct instructions` by user.


Potential learning signals:

| User Feedback | Potential Learning Signal |
|---|---|
| Explain this in more detail. | Preference for detailed explanations |
| This explanation is too shallow. | Preference for greater technical depth |
| Don't simplify technical terminology. | Preference for preserving technical terminology |
| Include implementation details. | Preference for practical implementation guidance |
| Explain the underlying mechanism. | Preference for conceptual and internal explanations |

> `Explicit feedback` is generally a strong `learning signal` because the user directly communicates what should change.

learning engine must distinguish between:
- `Temporary instructions` applying only to the `current response`.
- `Topic-specific` preferences.
- `Recurring` preferences.
- `Global` preferences.

A single instruction should not automatically become a permanent global preference.

---

##### B. Document Edits

The `learning engine` should compare generated documents against `user-modified` versions to identify `recurring editing patterns`.

Possible signals:

| User Modification | Potential Learning Signal |
|---|---|
| Repeatedly `expanded explanations` | Preference for additional detail |
| Removed `unnecessary content` | Preference for concise and relevant information |
| Added missing `implementation details` | Preference for practical technical coverage |
| `Reorganized headings` | Possible structural preference |
| Replaced `tables` with prose | Possible preference for narrative explanations |
| Added `formatting` | Preference for catchy visuals on important topics | 

Document edits must be `interpreted cautiously` because users may modify content for reasons unrelated to their general preferences.

> Document modifications should initially be treated as `observations` rather than `confirmed preferences`.

`Repeated patterns` across `multiple documents` can gradually `increase confidence`.

---

##### C. Acceptance and Rejection

The system can observe whether users `accept`, `reject`, or `request regeneration` of generated explanations.


Potential learning signals:

| User Action | Potential Learning Signal |
|---|---|
| `Accepts` generated explanation | Weak positive signal |
| `Rejects` generated explanation | Negative signal requiring contextual analysis |
| `Repeatedly requests` regeneration | Possible mismatch with expectations |
| Requests `additional detail` | Preference for greater depth |
| Accepts `revised explanation` | Evidence that the revision addressed the issue |

Acceptance alone is weak evidence of a specific preference.

> `explanation` does not necessarily indicate a preference for short explanations, technical explanations, or a particular writing style.

The `learning engine` should `combine acceptance patterns` with more `explicit evidence` before `modifying persistent preferences`.

---

##### D. Human Interruptions

An `interruption` during an explanation can `provide valuable contextual information`.

Example:

> "I don't understand virtual interfaces. Explain what they are before discussing bridge networking."

This provides two possible learning signals:

1. `Knowledge gap:` The user requires additional understanding of virtual interfaces.
2. `Explanatory preference:` The user may `prefer prerequisite concepts` to be explained before dependent concepts.

These signals must be stored separately.
- A `knowledge gap` represents the`system's understanding` about `user's current knowledge`.
- A `preference` represents `how the user wants information` to be presented.
- The two must not be treated as interchangeable.
