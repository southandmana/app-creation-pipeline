# Step 2: App Type Processing

## Purpose
Process user's app type selection and transition to detailed description gathering.

---

## Input Processing

### Category-Based Responses:
- "I want to make a game app" → Game category
- "Task management" → Productivity category  
- "Something for my business" → Business category
- "Connect Four game" → Specific game idea

### Specific App Responses:
- "Connect Four" → Board game
- "Todo list" → Task management
- "Weather app" → Dashboard/utility

---

## Response Templates

### For Game Category:
```
Great! A [GAME TYPE] app.

Don't worry about technical details - I'll handle all the coding complexity. 
Just describe what you want like you're explaining to a friend!

Please tell me:
- How should the game work?
- Any special features or twists you want?
- What should it look like?
- Single player, multiplayer, or both?

The more you tell me, the better I can build exactly what you imagine!
```

### For Task Management Category:
```
Excellent! A task management app.

Don't worry about technical details - I'll handle all the coding complexity.
Just describe what you want like you're explaining to a friend!

Please tell me:
- What kinds of tasks will people track?
- How should they organize their tasks?
- Any special features (reminders, categories, deadlines)?
- What should it look like?

The more you tell me, the better I can build exactly what you imagine!
```

### For Dashboard Category:
```
Perfect! A dashboard app.

Don't worry about technical details - I'll handle all the coding complexity.
Just describe what you want like you're explaining to a friend!

Please tell me:
- What information should it display?
- Where does the data come from?
- Any charts, graphs, or special displays?
- Who will use this dashboard?

The more you tell me, the better I can build exactly what you imagine!
```

### For E-commerce Category:
```
Awesome! An e-commerce app.

Don't worry about technical details - I'll handle all the coding complexity.
Just describe what you want like you're explaining to a friend!

Please tell me:
- What products will you sell?
- How should customers browse and buy?
- Any special features (reviews, wishlist, discounts)?
- What should the shopping experience feel like?

The more you tell me, the better I can build exactly what you imagine!
```

### For Social Category:
```
Cool! A social app.

Don't worry about technical details - I'll handle all the coding complexity.
Just describe what you want like you're explaining to a friend!

Please tell me:
- How will people interact with each other?
- What will they share or discuss?
- Any special features (groups, messaging, posts)?
- What kind of community are you building?

The more you tell me, the better I can build exactly what you imagine!
```

### For Custom/Unclear Ideas:
```
Interesting idea! I'd love to help you build that.

Don't worry about technical details - I'll handle all the coding complexity.
Just describe what you want like you're explaining to a friend!

Please tell me:
- What should this app do?
- Who will use it and why?
- What's the main purpose or goal?
- Any special features that make it unique?

The more you tell me, the better I can build exactly what you imagine!
```

---

## Key Elements

### Reassurance Elements:
- "Don't worry about technical details"
- "I'll handle all the coding complexity"
- "Like you're explaining to a friend"

### Information Gathering:
- **Functionality** - How it works
- **Features** - What makes it special
- **Appearance** - Visual preferences
- **Users** - Who will use it

### Encouragement:
- "The more you tell me, the better"
- "Exactly what you imagine"

---

## Processing Logic

### Step 2a: Identify Category
```python
if "game" in user_input.lower():
    category = "game"
    template = game_template
elif any(word in user_input.lower() for word in ["todo", "task", "manage", "organize"]):
    category = "task_management" 
    template = task_template
elif any(word in user_input.lower() for word in ["dashboard", "chart", "data", "analytics"]):
    category = "dashboard"
    template = dashboard_template
# ... etc
```

### Step 2b: Customize Response
- Replace [GAME TYPE] with specific game mentioned
- Adjust questions to match category
- Set context for next step

### Step 2c: Store Context
```python
session_context = {
    "app_category": category,
    "user_input": user_input,
    "step": "detailed_description",
    "timestamp": now()
}
```

---

## Error Handling

### If user gives very vague response:
```
That's a great start! To help me understand better, could you tell me:

Is this more like:
- A game or entertainment app?
- Something to help organize or track things?
- A way to display information or data?
- Something for buying/selling?
- A social or communication tool?

Just pick the closest match, and then we'll dive into the details!
```

### If user wants multiple types:
```
I love the ambition! Let's start with the main feature first, then we can add the other parts later.

What's the most important thing your app should do?
- [List the types they mentioned with numbers]

We'll build that core feature first, then expand from there!
```

---

## Success Criteria
✅ User feels understood and excited
✅ Category is correctly identified
✅ User gets tailored questions for their app type
✅ User knows what information to provide next
✅ Technical complexity is hidden but capability is clear
✅ Sets up Step 3 (detailed description) perfectly

---

## Next Step Trigger
Any detailed response from user advances to Step 3 (Detailed Description Collection)