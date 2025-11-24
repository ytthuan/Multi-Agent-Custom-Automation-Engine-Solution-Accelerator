# Customization Quick Start Guide

This guide provides quick, practical examples for customizing the Multi-Agent Custom Automation Engine. Perfect for users who want to jump straight into making changes.

## Table of Contents

1. [5-Minute Changes](#5-minute-changes)
2. [15-Minute Changes](#15-minute-changes)
3. [Advanced Customizations](#advanced-customizations)
4. [Common Recipes](#common-recipes)

---

## 5-Minute Changes

### Change Agent Personality

**Goal**: Make an agent more friendly or formal

**File**: `data/agent_teams/hr.json` (or your team JSON)

**What to change**:
```json
{
  "name": "HRHelperAgent",
  "system_message": "You are a friendly and casual HR assistant. Use emojis and conversational language. Make employees feel welcome and comfortable. 😊"
}
```

**Personality options**:
- **Formal**: "You are a professional HR representative. Use formal language and proper titles."
- **Friendly**: "You are a helpful buddy. Be warm, use emojis, and keep things casual."
- **Technical**: "You are a technical expert. Use precise terminology and be detail-oriented."
- **Concise**: "You are efficient and brief. Give short, direct answers without unnecessary elaboration."

---

### Add a Starting Task

**Goal**: Add a quick-start button for common tasks

**File**: `data/agent_teams/hr.json`

**What to change**:
```json
{
  "starting_tasks": [
    {
      "id": "task-1",
      "name": "Onboard New Employee",
      "prompt": "Please onboard our new employee Jessica Smith"
    },
    {
      "id": "task-2",
      "name": "Request Time Off",
      "prompt": "I need to request 3 days of vacation next week"
    },
    {
      "id": "task-3",
      "name": "Update Benefits",
      "prompt": "Help me update my health insurance enrollment"
    }
  ]
}
```

**Result**: Users see these as clickable shortcuts in the UI

---

### Change Team Name and Description

**Goal**: Customize team branding

**File**: `data/agent_teams/hr.json`

**What to change**:
```json
{
  "name": "My Company HR & Support",
  "description": "Your one-stop team for all employee needs - from HR questions to IT support"
}
```

---

## 15-Minute Changes

### Create a New Simple Agent

**Goal**: Add a new agent to handle a specific task domain

**File**: `data/agent_teams/hr.json`

**Step 1**: Add agent to the team's agents array:
```json
{
  "agents": [
    {
      "name": "FacilitiesAgent",
      "deployment_name": "gpt-4.1-mini",
      "icon": "",
      "system_message": "You are a facilities management assistant. Help with workspace requests, building access, parking, and office equipment. Always be helpful and responsive to employee needs.",
      "description": "Handles facilities and workspace management",
      "use_rag": false,
      "use_mcp": true,
      "use_bing": false,
      "use_reasoning": false,
      "coding_tools": false
    }
  ]
}
```

**Step 2**: Restart the application

**Step 3**: Test with: "I need to request a parking space"

---

### Create a Completely New Team

**Goal**: Add a new specialized team for a different department

**Step 1**: Create new file `data/agent_teams/sales.json`

```json
{
  "id": "4",
  "team_id": "00000000-0000-0000-0000-000000000004",
  "name": "Sales Team",
  "status": "visible",
  "created": "",
  "created_by": "",
  "agents": [
    {
      "name": "LeadQualificationAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You are a lead qualification specialist. Assess potential customers, gather information, and determine if they're a good fit for our products. Be professional but engaging.",
      "description": "Qualifies sales leads and gathers customer information",
      "use_rag": false,
      "use_mcp": true,
      "use_bing": false,
      "use_reasoning": false,
      "coding_tools": false
    },
    {
      "name": "ProposalAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You are a sales proposal expert. Create compelling proposals, quotes, and presentations. Highlight value and address customer needs.",
      "description": "Creates sales proposals and quotes",
      "use_rag": true,
      "use_mcp": true,
      "use_bing": false,
      "use_reasoning": false,
      "index_name": "sales-materials-index",
      "coding_tools": false
    },
    {
      "name": "ProxyAgent",
      "deployment_name": "",
      "system_message": "",
      "description": ""
    }
  ],
  "protected": false,
  "description": "Team focused on sales operations and customer engagement",
  "logo": "",
  "starting_tasks": [
    {
      "id": "task-1",
      "name": "Qualify New Lead",
      "prompt": "Qualify a new lead: Company XYZ Corp, interested in our enterprise solution"
    },
    {
      "id": "task-2",
      "name": "Create Proposal",
      "prompt": "Create a proposal for a 100-user enterprise license"
    }
  ]
}
```

**Step 2**: Load the new team (restart or dynamically load)

**Step 3**: Select "Sales Team" from team selector

**Step 4**: Test with starting tasks

---

### Enable Document Search (RAG)

**Goal**: Let agents search your company documents

**Prerequisites**:
- Azure AI Search service deployed
- Documents uploaded to search index

**Step 1**: Update agent configuration:

```json
{
  "name": "PolicyAgent",
  "deployment_name": "gpt-4.1-mini",
  "system_message": "You are a company policy expert. Search our policy documents to answer questions accurately. Always cite sources.",
  "use_rag": true,
  "index_name": "company-policies",
  "index_endpoint": "https://your-search.search.windows.net"
}
```

**Step 2**: Set environment variables:

```bash
AZURE_SEARCH_ENDPOINT=https://your-search.search.windows.net
AZURE_SEARCH_KEY=your-search-key
```

**Step 3**: Test with: "What is our remote work policy?"

**Agent will**:
1. Search document index
2. Find relevant policy documents
3. Answer based on actual documents
4. Cite sources

---

### Use Advanced Reasoning Model

**Goal**: Use o1-preview for complex problem-solving

**File**: `data/agent_teams/your-team.json`

**What to change**:
```json
{
  "name": "AnalystAgent",
  "deployment_name": "o1-preview",
  "system_message": "You are an expert analyst. Break down complex problems, consider multiple angles, and provide thorough analysis.",
  "use_rag": false,
  "use_mcp": false,
  "use_bing": false,
  "use_reasoning": true,
  "coding_tools": false
}
```

**Important constraints for o1 models**:
- ❌ Cannot use MCP tools (`use_mcp: false`)
- ❌ Cannot use Bing search (`use_bing: false`)
- ❌ Cannot use coding tools (`coding_tools: false`)
- ✅ Excellent for pure reasoning and analysis

**Best use cases**:
- Complex decision-making
- Multi-step reasoning
- Strategic planning
- Problem analysis

---

## Advanced Customizations

### Custom Tool Creation (MCP)

**Goal**: Create a custom tool that agents can use

**Example**: Create an "Approval Request" tool

**Step 1**: Define tool schema

Create/edit your MCP server configuration:

```json
{
  "tools": [
    {
      "name": "submit_approval_request",
      "description": "Submit a request for manager approval",
      "inputSchema": {
        "type": "object",
        "properties": {
          "request_type": {
            "type": "string",
            "enum": ["vacation", "expense", "purchase", "other"],
            "description": "Type of approval needed"
          },
          "details": {
            "type": "string",
            "description": "Details of the request"
          },
          "amount": {
            "type": "number",
            "description": "Amount if applicable (for expenses/purchases)"
          },
          "manager_email": {
            "type": "string",
            "description": "Manager's email address"
          }
        },
        "required": ["request_type", "details", "manager_email"]
      }
    }
  ]
}
```

**Step 2**: Implement tool logic

In your MCP server (e.g., `src/mcp_server/tools/approval.py`):

```python
async def submit_approval_request(
    request_type: str,
    details: str,
    manager_email: str,
    amount: float = None
) -> dict:
    """Submit approval request to manager"""
    
    # Create approval record
    approval_id = str(uuid.uuid4())
    
    # Send email to manager
    await send_email(
        to=manager_email,
        subject=f"Approval Request: {request_type}",
        body=f"Details: {details}\nAmount: ${amount if amount else 'N/A'}"
    )
    
    # Store in database
    await db.store_approval_request({
        "id": approval_id,
        "type": request_type,
        "details": details,
        "amount": amount,
        "status": "pending",
        "manager": manager_email
    })
    
    return {
        "success": True,
        "approval_id": approval_id,
        "message": f"Approval request sent to {manager_email}"
    }
```

**Step 3**: Enable in agent configuration:

```json
{
  "name": "HRHelperAgent",
  "use_mcp": true
}
```

**Step 4**: Test:
"I need to request vacation for next week. My manager is john@company.com"

---

### Conditional Auto-Approval

**Goal**: Auto-approve simple, low-risk tasks

**File**: `src/backend/v3/orchestration/human_approval_manager.py`

**Step 1**: Add approval logic

```python
class HumanApprovalMagenticManager:
    
    def __init__(self):
        self.auto_approve_keywords = [
            "lookup", "check", "view", "show", "display",
            "what is", "how many", "list"
        ]
        self.never_auto_approve = [
            "delete", "remove", "cancel", "terminate"
        ]
    
    async def request_approval(self, plan: str) -> bool:
        """Request approval with conditional auto-approve"""
        
        # Extract task from plan
        task_lower = plan.lower()
        
        # Never auto-approve dangerous actions
        if any(word in task_lower for word in self.never_auto_approve):
            return await self._request_user_approval(plan)
        
        # Auto-approve simple read-only tasks
        if any(word in task_lower for word in self.auto_approve_keywords):
            logger.info(f"Auto-approving read-only task: {plan}")
            return True
        
        # Check if task affects single vs. multiple records
        if self._affects_multiple_records(plan):
            return await self._request_user_approval(plan)
        
        # Default: request approval
        return await self._request_user_approval(plan)
    
    def _affects_multiple_records(self, plan: str) -> bool:
        """Check if plan affects multiple records"""
        multi_keywords = ["all", "every", "batch", "bulk", "multiple"]
        return any(word in plan.lower() for word in multi_keywords)
```

**Step 2**: Configure per agent:

```json
{
  "name": "ReadOnlyAgent",
  "system_message": "You only perform read operations. Never modify data.",
  "auto_approve": true
}
```

---

### Multi-Language Support

**Goal**: Support users in different languages

**Step 1**: Update agent system message:

```json
{
  "system_message": "You are a multilingual HR assistant. Detect the user's language and respond in that language. Supported languages: English, Spanish, French, German, Japanese."
}
```

**Step 2**: Add language detection (optional):

```python
from langdetect import detect

def get_user_language(message: str) -> str:
    """Detect language from user message"""
    try:
        return detect(message)
    except:
        return "en"  # Default to English
```

**Step 3**: Configure language-specific system messages:

```json
{
  "system_message_translations": {
    "en": "You are an HR assistant...",
    "es": "Eres un asistente de recursos humanos...",
    "fr": "Vous êtes un assistant RH...",
    "de": "Sie sind ein HR-Assistent...",
    "ja": "あなたは人事アシスタントです..."
  }
}
```

---

## Common Recipes

### Recipe 1: Customer Support Team

**Use case**: Handle customer inquiries and support tickets

```json
{
  "team_id": "customer-support",
  "name": "Customer Support Team",
  "agents": [
    {
      "name": "TechnicalSupportAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You troubleshoot technical issues. Be patient, ask diagnostic questions, and provide step-by-step solutions.",
      "use_rag": true,
      "use_mcp": true,
      "index_name": "support-docs"
    },
    {
      "name": "BillingSupportAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You handle billing inquiries and payment issues. Be clear about charges and helpful with resolution.",
      "use_mcp": true
    },
    {
      "name": "EscalationAgent",
      "deployment_name": "gpt-4.1",
      "system_message": "You handle escalated issues requiring manager attention. Assess urgency and route appropriately.",
      "use_mcp": true
    },
    {
      "name": "ProxyAgent"
    }
  ]
}
```

---

### Recipe 2: Data Analysis Team

**Use case**: Analyze data and generate insights

```json
{
  "team_id": "data-analysis",
  "name": "Data Analysis Team",
  "agents": [
    {
      "name": "DataCollectorAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You gather data from various sources. Use available tools to fetch data systematically.",
      "use_mcp": true,
      "coding_tools": true
    },
    {
      "name": "AnalystAgent",
      "deployment_name": "o1-preview",
      "system_message": "You analyze data and identify patterns. Provide thorough statistical analysis and insights.",
      "use_reasoning": true
    },
    {
      "name": "VisualizationAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You create charts and visualizations. Generate Python code for matplotlib/plotly visualizations.",
      "coding_tools": true
    },
    {
      "name": "ProxyAgent"
    }
  ]
}
```

---

### Recipe 3: Content Creation Team

**Use case**: Create marketing and content materials

```json
{
  "team_id": "content-creation",
  "name": "Content Creation Team",
  "agents": [
    {
      "name": "ResearchAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You research topics using available resources. Gather facts, statistics, and relevant information.",
      "use_rag": true,
      "use_bing": true,
      "index_name": "brand-guidelines"
    },
    {
      "name": "WriterAgent",
      "deployment_name": "gpt-4.1",
      "system_message": "You write engaging content. Follow brand voice and SEO best practices. Create compelling copy.",
      "use_rag": true,
      "index_name": "brand-guidelines"
    },
    {
      "name": "EditorAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You review and refine content. Check grammar, clarity, tone, and brand consistency.",
      "use_rag": true
    },
    {
      "name": "ProxyAgent"
    }
  ]
}
```

---

### Recipe 4: Compliance & Audit Team

**Use case**: Ensure compliance and conduct audits

```json
{
  "team_id": "compliance",
  "name": "Compliance & Audit Team",
  "agents": [
    {
      "name": "PolicyCheckerAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You verify compliance with company policies and regulations. Reference official policy documents.",
      "use_rag": true,
      "index_name": "compliance-policies"
    },
    {
      "name": "AuditAgent",
      "deployment_name": "o1-preview",
      "system_message": "You conduct thorough audits. Analyze records for discrepancies, patterns, and compliance issues.",
      "use_reasoning": true
    },
    {
      "name": "ReportingAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You generate compliance reports. Create detailed documentation of findings and recommendations.",
      "use_mcp": true
    },
    {
      "name": "ProxyAgent"
    }
  ]
}
```

---

## Configuration Reference

### Agent Configuration Options

```json
{
  "name": "AgentName",                    // Required: Unique agent identifier
  "deployment_name": "gpt-4.1-mini",      // Required: Model to use
  "system_message": "...",                 // Required: Agent's instructions
  "description": "...",                    // Optional: Human-readable description
  
  // Capabilities
  "use_rag": false,                        // Enable document search
  "use_mcp": false,                        // Enable custom tools
  "use_bing": false,                       // Enable web search
  "use_reasoning": false,                  // Enable o1 reasoning mode
  "coding_tools": false,                   // Enable code execution
  
  // RAG Configuration (if use_rag: true)
  "index_name": "",                        // Search index name
  "index_endpoint": "",                    // Search endpoint URL
  "index_foundry_name": "",               // Foundry index (alternative)
  
  // UI Configuration
  "icon": "",                              // Agent icon URL
  "input_key": "",                         // Special input handling
  "type": ""                               // Agent type classification
}
```

### Supported Models

```json
[
  "gpt-4.1-mini",      // Fast, cost-effective, general purpose
  "gpt-4.1",           // Balanced, higher quality
  "o1-preview",        // Advanced reasoning, no tools
  "o1-mini"            // Reasoning with efficiency
]
```

### Model Selection Guide

| Use Case | Recommended Model | Why |
|----------|------------------|-----|
| Simple queries | gpt-4.1-mini | Fast and cost-effective |
| General tasks | gpt-4.1-mini | Good balance |
| Complex conversations | gpt-4.1 | Better understanding |
| Deep reasoning | o1-preview | Advanced logic |
| Analysis | o1-preview | Multi-step thinking |
| Cost-sensitive reasoning | o1-mini | Cheaper reasoning |

---

## Testing Your Changes

### Quick Test Checklist

1. **Syntax Check**: Validate JSON
   ```bash
   python -m json.tool data/agent_teams/your-team.json
   ```

2. **Restart Application**: Apply changes
   ```bash
   docker restart macae-backend
   ```

3. **Test Basic Interaction**:
   - Send simple message
   - Verify agent responds
   - Check agent uses correct tools

4. **Test Edge Cases**:
   - Missing information
   - Ambiguous requests
   - Multi-step tasks

5. **Monitor Logs**:
   ```bash
   docker logs -f macae-backend
   ```

### Common Test Scenarios

```
✅ "Hello" - Test basic response
✅ "What can you help me with?" - Test agent description
✅ "Process [specific task]" - Test main functionality
✅ "I need help with [vague request]" - Test clarification
✅ "[Complex multi-step task]" - Test orchestration
```

---

## Best Practices

### System Message Tips

1. **Be Specific**: Clear instructions produce better results
   ```
   ❌ "You are helpful"
   ✅ "You help with employee onboarding. Use tools to create records, provision equipment, and set up accounts."
   ```

2. **Set Boundaries**: Define what the agent should NOT do
   ```
   ✅ "Never delete records without explicit approval. Always verify information before making changes."
   ```

3. **Provide Context**: Help the agent understand its role
   ```
   ✅ "You are part of the HR team. You work with TechnicalSupportAgent to complete onboarding tasks."
   ```

4. **Include Examples**: Show desired behavior
   ```
   ✅ "When asked to onboard an employee, first create their record, then notify TechnicalSupportAgent for equipment provisioning."
   ```

### Agent Team Design

1. **Single Responsibility**: Each agent has clear, focused purpose
2. **Complementary Skills**: Agents cover different aspects
3. **Clear Handoffs**: Define when agents should involve others
4. **Always Include ProxyAgent**: For user clarification

### Configuration Management

1. **Version Control**: Keep agent configs in git
2. **Document Changes**: Add comments explaining modifications
3. **Test Before Deploy**: Verify changes in development first
4. **Backup Configs**: Save working configurations

---

## Troubleshooting Quick Fixes

### Agent Not Using Tools

**Check**:
1. `use_mcp: true` in configuration
2. MCP server is running
3. Tools are registered in MCP server
4. System message mentions tools

**Fix**:
```json
{
  "use_mcp": true,
  "system_message": "You have access to employee management tools. Use create_employee_record, update_employee_record, etc."
}
```

### Agent Giving Wrong Answers

**Check**:
1. System message is clear
2. Use RAG if answers need documents
3. Consider better model

**Fix**:
```json
{
  "deployment_name": "gpt-4.1",  // Upgrade model
  "use_rag": true,  // Enable document search
  "system_message": "Always search documents before answering policy questions. Never guess or make up answers."
}
```

### Slow Responses

**Check**:
1. Too many agents in team
2. Using o1-preview for simple tasks
3. RAG searching too many documents

**Fix**:
```json
{
  "deployment_name": "gpt-4.1-mini",  // Faster model
  "agents": [/* Keep only essential agents */]
}
```

---

## Getting Help

### Useful Resources

- **Main Documentation**: `/docs/MESSAGE_WORKFLOW_GUIDE.md`
- **Architecture**: `/docs/README.md`
- **Deployment**: `/docs/DeploymentGuide.md`
- **Troubleshooting**: `/docs/TroubleShootingSteps.md`

### Common Questions

**Q: Can I mix models in one team?**
A: Yes! Each agent can use a different model.

**Q: How many agents should I have?**
A: 2-5 specialist agents + ProxyAgent is ideal.

**Q: Can agents share tools?**
A: Yes, multiple agents can use the same tools.

**Q: Should I always request approval?**
A: Use approval for data modifications, skip for read-only operations.

---

## Next Steps

1. **Start Small**: Modify existing team first
2. **Test Thoroughly**: Verify each change works
3. **Iterate**: Refine based on results
4. **Expand**: Add more agents as needed
5. **Monitor**: Watch logs and user feedback

Happy customizing! 🎉
