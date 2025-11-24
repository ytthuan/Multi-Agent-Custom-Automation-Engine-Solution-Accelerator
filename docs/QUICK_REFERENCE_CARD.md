# Quick Reference Card

## 🚀 Getting Started in 5 Minutes

### Step 1: Choose Your Path
- **Understand First** → Read [MESSAGE_WORKFLOW_GUIDE.md](./MESSAGE_WORKFLOW_GUIDE.md)
- **Customize Now** → Try [CUSTOMIZATION_QUICK_START.md](./CUSTOMIZATION_QUICK_START.md)
- **See Visuals** → View [MESSAGE_FLOW_DIAGRAMS.md](./MESSAGE_FLOW_DIAGRAMS.md)
- **Navigate All** → Start [WORKFLOW_AND_CUSTOMIZATION_INDEX.md](./WORKFLOW_AND_CUSTOMIZATION_INDEX.md)

---

## 📍 Key File Locations

```
Message Flow:
└─ Frontend sends → src/backend/v3/api/router.py
   └─ Orchestrator → src/backend/v3/orchestration/orchestration_manager.py
      └─ Agents → src/backend/v3/magentic_agents/*.py
         └─ Results sent back via WebSocket

Agent Teams:
└─ Team configs → data/agent_teams/*.json

Main Entry:
└─ Application → src/backend/app_kernel.py
```

---

## ⚡ Common Tasks

### Change Agent Personality
**File**: `data/agent_teams/your-team.json`
```json
{
  "name": "AgentName",
  "system_message": "You are a friendly assistant..."
}
```

### Create New Team
**File**: `data/agent_teams/new-team.json`
```json
{
  "team_id": "unique-id",
  "name": "Team Name",
  "agents": [
    {
      "name": "Agent1",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "...",
      "use_mcp": true
    }
  ]
}
```

### Enable Document Search
```json
{
  "name": "AgentName",
  "use_rag": true,
  "index_name": "your-index"
}
```

---

## 🔧 API Endpoints

```
POST /api/v3/process_request   → Send user message
GET  /api/v3/init_team         → Initialize team
POST /api/v3/plan_approval     → Approve/reject plan
WS   /api/v3/socket/{id}       → Real-time updates
```

---

## 🎯 Message Flow (Simple)

```
User Input
    ↓
Validate (RAI Check)
    ↓
Create Plan
    ↓
Orchestration
    ↓
Generate Execution Plan
    ↓
User Approval
    ↓
Execute with Agents
    ↓
Return Results
```

---

## 🤖 Agent Types

| Type | Model | Use For | Tools |
|------|-------|---------|-------|
| Foundry | gpt-4.1-mini | General tasks | MCP, RAG, Bing |
| Foundry | gpt-4.1 | Complex tasks | MCP, RAG, Bing |
| Reasoning | o1-preview | Deep reasoning | None |
| Reasoning | o1-mini | Fast reasoning | None |
| Proxy | - | User interaction | None |

---

## 🔍 Troubleshooting Quick Checks

**Agent not responding?**
1. Check WebSocket connection
2. Verify team initialized
3. Check logs: `docker logs macae-backend`

**Wrong answers?**
1. Update system_message
2. Enable RAG if needed
3. Use better model (gpt-4.1)

**Slow responses?**
1. Use gpt-4.1-mini
2. Reduce number of agents
3. Optimize RAG search

---

## 📚 Documentation Map

```
WORKFLOW_AND_CUSTOMIZATION_INDEX.md
    ├─ MESSAGE_WORKFLOW_GUIDE.md (Complete understanding)
    │   ├─ System Architecture
    │   ├─ Step-by-Step Journey
    │   ├─ Key Components
    │   ├─ Customization Guide
    │   └─ Troubleshooting
    │
    ├─ CUSTOMIZATION_QUICK_START.md (Practical changes)
    │   ├─ 5-Minute Changes
    │   ├─ 15-Minute Changes
    │   ├─ Advanced Customizations
    │   └─ Common Recipes
    │
    ├─ MESSAGE_FLOW_DIAGRAMS.md (Visual learning)
    │   ├─ Phase-by-Phase Flows
    │   ├─ Component Interactions
    │   └─ File Navigation Map
    │
    └─ QUICK_REFERENCE_CARD.md (This file!)
```

---

## 💡 Quick Tips

✅ **Always backup** configurations before changing
✅ **Test in dev** before deploying to production
✅ **Start small** with 5-minute changes
✅ **Use recipes** as templates
✅ **Check logs** when debugging
✅ **Read system messages** carefully - they guide agent behavior

❌ **Don't** make multiple changes at once
❌ **Don't** skip testing
❌ **Don't** modify production without validation
❌ **Don't** ignore error messages

---

## 🎓 Learning Time Estimates

| Goal | Time | Start Here |
|------|------|------------|
| Quick overview | 10 min | Diagrams → Quick Overview |
| Understand system | 45 min | Workflow Guide → Architecture |
| Make first change | 15 min | Quick Start → 5-Min Changes |
| Create new team | 30 min | Quick Start → Common Recipes |
| Deep customization | 2-4 hrs | All guides |

---

## 🆘 Need Help?

1. **Check Troubleshooting** sections in guides
2. **Search docs** with Ctrl+F for keywords
3. **Review examples** similar to your use case
4. **Check logs** for error details
5. **File issue** on GitHub with details

---

## 📝 Configuration Checklist

Before deploying customizations:

- [ ] JSON syntax validated
- [ ] Agent names are unique
- [ ] System messages are clear
- [ ] Tools are configured (use_mcp, use_rag)
- [ ] Model names are correct
- [ ] Team tested with sample tasks
- [ ] Logs checked for errors
- [ ] Backup of working config saved

---

## 🔗 Quick Links

- [Main README](../README.md)
- [Deployment Guide](./DeploymentGuide.md)
- [Troubleshooting Steps](./TroubleShootingSteps.md)
- [Sample Questions](./SampleQuestions.md)

---

## 📞 Support

- **Documentation Issues**: File GitHub issue
- **Deployment Help**: See DeploymentGuide.md
- **Customization Questions**: Check CUSTOMIZATION_QUICK_START.md
- **Understanding System**: Read MESSAGE_WORKFLOW_GUIDE.md

---

**Print this card** for quick reference while working! 📋

**Version**: 1.0 | **Last Updated**: 2024-11-24
