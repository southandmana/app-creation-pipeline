# Step 5: One-at-a-Time Question Engine

## Purpose
Ask focused, individual questions to gather complete app requirements. This is the iterative loop that ensures 100% completeness before building.

---

## Core Principle
**ONE QUESTION AT A TIME** - Never overwhelm users with multiple questions.

---

## Question Flow Structure

### Round 1: Essential Questions
**Always ask these core questions first:**

#### Question 1: Device Compatibility
```
📋 Understanding Your Idea (Question 1 of ~6)

Should this work on phones and tablets too, or just computers?
```

#### Question 2: Online Sharing
```  
📋 Question 2 of ~6

Want to put this online so friends can play from anywhere?
(I'll handle all the technical setup - no coding knowledge needed!)
```

#### Question 3: Category-Specific Core Feature
**For Games:**
```
📋 Question 3 of ~6

Should this be single-player against the computer, two players taking turns, or both?
```

**For Task Management:**
```
📋 Question 3 of ~6

How should people organize their tasks - by categories, due dates, priority levels, or just one simple list?
```

**For Dashboards:**
```
📋 Question 3 of ~6

What's the most important information this should display first when someone opens it?
```

#### Question 4: Visual Style
```
📋 Question 4 of ~6

For the look and feel, would you prefer:
- Fun and colorful with animations
- Clean and minimal 
- Professional business look
- You decide (surprise me!)
```

#### Question 5: Data Persistence
```
📋 Question 5 of ~6

Should this remember information when you close and reopen it? (Like scores, settings, saved data)
```

### Round 2+: Gap-Filling Questions
Based on answers and category, ask specific follow-ups.

---

## Category-Specific Question Banks

### Games - Additional Questions
```
How smart should the computer opponent be?
- Easy (makes random moves)
- Medium (blocks your wins, takes obvious wins)  
- Hard (thinks several moves ahead)
- Multiple difficulty levels

Want sound effects when things happen in the game?

Should there be any special animations or effects when someone wins?

Any time limits or should players take as long as they want?

Want to track high scores or win streaks?
```

### Task Management - Additional Questions  
```
Should tasks have due dates and reminders?

Want to assign different priority levels (like urgent, normal, low)?

Should multiple people be able to share the same task list?

Any special features like recurring tasks (daily, weekly)?

Want categories or tags to organize tasks better?
```

### Dashboard - Additional Questions
```
Should the data update automatically or when users refresh?

Want any charts or graphs to visualize the information?

Should different people see different information?

Any alerts or notifications when certain conditions are met?

How often should the data refresh?
```

### E-commerce - Additional Questions
```
How will customers pay? (PayPal, credit cards, or just contact forms for now)

Want customer reviews and ratings on products?

Should customers be able to create accounts and save favorites?

Any special features like discount codes or sales?

Need inventory tracking (how many items left)?
```

### Social - Additional Questions
```
Should people create accounts or can they participate anonymously?

Want private messaging between users?

Should there be moderation or reporting features?

Any special roles (like admins, moderators, regular users)?

Want notifications when people interact with you?
```

---

## Question Response Templates

### Standard Format:
```
📋 Understanding Your Idea (Question [X] of ~[Y])

[Single focused question]

[Optional context in parentheses if needed]
```

### Progress Indicators:
- Always show current question number
- Show estimated total (adjust as you learn more)
- Keep momentum with progress

### User-Friendly Elements:
- **Emoji indicators** (📋, 🎮, 🎨, 💾, etc.)
- **Simple language** - avoid technical terms
- **Context when helpful** - brief explanations in parentheses
- **Options when useful** - give examples to guide thinking

---

## Processing User Answers

### Store Each Answer:
```python
user_answers = {
    "device_compatibility": "phones and computers",
    "online_sharing": "yes",
    "game_mode": "both single and multiplayer", 
    "visual_style": "fun and colorful",
    "data_persistence": "yes, remember scores",
    # ... continue for each question
}
```

### After Each Answer:
1. **Acknowledge:** "Got it!"
2. **Process:** Update internal requirements
3. **Evaluate:** Is this enough information?
4. **Decide:** Ask another question OR move to specification

---

## Advanced Question Logic

### Dynamic Question Generation:
```python
def generate_next_question(user_answers, app_category, round_number):
    if round_number == 1:
        return get_essential_questions(app_category)
    else:
        gaps = identify_information_gaps(user_answers, app_category)
        return generate_targeted_question(gaps[0])
```

### Gap Identification Examples:
```python
# If user said "multiplayer" but didn't specify local vs online
if "multiplayer" in answers["game_mode"] and not answers.get("multiplayer_type"):
    return "Is that local multiplayer (same computer) or online multiplayer (over internet)?"

# If user wants e-commerce but no payment method specified  
if app_category == "ecommerce" and not answers.get("payment_method"):
    return "How should customers pay - credit cards, PayPal, or just contact forms for now?"
```

---

## Handling Different Response Types

### Clear Answers:
```
User: "Yes, I want it online"
AI: "Perfect! Moving to question 3..."
```

### Uncertain Answers:
```
User: "I'm not sure about that"
AI: "No worries! I can make a good choice for that. Let me ask about..."
```

### "You Decide" Answers:
```
User: "You decide what looks best"
AI: "Great! I'll make it look awesome. Next question..."
```

### Overly Complex Answers:
```
User: "Well, it depends on whether the user is authenticated and has premium access..."
AI: "I love the detailed thinking! Let me ask about the basics first, then we'll add those advanced features..."
```

---

## Loop Control

### Continue Loop If:
- Critical information missing
- Contradictory answers need clarification
- Technical feasibility questions remain
- User experience gaps identified

### Exit Loop When:
- All essential questions answered
- No critical gaps identified
- Can build complete, professional app
- Web research can fill remaining details

---

## Error Recovery

### If User Gets Overwhelmed:
```
Feeling like a lot of questions? Don't worry - we're almost done! 

These help me build exactly what you want instead of guessing. 

Want to skip ahead and let me make the remaining decisions? Just say "you choose the rest" and I'll handle it!
```

### If User Gives Conflicting Answers:
```
Quick clarification - earlier you mentioned [X] but now you're saying [Y]. 

Which direction do you prefer? Both are totally doable!
```

---

## Documentation Updates (Throughout Step 5)

**MANDATORY**: Update user requirements documentation after each question response:

```bash
# Update 01_user_requirements.md with:
# - Each question asked and user's exact response
# - Structured question responses in the appropriate sections
# - Any clarifications or assumptions made
# - Progress tracking of information gathering
```

**Key Documentation Points:**
- Record all user answers verbatim in "Structured Question Responses" section
- Track completeness assessment after each round
- Document any gaps identified and how they were addressed
- Note when user opts out or delegates decisions to agent

---

## Success Criteria
✅ One question at a time (never multiple)
✅ Clear progress indication  
✅ User feels guided, not interrogated
✅ All critical information gathered
✅ No assumptions made about missing info
✅ User can opt out if overwhelmed
✅ Documentation updated with all responses
✅ Smooth transition to specification generation

---

## Next Step Trigger
When completeness evaluation determines all essential info is gathered → Step 6: Completeness Evaluation and Web Research