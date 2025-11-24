# Message Workflow Guide

## Overview

This guide explains how messages flow through the Multi-Agent Custom Automation Engine system from the moment a user sends a message until they receive a response. This documentation is designed for users with limited coding experience who want to understand and customize the system.

## Table of Contents

1. [System Architecture Overview](#system-architecture-overview)
2. [Complete Message Workflow](#complete-message-workflow)
3. [Key Components Explained](#key-components-explained)
4. [Step-by-Step Message Journey](#step-by-step-message-journey)
5. [Customization Guide](#customization-guide)
6. [Common Scenarios](#common-scenarios)
7. [Troubleshooting](#troubleshooting)

---

## System Architecture Overview

The Multi-Agent Custom Automation Engine is built using several key technologies:

- **Frontend**: User interface where users interact with the system
- **Backend API**: FastAPI-based server that handles requests (located in `src/backend/`)
- **WebSocket**: Real-time communication channel between frontend and backend
- **Agents**: AI-powered workers that perform specific tasks
- **Database**: Azure Cosmos DB that stores plans, tasks, and user data
- **Azure OpenAI**: Powers the intelligent agents

```
┌─────────────┐
│   Frontend  │ (User Interface)
│   (React)   │
└──────┬──────┘
       │ HTTP/WebSocket
       ▼
┌─────────────┐
│   Backend   │ (FastAPI)
│   API       │
└──────┬──────┘
       │
       ├─────────────┐
       ▼             ▼
┌─────────────┐  ┌─────────────┐
│ Orchestrator│  │   Database  │
│  Manager    │  │  (Cosmos)   │
└──────┬──────┘  └─────────────┘
       │
       ▼
┌─────────────┐
│   Agents    │ (AI Workers)
│  Team       │
└─────────────┘
```

---

## Complete Message Workflow

When you send a message to the system, here's what happens at a high level:

1. **User sends a message** → Frontend captures the message
2. **Message validation** → System checks for safety and appropriateness
3. **Team initialization** → System ensures the correct agent team is ready
4. **Plan creation** → System creates a plan with a unique ID
5. **Orchestration** → Agents work together to process the request
6. **Plan generation** → A detailed execution plan is created
7. **User approval** → User reviews and approves/rejects the plan
8. **Execution** → Agents execute the approved plan
9. **Response delivery** → Results are sent back to the user

---

## Key Components Explained

### 1. **Frontend (User Interface)**
- **Location**: `src/frontend/`
- **Purpose**: Where users type messages and see responses
- **Technology**: React-based web application

### 2. **Backend API Entry Point**
- **Location**: `src/backend/app_kernel.py`
- **Purpose**: Main application server that receives all requests
- **Key Routes**:
  - `/api/v3/init_team` - Initialize agent teams
  - `/api/v3/process_request` - Process user messages
  - `/api/v3/plan_approval` - Handle plan approvals

### 3. **Router (API Endpoints)**
- **Location**: `src/backend/v3/api/router.py`
- **Purpose**: Defines all API endpoints and routes requests
- **Key Functions**:
  - `start_comms()` - Establishes WebSocket connection
  - `init_team()` - Initializes agent team for a user
  - `process_request()` - Main endpoint for processing user messages
  - `plan_approval()` - Handles user's approval/rejection of plans

### 4. **Orchestration Manager**
- **Location**: `src/backend/v3/orchestration/orchestration_manager.py`
- **Purpose**: Coordinates multiple agents to work together
- **Key Functions**:
  - `init_orchestration()` - Sets up the agent team
  - `run_orchestration()` - Executes the multi-agent workflow

### 5. **Agent Factory**
- **Location**: `src/backend/v3/magentic_agents/magentic_agent_factory.py`
- **Purpose**: Creates and configures individual agents
- **Agent Types**:
  - **Foundry Agent**: Standard AI agent with tools (GPT-4 models)
  - **Reasoning Agent**: Advanced reasoning agent (O-series models)
  - **Proxy Agent**: Special agent that asks users for clarification

### 6. **Agent Teams Configuration**
- **Location**: `data/agent_teams/*.json`
- **Purpose**: Defines agent teams and their capabilities
- **Examples**:
  - `hr.json` - Human Resources team
  - `retail.json` - Retail operations team

### 7. **Database**
- **Location**: Database access via `common/database/database_factory.py`
- **Purpose**: Stores plans, user data, team configurations
- **Data Stored**:
  - User sessions
  - Plans and their status
  - Team configurations
  - Task history

---

## Step-by-Step Message Journey

Let's follow a message through the entire system with a real example:

**Example**: User sends "Please onboard our new employee Jessica Smith"

### Phase 1: Connection Setup

#### Step 1: WebSocket Connection Established
```
File: src/backend/v3/api/router.py
Function: start_comms()
```

**What happens:**
- Frontend opens a WebSocket connection to the backend
- Backend assigns a unique `process_id` to track this conversation
- Connection is stored in `connection_config` for later communication
- The WebSocket stays open to send real-time updates to the user

**Technical details:**
```python
@app_v3.websocket("/socket/{process_id}")
async def start_comms(websocket: WebSocket, process_id: str, user_id: str):
    await websocket.accept()  # Accept the connection
    connection_config.add_connection(process_id, connection=websocket, user_id=user_id)
    # Keep connection open for updates
```

#### Step 2: Team Initialization
```
File: src/backend/v3/api/router.py
Function: init_team()
```

**What happens:**
- System checks which agent team the user should use
- If user has no team, defaults to HR team (ID: 00000000-0000-0000-0000-000000000001)
- Retrieves team configuration from database
- Creates agent instances based on team configuration
- Stores the initialized orchestration for this user

**Configuration example (from `data/agent_teams/hr.json`):**
```json
{
  "team_id": "team-1",
  "name": "Human Resources Team",
  "agents": [
    {
      "name": "HRHelperAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You have access to HR tools...",
      "use_mcp": true
    },
    {
      "name": "TechnicalSupportAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You have access to technical support tools...",
      "use_mcp": true
    },
    {
      "name": "ProxyAgent"
    }
  ]
}
```

**Agent creation flow:**
1. Read team configuration from JSON file
2. For each agent in the configuration:
   - Check agent type (Foundry, Reasoning, or Proxy)
   - Create agent with specified tools and capabilities
   - Configure system message and behavior
3. Bundle agents into an orchestration team

### Phase 2: Message Processing

#### Step 3: User Message Received
```
File: src/backend/v3/api/router.py
Function: process_request()
```

**What happens:**
- User's message arrives at the `/api/v3/process_request` endpoint
- Message is wrapped in an `InputTask` object with metadata:
  ```python
  {
    "description": "Please onboard our new employee Jessica Smith",
    "session_id": "unique-session-id",
    "user_id": "user-principal-id"
  }
  ```

#### Step 4: Safety Validation (RAI Check)
```
File: common/utils/utils_kernel.py
Function: rai_success()
```

**What happens:**
- System checks the message for inappropriate content
- Uses Azure AI Content Safety service
- If message fails safety check, request is rejected
- If message passes, processing continues

**Example check:**
```python
if not await rai_success(input_task.description):
    raise HTTPException(
        status_code=400,
        detail="Request contains content that doesn't meet our safety guidelines"
    )
```

#### Step 5: Plan Creation
```
File: src/backend/v3/api/router.py
Function: process_request()
```

**What happens:**
- System creates a new Plan object in the database
- Plan includes:
  - Unique plan ID
  - User ID
  - Team ID
  - Initial goal (user's message)
  - Status: "in_progress"
- Plan is saved to Cosmos DB for tracking

**Plan structure:**
```python
plan = Plan(
    id=plan_id,
    plan_id=plan_id,
    user_id=user_id,
    session_id=session_id,
    team_id=team_id,
    initial_goal="Please onboard our new employee Jessica Smith",
    overall_status=PlanStatus.in_progress
)
```

### Phase 3: Orchestration

#### Step 6: Background Task Started
```
File: src/backend/v3/api/router.py
Function: process_request()
```

**What happens:**
- Main API endpoint returns immediately with plan_id
- Actual processing happens in background task
- This allows the API to stay responsive

```python
background_tasks.add_task(OrchestrationManager().run_orchestration, user_id, input_task)
return {"status": "Request started successfully", "plan_id": plan_id}
```

#### Step 7: Orchestration Execution
```
File: src/backend/v3/orchestration/orchestration_manager.py
Function: run_orchestration()
```

**What happens:**
- Retrieves the user's initialized orchestration (agent team)
- Creates a runtime environment for agents to communicate
- Sends the user's task to the orchestration
- Agents begin working together

**Agent collaboration process:**
```
1. Orchestrator receives task: "Please onboard our new employee Jessica Smith"
2. Orchestrator analyzes which agents are needed
3. Orchestrator creates a multi-step plan
4. Plan is sent to user for approval (via WebSocket)
```

#### Step 8: Plan Generation and Approval Request
```
File: src/backend/v3/orchestration/human_approval_manager.py
Function: request_approval()
```

**What happens:**
- Orchestrator creates a detailed execution plan
- Plan is formatted with steps and responsible agents
- Plan is sent to frontend via WebSocket
- System waits for user approval

**Example plan sent to user:**
```json
{
  "type": "PLAN_APPROVAL_REQUIRED",
  "data": {
    "m_plan_id": "internal-plan-id",
    "plan_id": "user-facing-plan-id",
    "steps": [
      {
        "step": 1,
        "agent": "HRHelperAgent",
        "action": "Create employee record for Jessica Smith"
      },
      {
        "step": 2,
        "agent": "TechnicalSupportAgent",
        "action": "Provision laptop for Jessica Smith"
      },
      {
        "step": 3,
        "agent": "TechnicalSupportAgent",
        "action": "Set up email account for Jessica Smith"
      }
    ]
  }
}
```

**User sees this plan in the frontend and can:**
- ✅ Approve - Click to proceed with execution
- ❌ Reject - Cancel the plan
- ✏️ Provide feedback - Suggest modifications

### Phase 4: User Approval

#### Step 9: User Approval Response
```
File: src/backend/v3/api/router.py
Function: plan_approval()
```

**What happens when user clicks "Approve":**
- Frontend sends approval to `/api/v3/plan_approval` endpoint
- Request includes:
  ```python
  {
    "m_plan_id": "internal-plan-id",
    "approved": true,
    "feedback": ""  # Optional user comments
  }
  ```
- System records approval in orchestration_config
- Waiting orchestration is unblocked and continues execution

**What happens when user clicks "Reject":**
- Same endpoint but with `"approved": false`
- Orchestration is notified of rejection
- System may ask for feedback to create a new plan

**Technical implementation:**
```python
orchestration_config.set_approval_result(
    m_plan_id=human_feedback.m_plan_id,
    approved=human_feedback.approved,
    feedback=human_feedback.feedback
)
```

### Phase 5: Execution

#### Step 10: Agent Execution
```
File: src/backend/v3/magentic_agents/foundry_agent.py
Function: process()
```

**What happens:**
- Each agent executes their assigned tasks in order
- Agents can use tools (MCP, RAG, etc.) to complete tasks
- Each agent action is sent to frontend via WebSocket

**Agent execution flow:**

**Step 10a: HRHelperAgent Creates Employee Record**
- Agent receives task: "Create employee record for Jessica Smith"
- Agent calls MCP tool: `create_employee_record(name="Jessica Smith")`
- Tool executes and returns result
- Result sent to user via WebSocket:
  ```json
  {
    "type": "AGENT_TOOL_MESSAGE",
    "data": {
      "agent_name": "HRHelperAgent",
      "tool_calls": [
        {
          "tool_name": "create_employee_record",
          "arguments": {"name": "Jessica Smith"}
        }
      ]
    }
  }
  ```

**Step 10b: TechnicalSupportAgent Provisions Laptop**
- Agent receives task: "Provision laptop for Jessica Smith"
- Agent calls MCP tool: `provision_laptop(employee="Jessica Smith")`
- Tool executes and returns result
- Result sent to user

**Step 10c: TechnicalSupportAgent Sets Up Email**
- Agent receives task: "Set up email account for Jessica Smith"
- Agent calls MCP tool: `create_email_account(employee="Jessica Smith")`
- Tool executes and returns result

#### Step 11: Response Callbacks
```
File: src/backend/v3/callbacks/response_handlers.py
Functions: agent_response_callback(), streaming_agent_response_callback()
```

**What happens:**
- Every agent action triggers a callback
- Callbacks format messages for the frontend
- Messages are sent via WebSocket connection

**Two types of messages:**

1. **Tool Call Messages**: When agent uses a tool
   ```python
   AgentToolMessage(
       agent_name="HRHelperAgent",
       tool_calls=[{"tool_name": "create_employee_record", "arguments": {...}}]
   )
   ```

2. **Text Messages**: When agent communicates results
   ```python
   AgentMessage(
       agent_name="HRHelperAgent",
       content="Successfully created employee record for Jessica Smith",
       timestamp=time.time()
   )
   ```

**Messages are sent via WebSocket:**
```python
await connection_config.send_status_update_async(
    message,
    user_id,
    message_type=WebsocketMessageType.AGENT_MESSAGE
)
```

### Phase 6: Completion

#### Step 12: Final Result
```
File: src/backend/v3/orchestration/orchestration_manager.py
Function: run_orchestration()
```

**What happens:**
- All agents have completed their tasks
- Orchestrator compiles final result
- Final result sent to user via WebSocket
- Plan status updated in database to "completed"

**Final result message:**
```json
{
  "type": "FINAL_RESULT_MESSAGE",
  "data": {
    "content": "Employee onboarding completed successfully for Jessica Smith. Created employee record, provisioned laptop, and set up email account.",
    "status": "completed",
    "timestamp": 1234567890
  }
}
```

#### Step 13: Cleanup
```
File: src/backend/v3/orchestration/orchestration_manager.py
Function: run_orchestration()
```

**What happens:**
- Runtime is stopped
- Resources are released
- WebSocket connection remains open for next message
- Orchestration stays in memory for continued conversation

---

## Key Components Explained (Deep Dive)

### WebSocket Communication

**Purpose**: Real-time, bidirectional communication between frontend and backend

**Location**: `src/backend/v3/config/settings.py` (ConnectionManager)

**How it works:**
1. Frontend establishes WebSocket connection
2. Backend stores connection with user_id and process_id
3. Backend can send updates at any time without frontend requesting
4. Messages are JSON formatted

**Message types:**
- `AGENT_MESSAGE` - Agent text responses
- `AGENT_TOOL_MESSAGE` - Agent tool usage
- `PLAN_APPROVAL_REQUIRED` - Plan needs user approval
- `FINAL_RESULT_MESSAGE` - Task completed

### Orchestration

**Purpose**: Coordinate multiple agents to work together on complex tasks

**Key concept**: Instead of a single AI handling everything, multiple specialized agents collaborate

**How agent collaboration works:**

1. **Manager Agent** (Human Approval Manager)
   - Decides which agents to involve
   - Creates the overall plan
   - Coordinates execution order

2. **Specialist Agents** (HRHelper, TechnicalSupport, etc.)
   - Each handles specific domain tasks
   - Has access to domain-specific tools
   - Reports results back to manager

3. **Proxy Agent**
   - Special agent that asks users for clarification
   - Used when information is missing
   - Bridges human-AI communication

**Example collaboration:**
```
User: "Onboard Jessica Smith"
└─→ Manager: Analyzes task, creates plan
    ├─→ HRHelperAgent: Create employee record
    ├─→ TechnicalSupportAgent: Provision laptop
    └─→ TechnicalSupportAgent: Set up email
```

### Agent Tools (MCP)

**MCP** = Model Context Protocol

**Purpose**: Allows agents to perform actions beyond text generation

**Tool types:**
- Database operations (create, read, update records)
- API calls (external services)
- File operations (read, write files)
- Custom business logic

**Example tool definition:**
```json
{
  "name": "create_employee_record",
  "description": "Creates a new employee record in the HR system",
  "parameters": {
    "name": "string",
    "email": "string",
    "department": "string"
  }
}
```

**Tool execution flow:**
1. Agent decides a tool is needed
2. Agent generates tool call with parameters
3. System executes tool
4. Result returned to agent
5. Agent incorporates result into response

---

## Customization Guide

This section explains how to customize the system for your specific use case.

### 1. Creating a New Agent Team

**Scenario**: You want to create a "Finance Team" for financial tasks

**Step 1: Create team configuration file**

Create new file: `data/agent_teams/finance.json`

```json
{
  "id": "3",
  "team_id": "00000000-0000-0000-0000-000000000003",
  "name": "Finance Team",
  "status": "visible",
  "agents": [
    {
      "name": "AccountingAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You are an accounting expert with access to financial tools. Help with invoicing, expense reports, and financial analysis.",
      "description": "Handles accounting and bookkeeping tasks",
      "use_rag": false,
      "use_mcp": true,
      "use_bing": false,
      "coding_tools": false
    },
    {
      "name": "AuditAgent",
      "deployment_name": "gpt-4.1-mini",
      "system_message": "You are an audit specialist. Review financial records for compliance and accuracy.",
      "description": "Performs financial audits and compliance checks",
      "use_rag": false,
      "use_mcp": true,
      "use_bing": false,
      "coding_tools": false
    },
    {
      "name": "ProxyAgent",
      "deployment_name": "",
      "system_message": "",
      "description": ""
    }
  ],
  "description": "Team focused on financial operations and accounting",
  "starting_tasks": [
    {
      "id": "task-1",
      "name": "Process Invoice",
      "prompt": "Process invoice for vendor XYZ Corp"
    }
  ]
}
```

**Step 2: Register team in database**

The team will be automatically loaded when the system starts. You can also manually add it via the database interface.

**Step 3: Test your team**

1. Start the application
2. Select "Finance Team" from the team selector
3. Send a test message: "Process invoice for vendor XYZ Corp"

### 2. Modifying Agent Behavior

**Scenario**: You want the HRHelperAgent to be more formal in communication

**Step 1: Locate agent configuration**

File: `data/agent_teams/hr.json`

**Step 2: Modify system_message**

```json
{
  "name": "HRHelperAgent",
  "system_message": "You are a professional HR representative. Always maintain a formal and courteous tone. Use proper titles and formal language when addressing employees. You have access to HR tools for onboarding, benefits, and policies. When you need additional information, politely ask the ProxyAgent for clarification rather than making assumptions."
}
```

**Key elements in system_message:**
- **Tone**: Formal, casual, technical, friendly
- **Behavior**: How to handle missing information
- **Constraints**: What not to do
- **Tools**: What capabilities the agent has

### 3. Adding Custom Tools (MCP)

**Scenario**: You want to add a "Send Welcome Email" tool for onboarding

**Step 1: Define the tool**

Tools are typically defined in MCP server configuration. The exact location depends on your MCP setup.

Example tool definition:
```json
{
  "name": "send_welcome_email",
  "description": "Sends a welcome email to a new employee",
  "inputSchema": {
    "type": "object",
    "properties": {
      "employee_name": {
        "type": "string",
        "description": "Full name of the employee"
      },
      "employee_email": {
        "type": "string",
        "description": "Email address of the employee"
      },
      "start_date": {
        "type": "string",
        "description": "Employee start date"
      }
    },
    "required": ["employee_name", "employee_email"]
  }
}
```

**Step 2: Update agent configuration**

Ensure the agent has `"use_mcp": true` in their configuration:

```json
{
  "name": "HRHelperAgent",
  "use_mcp": true
}
```

**Step 3: Tool implementation**

Implement the actual tool logic in your MCP server (typically in `src/mcp_server/`)

### 4. Modifying the Approval Process

**Scenario**: You want to skip approval for simple tasks

**Step 1: Locate approval manager**

File: `src/backend/v3/orchestration/human_approval_manager.py`

**Step 2: Add conditional logic**

You can modify the `request_approval()` method to add conditions:

```python
async def request_approval(self, plan: str, context: dict) -> bool:
    # Check if task is simple
    if self._is_simple_task(context.get("task")):
        # Auto-approve simple tasks
        return True
    
    # Request approval for complex tasks
    return await self._request_user_approval(plan)

def _is_simple_task(self, task: str) -> bool:
    """Determine if a task is simple enough to auto-approve"""
    simple_keywords = ["lookup", "check", "view", "show"]
    return any(keyword in task.lower() for keyword in simple_keywords)
```

**Warning**: Be cautious with auto-approval. Always ensure critical operations require human oversight.

### 5. Adding RAG (Retrieval Augmented Generation)

**Scenario**: You want agents to search your company documents

**Step 1: Enable RAG in agent configuration**

```json
{
  "name": "HRHelperAgent",
  "use_rag": true,
  "index_name": "company-policies-index",
  "index_endpoint": "https://your-search-service.search.windows.net"
}
```

**Step 2: Set up Azure AI Search**

1. Create Azure AI Search service
2. Upload documents to search index
3. Configure search endpoint in environment variables

**Step 3: Test RAG capability**

Send a message that requires document lookup:
"What is our vacation policy?"

Agent will:
1. Search the document index
2. Find relevant policy documents
3. Use the information to answer your question

### 6. Changing AI Models

**Scenario**: You want to use a more powerful model for complex reasoning

**Step 1: Update agent configuration**

```json
{
  "name": "ComplexReasoningAgent",
  "deployment_name": "o1-preview",
  "system_message": "You are an expert in complex problem solving..."
}
```

**Available models** (check `config.SUPPORTED_MODELS`):
- `gpt-4.1-mini` - Fast, cost-effective
- `gpt-4.1` - Balanced performance
- `o1-preview` - Advanced reasoning
- `o1-mini` - Reasoning with efficiency

**Note**: O-series models (o1-preview, o1-mini) cannot use certain tools. They are ReasoningAgents and have constraints:
- Cannot use Bing search
- Cannot use coding tools
- Best for pure reasoning tasks

### 7. Customizing Message Responses

**Scenario**: You want to add emojis or custom formatting to agent responses

**Step 1: Locate response handler**

File: `src/backend/v3/callbacks/response_handlers.py`

**Step 2: Modify message formatting**

```python
def agent_response_callback(message: ChatMessageContent, user_id: str = None):
    agent_name = message.name or "Unknown Agent"
    
    # Add emoji based on agent type
    emoji = {
        "HRHelperAgent": "👤",
        "TechnicalSupportAgent": "🔧",
        "ProxyAgent": "💬"
    }.get(agent_name, "🤖")
    
    # Format message with emoji
    formatted_content = f"{emoji} {clean_citations(message.content)}"
    
    final_message = AgentMessage(
        agent_name=agent_name,
        content=formatted_content,
        timestamp=time.time()
    )
    # Send formatted message...
```

### 8. Adding New API Endpoints

**Scenario**: You want to add an endpoint to check task history

**Step 1: Add endpoint to router**

File: `src/backend/v3/api/router.py`

```python
@app_v3.get("/task_history")
async def get_task_history(request: Request, limit: int = 10):
    """Get user's task history"""
    authenticated_user = get_authenticated_user_details(request.headers)
    user_id = authenticated_user["user_principal_id"]
    
    # Get database
    memory_store = await DatabaseFactory.get_database(user_id=user_id)
    
    # Query plans
    plans = await memory_store.get_user_plans(user_id=user_id, limit=limit)
    
    return {
        "status": "success",
        "history": [
            {
                "plan_id": plan.plan_id,
                "goal": plan.initial_goal,
                "status": plan.overall_status,
                "created": plan.created
            }
            for plan in plans
        ]
    }
```

**Step 2: Test endpoint**

```bash
curl -X GET "http://localhost:8000/api/v3/task_history?limit=5" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## Common Scenarios

### Scenario 1: Multi-Step Task with Dependencies

**User request**: "Onboard new employee and schedule their training"

**System behavior**:

1. **Plan Creation**:
   ```
   Step 1: Create employee record (HRHelperAgent)
   Step 2: Provision equipment (TechnicalSupportAgent)
   Step 3: Set up accounts (TechnicalSupportAgent)
   Step 4: Schedule training (HRHelperAgent)
   ```

2. **Sequential Execution**: Steps execute in order
3. **Dependency Handling**: Step 4 uses employee ID from Step 1

### Scenario 2: Agent Needs Clarification

**User request**: "Process invoice"

**System behavior**:

1. **Agent Analysis**: HRHelperAgent realizes information is missing
2. **Proxy Agent Invoked**: 
   ```
   ProxyAgent message: "I need more information to process the invoice:
   - Which vendor is this invoice from?
   - What is the invoice amount?
   - What is the invoice date?"
   ```
3. **User Response**: User provides missing information
4. **Processing Resumes**: Agent processes with complete information

### Scenario 3: Task Rejection and Refinement

**User request**: "Delete all employee records"

**System behavior**:

1. **Plan Generated**:
   ```
   Step 1: Delete all employee records (HRHelperAgent)
   Warning: This action will permanently delete data
   ```

2. **User Rejection**: User sees the plan and clicks "Reject"
3. **User Provides Feedback**: "I only want to delete inactive employees"
4. **New Plan Generated**:
   ```
   Step 1: Identify inactive employees (HRHelperAgent)
   Step 2: Confirm list with user (ProxyAgent)
   Step 3: Delete only inactive employees (HRHelperAgent)
   ```

### Scenario 4: Using RAG for Document Search

**User request**: "What's our vacation policy?"

**System behavior**:

1. **Agent with RAG**: HRHelperAgent has `use_rag: true`
2. **Document Search**:
   - Searches company policy documents
   - Finds "Employee Handbook - Vacation Policy"
   - Retrieves relevant sections
3. **Response Generation**:
   ```
   "According to our employee handbook, full-time employees receive:
   - 10 days vacation in year 1
   - 15 days vacation after year 2
   - 20 days vacation after year 5
   
   [Source: Employee Handbook, Section 4.2]"
   ```

---

## Troubleshooting

### Issue 1: Agent Not Responding

**Symptoms**: User sends message but receives no response

**Possible causes and solutions**:

1. **WebSocket connection lost**
   - Check browser console for connection errors
   - Verify backend is running
   - Check firewall settings

2. **Agent initialization failed**
   - Check logs: `docker logs <container-name>`
   - Verify Azure OpenAI credentials
   - Check model deployment names

3. **RAI check failure**
   - Message contains inappropriate content
   - User sees error: "Request doesn't meet safety guidelines"
   - Rephrase message

**Debug steps**:
```bash
# Check backend logs
docker logs macae-backend

# Check for errors in orchestration
grep "ERROR" /var/log/backend.log

# Verify team is initialized
curl http://localhost:8000/api/v3/init_team
```

### Issue 2: Plan Approval Not Working

**Symptoms**: User approves plan but nothing happens

**Possible causes and solutions**:

1. **Mismatched plan IDs**
   - Check that `m_plan_id` matches in approval request
   - Verify orchestration_config has the approval pending

2. **Approval timeout**
   - Plan approvals may timeout after certain duration
   - User needs to restart the request

**Debug steps**:
```python
# Check approval status in orchestration_config
print(orchestration_config.approvals)

# Verify m_plan_id exists
if m_plan_id in orchestration_config.approvals:
    print("Approval found")
```

### Issue 3: Agent Using Wrong Tools

**Symptoms**: Agent calls incorrect tools or makes mistakes

**Possible causes and solutions**:

1. **Unclear system message**
   - Revise system_message to be more specific
   - Add examples of correct tool usage
   - Specify when NOT to use certain tools

2. **Missing tool descriptions**
   - Ensure MCP tools have clear descriptions
   - Add parameter descriptions
   - Provide usage examples

**Example fix**:
```json
{
  "system_message": "You are HR agent. Use create_employee_record ONLY for new hires. Use update_employee_record for existing employees. Never delete records without explicit approval."
}
```

### Issue 4: Slow Response Times

**Symptoms**: Long delays between user message and agent response

**Possible causes and solutions**:

1. **Large agent team**
   - Reduce number of agents in team
   - Use faster models (gpt-4.1-mini instead of o1-preview)

2. **Complex reasoning**
   - Simplify system messages
   - Break complex tasks into smaller steps

3. **RAG search slow**
   - Optimize search index
   - Reduce number of documents searched
   - Use semantic ranking

**Performance optimization**:
```json
{
  "name": "FastAgent",
  "deployment_name": "gpt-4.1-mini",  // Faster model
  "max_tokens": 500,  // Limit response length
  "temperature": 0.3   // More focused responses
}
```

### Issue 5: Incorrect Team Loaded

**Symptoms**: Wrong agent team is used for user

**Possible causes and solutions**:

1. **Team not set in database**
   - Check user's current team: `await memory_store.get_current_team(user_id)`
   - Manually set team: Call `/api/v3/select_team`

2. **Team configuration not found**
   - Verify team JSON file exists in `data/agent_teams/`
   - Check team_id matches database record

**Debug steps**:
```python
# Get user's current team
current_team = await memory_store.get_current_team(user_id)
print(f"Current team: {current_team.team_id if current_team else 'None'}")

# List available teams
teams = await memory_store.get_all_teams()
for team in teams:
    print(f"Team: {team.name}, ID: {team.team_id}")
```

---

## Advanced Topics

### Understanding Streaming Responses

Some agents can stream responses in real-time rather than waiting for complete response:

**How it works**:
1. Agent begins generating response
2. Partial content sent via WebSocket as it's generated
3. User sees text appearing word-by-word
4. Final message sent when complete

**Code location**: `src/backend/v3/callbacks/response_handlers.py`
- Function: `streaming_agent_response_callback()`

### Plan Conversion

The system converts simple plans to multi-step plans (m_plans):

**Conversion flow**:
1. User task → Simple goal
2. Orchestrator analyzes → Creates m_plan
3. M_plan has structured steps
4. Each step assigned to specific agent

**Code location**: `src/backend/v3/orchestration/helper/plan_to_mplan_converter.py`

### Error Recovery

System handles various errors gracefully:

**Error types**:
1. **Network errors**: Retry logic with exponential backoff
2. **Agent errors**: Fallback to simpler approach or ask for help
3. **Tool errors**: Report error to user via ProxyAgent
4. **Timeout errors**: Save state and notify user

### Monitoring and Telemetry

System tracks events for monitoring:

**Key events tracked**:
- `PlanCreated` - New plan started
- `PlanApproved` - User approved plan
- `AgentToolCall` - Agent used a tool
- `TaskCompleted` - Task finished successfully
- `ErrorOccurred` - Error during processing

**Location**: Events sent to Application Insights (if configured)

---

## Summary

This guide covered the complete message workflow from user input to final response. Key takeaways:

1. **Message Flow**: User → API → Orchestrator → Agents → Response
2. **Key Components**: Frontend, Backend API, Orchestration Manager, Agents, Database
3. **Customization Points**: Agent teams, system messages, tools, models
4. **Real-time Communication**: WebSocket for instant updates
5. **Safety**: RAI checks ensure appropriate content
6. **Approval Process**: Users review plans before execution

For more help:
- Check other documentation in `/docs` folder
- Review sample configurations in `/data/agent_teams`
- Examine code with detailed comments
- Refer to troubleshooting section for common issues

---

## Next Steps

After understanding this workflow, you can:

1. **Create custom agent teams** for your specific use case
2. **Develop custom MCP tools** for your business processes
3. **Modify system messages** to change agent behavior
4. **Integrate with your existing systems** via custom tools
5. **Monitor and optimize** performance for your workload

Happy customizing! 🚀
