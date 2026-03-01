---
description: Guide for using specialized Agent Skills
---

To effectively activate and use code analysis skills, follow these rules:

### 1. Task Classification
Based on the user’s request, determine the most appropriate Skill:

- **Exploration & Understanding**: Use [code-explorer](file:///Users/anhnguyen/Projects/workspace/skills/code-explorer/SKILL.md) when you need to understand existing code flows.
- **Design & Planning**: Use [code-architect](file:///Users/anhnguyen/Projects/workspace/skills/code-architect/SKILL.md) for system architecture tasks.
- **UI Design**: Use [frontend-designer](file:///Users/anhnguyen/Projects/workspace/skills/frontend-designer/SKILL.md) for high-quality web/UI design.
- **Inspection & Debugging**: Use [code-reviewer](file:///Users/anhnguyen/Projects/workspace/skills/code-reviewer/SKILL.md) for single files, or [pr-reviewer](file:///Users/anhnguyen/Projects/workspace/skills/pr-reviewer/SKILL.md) for full Pull Requests.

### 2. How to Activate
When you detect that a request falls into one of the categories above:

1. Inform the user that you will use the corresponding skill.  
2. Read the `SKILL.md` file in that skill’s directory to understand the process (Analysis Approach, Core Process, or Review Scope).  
3. Follow the steps defined in the skill and present results according to the **Output Guidance** section.  

### 3. Examples
- “Explain the user registration flow” → Use **code-explorer**.  
- “I want to add QR code payment” → Use **code-architect**.  
- “Review the changes I just made” → Use **code-reviewer**.  
