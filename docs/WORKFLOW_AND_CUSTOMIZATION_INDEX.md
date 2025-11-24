# Workflow and Customization Documentation

This index provides a guide to understanding how messages flow through the Multi-Agent Custom Automation Engine and how to customize it for your specific needs.

## 📚 Documentation Overview

We've created two comprehensive guides to help you understand and customize the system:

### 1. 📖 [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
**Best for**: Understanding how the system works

A complete, detailed explanation of how messages flow through the system from start to finish. This guide is designed for users with limited coding experience who want to deeply understand the architecture and workflow.

**What you'll learn:**
- Complete message journey from user input to final response
- Detailed explanation of every component (Frontend, Backend, Agents, Database)
- Step-by-step walkthrough with code references
- Key concepts: Orchestration, Agent Teams, WebSocket communication
- How agents collaborate to solve complex tasks
- Troubleshooting common issues

**Length**: ~50 pages of detailed documentation

**When to use this guide:**
- You're new to the system and want to understand how it works
- You need to explain the system to others
- You're debugging issues and need to understand the flow
- You're planning major customizations

---

### 2. 🚀 [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
**Best for**: Making practical changes to the system

A hands-on guide with quick, practical examples for customizing the system. Perfect for users who want to jump straight into making changes.

**What you'll learn:**
- 5-minute changes (personality, starting tasks, team names)
- 15-minute changes (new agents, new teams, RAG setup)
- Advanced customizations (custom tools, conditional approval, multi-language)
- Common recipes (customer support, data analysis, content creation teams)
- Configuration reference
- Testing and best practices

**Length**: ~25 pages of practical examples

**When to use this guide:**
- You want to customize agent behavior quickly
- You need to create new agent teams
- You want pre-built recipes for common use cases
- You need configuration references

---

## 🗺️ Quick Navigation

### For First-Time Users

**Start here** → Read [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md) sections:
1. System Architecture Overview
2. Complete Message Workflow
3. Key Components Explained
4. Step-by-Step Message Journey

**Total time**: 30-45 minutes

---

### For Users Who Want to Customize

**Start here** → Jump to [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md):
1. Pick a change from "5-Minute Changes"
2. Follow the instructions
3. Test your change
4. Move to more advanced customizations

**Total time**: 5-60 minutes depending on complexity

---

### For Developers Building Custom Features

**Read both guides**:
1. [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md) - Understand the architecture
2. [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md) - See implementation patterns
3. Refer back as needed when building

---

## 📋 Common Scenarios

### Scenario: "I want to understand what happens when I send a message"

**Guide**: [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
**Section**: "Step-by-Step Message Journey"
**Time**: 15 minutes

---

### Scenario: "I want to make my agent more friendly"

**Guide**: [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
**Section**: "5-Minute Changes → Change Agent Personality"
**Time**: 5 minutes

---

### Scenario: "I want to create a customer support team"

**Guide**: [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
**Section**: "Common Recipes → Customer Support Team"
**Time**: 15 minutes

---

### Scenario: "I need to add a custom tool for my business process"

**Guide**: [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
**Section**: "Advanced Customizations → Custom Tool Creation"
**Time**: 30-60 minutes

---

### Scenario: "Agents aren't responding correctly"

**Guide**: [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
**Section**: "Troubleshooting"
**Time**: 10-20 minutes

---

### Scenario: "I want to enable document search for company policies"

**Guide**: [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
**Section**: "15-Minute Changes → Enable Document Search (RAG)"
**Time**: 15 minutes (plus Azure setup)

---

## 🎯 Learning Path

### Beginner Path
1. Read "System Architecture Overview" in [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
2. Try a "5-Minute Change" from [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
3. Read "Step-by-Step Message Journey" to understand what happened
4. Try more complex customizations

### Intermediate Path
1. Skim [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md) for architecture
2. Create a new agent using [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
3. Refer back to workflow guide when debugging
4. Explore advanced customizations

### Advanced Path
1. Read full [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
2. Study code references mentioned in the guide
3. Build custom tools using [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md) examples
4. Extend the system with new capabilities

---

## 📖 What's in Each Guide

### Message Workflow Guide Contents

1. **System Architecture Overview**
   - High-level component diagram
   - Technology stack explanation
   
2. **Complete Message Workflow**
   - 9-step overview from input to response
   
3. **Key Components Explained**
   - Frontend, Backend, Orchestration, Agents, Database
   - Location in codebase for each component
   
4. **Step-by-Step Message Journey**
   - Detailed 13-step walkthrough
   - Example: "Onboard Jessica Smith"
   - Code snippets showing what happens at each step
   - Phase breakdown: Connection, Processing, Orchestration, Approval, Execution, Completion
   
5. **Key Components Explained (Deep Dive)**
   - WebSocket communication
   - Agent orchestration
   - MCP tools
   
6. **Customization Guide**
   - Creating new teams
   - Modifying agent behavior
   - Adding custom tools
   - Modifying approval process
   - Adding RAG
   - Changing models
   - Customizing responses
   
7. **Common Scenarios**
   - Multi-step tasks
   - Agent clarification
   - Task rejection
   - Document search
   
8. **Troubleshooting**
   - Agent not responding
   - Plan approval issues
   - Wrong tools used
   - Slow responses
   - Incorrect team loaded
   
9. **Advanced Topics**
   - Streaming responses
   - Plan conversion
   - Error recovery
   - Monitoring

### Customization Quick Start Contents

1. **5-Minute Changes**
   - Change agent personality
   - Add starting tasks
   - Change team names
   
2. **15-Minute Changes**
   - Create new simple agent
   - Create new team
   - Enable document search
   - Use advanced reasoning model
   
3. **Advanced Customizations**
   - Custom tool creation
   - Conditional auto-approval
   - Multi-language support
   
4. **Common Recipes**
   - Customer support team
   - Data analysis team
   - Content creation team
   - Compliance & audit team
   
5. **Configuration Reference**
   - All agent options explained
   - Supported models
   - Model selection guide
   
6. **Testing Your Changes**
   - Quick test checklist
   - Common test scenarios
   
7. **Best Practices**
   - System message tips
   - Agent team design
   - Configuration management
   
8. **Troubleshooting Quick Fixes**
   - Agent not using tools
   - Wrong answers
   - Slow responses

---

## 🔍 Finding Specific Information

### Architecture Questions

| Question | Guide | Section |
|----------|-------|---------|
| What are the main components? | Message Workflow | System Architecture Overview |
| How do agents communicate? | Message Workflow | Key Components → WebSocket Communication |
| How does orchestration work? | Message Workflow | Key Components → Orchestration |
| What is the tech stack? | Message Workflow | System Architecture Overview |

### Configuration Questions

| Question | Guide | Section |
|----------|-------|---------|
| What agent options exist? | Customization Quick Start | Configuration Reference |
| Which model should I use? | Customization Quick Start | Model Selection Guide |
| How do I configure RAG? | Customization Quick Start | Enable Document Search |
| What are valid JSON fields? | Customization Quick Start | Agent Configuration Options |

### Customization Questions

| Question | Guide | Section |
|----------|-------|---------|
| How do I change agent tone? | Customization Quick Start | Change Agent Personality |
| How do I create a new team? | Customization Quick Start | Create New Team |
| How do I add custom tools? | Customization Quick Start | Custom Tool Creation |
| How do I modify approval? | Customization Quick Start | Conditional Auto-Approval |

### Troubleshooting Questions

| Question | Guide | Section |
|----------|-------|---------|
| Agent not responding? | Message Workflow | Troubleshooting → Issue 1 |
| Approval not working? | Message Workflow | Troubleshooting → Issue 2 |
| Agent using wrong tools? | Message Workflow | Troubleshooting → Issue 3 |
| Slow responses? | Message Workflow | Troubleshooting → Issue 4 |
| Wrong team loaded? | Message Workflow | Troubleshooting → Issue 5 |

### Code Questions

| Question | Guide | Section |
|----------|-------|---------|
| Where is the main API? | Message Workflow | Key Components → Backend API |
| Where are agent teams defined? | Message Workflow | Key Components → Agent Teams |
| Where is orchestration logic? | Message Workflow | Key Components → Orchestration Manager |
| Where are callbacks? | Message Workflow | Step-by-Step Journey → Step 11 |

---

## 🛠️ Hands-On Exercises

### Exercise 1: Understand the Flow (Beginner)
**Time**: 30 minutes

1. Read [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md) → "Step-by-Step Message Journey"
2. Deploy the application
3. Open browser developer tools → Network tab
4. Send message: "Hello"
5. Observe WebSocket messages
6. Match what you see to the guide

### Exercise 2: Create Custom Agent (Intermediate)
**Time**: 20 minutes

1. Read [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md) → "Create New Simple Agent"
2. Add a "WeatherAgent" to HR team
3. Give it system message about providing weather info
4. Test with: "What's the weather like?"
5. Observe agent response

### Exercise 3: Build New Team (Advanced)
**Time**: 45 minutes

1. Read [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md) → "Common Recipes"
2. Choose a recipe or create your own use case
3. Create new team JSON file
4. Define 2-3 specialized agents
5. Add starting tasks
6. Test full workflow
7. Debug and refine

---

## 💡 Tips for Success

### Reading the Documentation

✅ **Do:**
- Start with architecture overview to get context
- Follow examples step-by-step
- Try changes in a development environment first
- Refer back to workflow guide when confused
- Use search (Ctrl+F) to find specific topics

❌ **Don't:**
- Skip the architecture section
- Make changes without testing
- Modify production without backup
- Ignore warnings and notes

### Making Customizations

✅ **Do:**
- Start with small changes (5-minute changes)
- Test each change before moving to next
- Keep backup of working configurations
- Read the "Best Practices" section
- Monitor logs when testing

❌ **Don't:**
- Make multiple changes at once
- Skip testing
- Deploy to production without validation
- Ignore error messages

### Getting Help

If you're stuck:
1. Check the Troubleshooting section in [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
2. Review similar examples in [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
3. Check logs: `docker logs macae-backend`
4. Review the code files referenced in the documentation
5. File an issue on GitHub with details

---

## 📝 Documentation Conventions

### File References
When we reference a file, we show the full path:
```
File: src/backend/v3/api/router.py
Function: process_request()
```

### Code Examples
Code examples are shown with syntax highlighting:
```json
{
  "name": "AgentName",
  "deployment_name": "gpt-4.1-mini"
}
```

### Important Notes
Important information is highlighted:
> ⚠️ **Important**: Always backup configurations before making changes

### Tips and Warnings
✅ **Best Practice**: Start with small changes
❌ **Avoid**: Making multiple changes simultaneously

---

## 🚀 Getting Started Right Now

### If you have 5 minutes
1. Open [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
2. Go to "5-Minute Changes"
3. Try "Change Agent Personality"
4. See immediate results

### If you have 30 minutes
1. Open [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
2. Read "System Architecture Overview" and "Complete Message Workflow"
3. Follow along with an example in "Step-by-Step Message Journey"
4. Try one customization from Quick Start guide

### If you have 2 hours
1. Read full [Message Workflow Guide](./MESSAGE_WORKFLOW_GUIDE.md)
2. Work through "15-Minute Changes" in [Customization Quick Start](./CUSTOMIZATION_QUICK_START.md)
3. Build a complete custom team using a recipe
4. Test and refine your implementation

---

## 📚 Additional Resources

### Related Documentation
- [Deployment Guide](./DeploymentGuide.md) - How to deploy the system
- [Troubleshooting Steps](./TroubleShootingSteps.md) - Common deployment issues
- [MCP Server Guide](./mcp_server.md) - Model Context Protocol setup
- [Sample Questions](./SampleQuestions.md) - Example questions to ask

### External Resources
- [Semantic Kernel Documentation](https://learn.microsoft.com/en-us/semantic-kernel/)
- [Azure AI Foundry Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/)
- [Azure OpenAI Documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

---

## 🎓 Next Steps After Reading

1. **Deploy the system** (if not already done)
   - Follow [Deployment Guide](./DeploymentGuide.md)
   
2. **Make your first customization**
   - Try a 5-minute change
   - Test and verify it works
   
3. **Build understanding progressively**
   - Start with simple changes
   - Read workflow guide sections as needed
   - Gradually tackle more complex customizations
   
4. **Share your learnings**
   - Document your use case
   - Contribute improvements
   - Help others in the community

---

## 📮 Feedback

Found something unclear? Have suggestions? 

- [Submit an issue](https://github.com/microsoft/Multi-Agent-Custom-Automation-Engine-Solution-Accelerator/issues)
- Include which guide and section
- Describe what was confusing
- Suggest improvements

---

**Happy learning and customizing!** 🎉

Remember: Start small, test often, and refer back to these guides whenever you need guidance.
