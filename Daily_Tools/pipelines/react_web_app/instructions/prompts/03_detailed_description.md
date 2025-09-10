# Step 3: Detailed Description Collection

## Purpose
Collect comprehensive details about the user's app idea before moving to system detection and questioning phases.

---

## Context
User has provided their app category/type and now needs to give detailed description of what they want built.

---

## Processing Approach

### Step 3a: Receive User's Detailed Description
User provides detailed explanation of their app idea.

### Step 3b: Acknowledge and Transition
Don't ask clarifying questions yet - that happens in Step 5. This step is purely about:
1. Acknowledging their input
2. Starting background system detection
3. Preparing for the structured questioning phase

---

## Response Template

```
Thanks for those details! I can already picture what you want to build - this is going to be awesome!

🔍 Let me quickly check your computer setup to make sure I build this optimally for your system...

[System detection happens here - Step 4]

Once I understand your setup, I'll ask a few quick questions to make sure I build exactly what you're envisioning.
```

---

## What NOT to Do in This Step

❌ **Don't ask clarifying questions yet**
- That happens in Step 5 (Structured Questions)
- Avoid: "What about multiplayer?" or "How should the UI look?"

❌ **Don't start building immediately**  
- Need system detection and structured questions first

❌ **Don't make assumptions**
- Store their input, don't interpret yet

❌ **Don't overwhelm with technical talk**
- Keep response simple and encouraging

---

## What TO Do in This Step

✅ **Acknowledge their input positively**
- "This is going to be awesome!"
- "I can picture what you want"

✅ **Set expectations for next steps**
- System check + questions coming
- Build exactly what they envisioned

✅ **Maintain momentum**
- Keep them excited about the project
- Smooth transition to technical prep

✅ **Store all details for later analysis**
- Full description saved for Step 4 analysis
- Nothing gets lost

---

## Example Interactions

### User Description: Connect Four Game
**User:** "I want to make a Connect Four game where you can play against the computer with different difficulty levels. Make it colorful and fun with animations when pieces drop. Also want to keep track of wins and losses."

**AI Response:**
```
Thanks for those details! I can already picture what you want to build - a colorful Connect Four game with smart AI opponents and score tracking. This is going to be awesome!

🔍 Let me quickly check your computer setup to make sure I build this optimally for your system...

[Proceeds to Step 4: System Detection]

Once I understand your setup, I'll ask a few quick questions to make sure I build exactly what you're envisioning.
```

### User Description: Task Manager
**User:** "I want a simple todo app but with categories for work, personal, and shopping. Users can add tasks, mark them done, and see their progress. Maybe some nice colors to make it not boring."

**AI Response:**
```
Thanks for those details! I can picture what you want - a clean task manager with categories and progress tracking, but with personality through nice colors. This is going to be awesome!

🔍 Let me quickly check your computer setup to make sure I build this optimally for your system...

[Proceeds to Step 4: System Detection]

Once I understand your setup, I'll ask a few quick questions to make sure I build exactly what you're envisioning.
```

---

## Data Storage & Documentation

### What to Store:
```python
session_data = {
    "raw_description": user_input,
    "app_category": category_from_step2,
    "timestamp": now(),
    "step": "system_detection",
    "user_enthusiasm": high/medium/low  # based on language used
}
```

### Documentation Creation (Steps 1-3)
**MANDATORY**: Create user requirements documentation file:

```bash
# Create documentation directory
mkdir -p generated_apps/[app-name]/_agent_session

# Copy and initialize user requirements template
cp instructions/templates/documentation/01_user_requirements_template.md \
   generated_apps/[app-name]/_agent_session/01_user_requirements.md
```

**Fill in the template with:**
- App name (from user's description)
- User's exact responses from Steps 1-3
- App category identified in Step 2
- Complete user description from Step 3
- Initial interpretation and assumptions made
- Timestamp information

This documentation provides complete traceability of user requirements from the beginning of the process.

### Analysis Prep:
- Parse description for mentioned features
- Identify potential gaps or ambiguities  
- Prepare category-specific questions for Step 5
- Note technical complexity level

---

## Transition Logic

### Always Proceed To:
Step 4: System Detection (with fallbacks)

### Context Passed Forward:
- Full user description
- App category
- Enthusiasm level
- Initial complexity assessment

---

## Error Handling

### If user gives very short description:
```
Great start! I can work with that.

🔍 Let me check your computer setup first...

After that, I'll ask some questions to help fill in the details and make sure I build exactly what you have in mind.
```

### If user gives extremely detailed technical description:
```
Wow, you've really thought this through! I love the detail.

🔍 Let me check your computer setup to make sure I can implement all these features optimally...

I might have a few questions to clarify some technical choices, then we'll get building!
```

### If user seems uncertain:
```
No worries - even rough ideas can become amazing apps!

🔍 Let me check your computer setup first...

Then I'll ask some questions to help us figure out the details together. We'll build exactly what you need!
```

---

## Success Criteria
✅ User feels heard and acknowledged
✅ User stays excited about the project  
✅ Smooth transition to system detection
✅ All details preserved for later analysis
✅ Expectations set for upcoming questions
✅ No premature assumptions or questions asked

---

## Next Step
Always advances to Step 4: System Detection