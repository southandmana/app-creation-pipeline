# Step 1: Initiate Trigger System

## Purpose
This prompt activates when user types "initiate" to start the React web app creation process.

---

## Trigger Detection
**User Input:** `initiate`

**Response:** Immediately activate the app creation flow

---

## Initial Response Template

```
Welcome! I'll help you create a React web app. 

What type of app would you like to create? Here are some examples:
1. 📝 Task Management App (todos, projects, deadlines)
2. 🎮 Game App (puzzle, card game, board game)
3. 📊 Dashboard App (analytics, data visualization)
4. 🛍️ E-commerce App (product catalog, shopping cart)
5. 💬 Social App (chat, forum, blog)

Or describe your own idea!
```

---

## Implementation Notes

### Key Elements:
- **Welcoming tone** - Sets positive, helpful mood
- **Clear categories** - Gives users direction without overwhelming
- **Examples in parentheses** - Helps users understand each category
- **Open-ended option** - "Or describe your own idea!"
- **Emojis** - Makes it visually friendly and approachable

### User Experience Goals:
- Reduce decision paralysis with clear examples
- Appeal to non-developers with friendly language
- Set expectation that AI will handle complexity
- Get user excited about creating something

### Expected User Responses:
- Category selection: "I want to make a game app"
- Specific idea: "I want to make a Connect Four game"
- Custom idea: "I want to make an app that tracks my workouts"
- Vague idea: "Something fun for my friends"

### Next Step Trigger:
Any response from user advances to Step 2 (App Type Processing)

---

## Error Handling

### If user gives unclear response:
```
That sounds interesting! Let me help clarify - which category best fits your idea?

1. 📝 Task Management (organizing, tracking things)
2. 🎮 Game (entertainment, competition, puzzles) 
3. 📊 Dashboard (showing data, charts, stats)
4. 🛍️ E-commerce (buying/selling products)
5. 💬 Social (chat, sharing, community)

Or just tell me more about what you have in mind!
```

### If user says they don't know:
```
No worries! Let's start simple. 

Would you like to create:
- Something fun and entertaining? (Game)
- Something to help organize your life? (Task Management)
- Something to show information visually? (Dashboard)

Just pick what sounds most interesting to you!
```

---

## Testing Scenarios

### Test Input 1: `initiate`
**Expected:** Shows welcome message with 5 categories

### Test Input 2: User types something else first
**Expected:** Normal Claude response, doesn't trigger flow

### Test Input 3: `INITIATE` (caps)
**Expected:** Should still trigger (case-insensitive)

---

## Success Criteria
✅ User sees welcoming, clear categories
✅ User feels confident picking an option  
✅ Non-developers don't feel intimidated
✅ Sets positive tone for entire experience
✅ Advances user to next step smoothly