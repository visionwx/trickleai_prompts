# TrickleAI Client

A JavaScript client for interacting with TrickleAI socket services.

## Installation

```html
<script src="https://resource.trickle.so/vendor_lib/trickleai-service/trickleai-service.min.js"></script>
```

- Once installed, the `TrickleAIService` class will be available globally.

## Usage

```javascript
// Initialize the service
const service = new TrickleAIService(
    workspaceId,
    memberId, 
    userId,
    token,
    {
        // Optional configuration
        maxReconnectAttempts: 5,
        reconnectDelay: 1000,
        onSocketStatusChange: (isConnected, event) => {
            // Handle socket status changes
            console.log('Socket status:', event, isConnected);
        }
    }
);

// Connect to socket
await service.connect();

// Get or create conversation
const conversationId = await service.getConversationId('conversation_name');

// List conversation messages
const messages = await service.listConversationMessages(conversationId, 10);

// Run AI agent
const response = await service.runAgent(
    'conversation_name',
    agentConfigId,
    {
        "inputs.system": "You are helpful assistant",
        "inputs.user": "Hello!"
    },
    {
        // Optional callbacks
        onMessage: (text) => {
            // Handle streaming updates
            console.log('Streaming:', text);
        },
        onDone: (fullResponse) => {
            // Handle completion
            console.log('Done:', fullResponse);
        },
        onError: (error) => {
            // Handle errors
            console.error('Error:', error);
        }
    },
    // Optional user data
    { text: "Hello!" }
);

// Clear conversation
const success = await service.clearConversation(conversationId);

// Parse assistant message
const messageBlocks = service.parseAssistantMessage(assistantMessage);
```

## API Reference

### Constructor

```typescript
constructor(
    workspaceId: string,
    memberId: string,
    userId: string,
    token: string,
    options?: {
        maxReconnectAttempts?: number,
        reconnectDelay?: number,
        onSocketStatusChange?: (isConnected: boolean, event: string) => void
    }
)
```

Creates a new TrickleAI service instance.

- `workspaceId`: Workspace identifier
- `memberId`: Member identifier
- `userId`: User identifier
- `token`: Authentication token
- `options`: Optional configuration
  - `maxReconnectAttempts`: Maximum reconnection attempts (default: 5)
  - `reconnectDelay`: Initial delay between reconnection attempts in ms (default: 1000)
  - `onSocketStatusChange`: Callback for socket status changes
    - `isConnected`: Current socket.connected state
    - `event`: Status event ('connect', 'connect_error', 'disconnect', 'reconnecting')

### Methods

#### connect()

```typescript
async connect(): Promise<void>
```

Establishes socket connection. Must be called before using other methods.

#### getConversationId(conversationName)

```typescript
async getConversationId(conversationName: string): Promise<string>
```

Gets or creates a conversation by name.

Generate conversation names according to the following specifications:

- always use this format: `{name}-{randomUUID}`, for example: "netagent-c300bb44adc142d49e8ffe535aaed230", "test-c300bb44adc142d49e8ffe535aaed230"
- save the generated conversation name to local storage by default


#### listConversationMessages(conversationId, limit?, until?)

```typescript
async listConversationMessages(
    conversationId: string,
    limit?: number,
    until?: number | null
): Promise<Array<Message>>
```

Lists messages from a conversation.

- `limit`: Maximum number of messages (default: 10)
- `until`: Timestamp for pagination

Message format:
```typescript
interface Message {
    author: "user" | "assistant" | "system"
    text: string
    createAt: string // example, "2025-04-18T05:11:14.042665+0000"
}
```

#### runAgent(conversationName, agentConfigId, promptVariables, callbacks?, userData?)

```typescript
async runAgent(
    conversationName: string,
    agentConfigId: string,
    promptVariables: Object,
    callbacks?: {
        onMessage?: (text: string) => void,
        onDone?: (fullResponse: string) => void,
        onError?: (error: Error) => void
    },
    userData?: Object
): Promise<string>
```

Runs an AI agent in the specified conversation.

- `callbacks`: Optional callbacks for handling responses
  - `onMessage`: Called with streaming updates
  - `onDone`: Called with final response
  - `onError`: Called on error
- `userData`: Optional data to store with the message

#### clearConversation(conversationId)

```typescript
async clearConversation(conversationId: string): Promise<boolean>
```

Clears all messages in a conversation.

#### parseAssistantMessage(assistantMessage)

```typescript
parseAssistantMessage(assistantMessage: string): AssistantMessageContent[]
```

Parses assistant message content into structured blocks.

- `assistantMessage`: The message content sent by the assistant

Returns an array of `AssistantMessageContent` objects, which can be either:

- `TextContent`: Regular text content
  ```typescript
  interface TextContent {
    type: "text"
    content: string
    partial: boolean
  }
  ```

- `ToolUse`: Tool usage content
  ```typescript
  interface ToolUse {
    type: "tool_use"
    name: ToolUseName  // e.g. "execute_command", "read_file", etc.
    params: Partial<Record<ToolParamName, string>>
    partial: boolean
  }
  ```

Example usage:

```javascript
// Parse assistant message
const messageBlocks = service.parseAssistantMessage(assistantMessage);

// Process different types of content
messageBlocks.forEach(block => {
  if (block.type === "text") {
    // Handle text content
    console.log("Text content:", block.content);
    console.log("Is partial:", block.partial);
  } else if (block.type === "tool_use") {
    // Handle tool use
    console.log("Tool name:", block.name);
    console.log("Tool parameters:", block.params);
    console.log("Is partial:", block.partial);
    
    // Example: Handle specific tools
    if (block.name === "execute_command") {
      const command = block.params.command;
      console.log("Command to execute:", command);
    }
  }
});
```

## Socket Status Events

The `onSocketStatusChange` callback receives the following events:

- `connect`: Socket successfully connected
- `connect_error`: Error connecting to socket
- `disconnect`: Socket disconnected
- `reconnecting`: Attempting to reconnect

## Configuration

- token: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c3JfMDk0NzYwYTZkODAwMDAwMSIsImlhdCI6MTc0NDUyMDYyMSwiZXhwIjoxNzc2MDc3NTczLCJzY29wZSI6ImJyb3dzZXIifQ.6PFtPxgkw1-YFydUyJvjtZIxFe0O6JsdsMgxqtbf_bg`
- workspaceId: `workspace_id_usr_094760a6d8000001`
- memberId: `member_id_usr_094760a6d8000001`
- userId: `usr_094760a6d8000001`

## Avaliable Agents

### General Agent

#### Agent Info
- agent config ID: `e7d4644af2aa4b84a4a8d6a9e7d83a81`
- prompt variables:
```javascript
promptVariable = {
    'inputs.text': '', // the user input question
    'inputs.currentTime': '', // 4/7/2025, 12:03:18 PM (Asia/Shanghai, UTC+8:00)
    'inputs.currentMode': 'ACT MODE', // always use 'ACT MODE'
    'inputs.currentWorkingDirectory': '', // Current working directory
    'inputs.currentWorkingDirectoryOfFriends': '', // Current Working Directory of all the friends
}
```
    - Use the following function to get the current time
    ```javascript
    function getCurrentTime() {
        const date = new Date();
        const currentTime = date.toLocaleString('en-US', { 
            timeZone: 'Asia/Shanghai',
            year: 'numeric',
            month: 'numeric',
            day: 'numeric',
            hour: '2-digit',
            minute: '2-digit',
            second: '2-digit',
            hour12: true
        });
        return `${currentTime} (Asia/Shanghai, UTC+8:00)`
    }
    ```
    - Example of 'inputs.currentWorkingDirectory'
    ```
    notes/notion-pricing.md
    knowledge/how-to-write-a-note-page.md
    ```
    - Example of 'inputs.currentWorkingDirectoryOfFriends'
    ```
    ## XXX's current working directory
    notes/notion-pricing.md
    knowledge/how-to-write-a-note-page.md

    ## YYY's current working directory
    ...
    ```

#### Usage
```javascript
// Run AI agent
const response = await service.runAgent(
    'conversation_name',
    agentConfigId,
    {
        'inputs.text': 'Compare pricing of each model from OpenAI',
        'inputs.currentTime': '4/7/2025, 12:03:18 PM (Asia/Shanghai, UTC+8:00)',
        'inputs.currentMode': 'ACT MODE',
        'inputs.currentWorkingDirectory': 'notes/notion-pricing.md\nknowledge/how-to-write-a-note-page.md',
        'inputs.currentWorkingDirectoryOfFriends': '',
    },
    {
        // Optional callbacks
        onMessage: (text) => {
            // Handle streaming updates
            const messageBlocks = service.parseAssistantMessage(text);
            // Handle TextContent and ToolUse base on the type of per tool

        },
        onDone: (fullResponse) => {
            // Handle completion
            const messageBlocks = service.parseAssistantMessage(fullResponse);
        },
        onError: (error) => {
            // Handle errors
            console.error('Error:', error);
        }
    },
    // Optional user data
    { text: "Hello!" }
);
```

#### Support Tools

- read_file
  ```typescript
  const readFileTool:ToolUse = {
    "type": "tool_use",
    "name": "read_file",
    "params": {
        "path": "file path",
    },
    "partial": false,
  }
  ```

- write_to_file
  ```typescript
  const writeToFileTool:ToolUse = {
    "type": "tool_use",
    "name": "write_to_file",
    "params": {
        "path": "file path",
        "content": "file content"
    },
    "partial": false,
  }
  ```

- replace_in_file
  ```typescript
  const replaceInFileTool:ToolUse = {
    "type": "tool_use",
    "name": "replace_in_file",
    "params": {
        "path": "file path",
        "diff": "multi SEARCH/REPLACE blocks"
    },
    "partial": false,
  }
  ```

- ask_followup_question
  ```typescript
  const askFollowupQuestionTool:ToolUse = {
    "type": "tool_use",
    "name": "ask_followup_question",
    "params": {
        "question": "question to ask",
        "options": "['option1', 'option2', ...]"
    },
    "partial": false,
  }
  ```

- attempt_completion
  ```typescript
  const attemptCompletionTool:ToolUse = {
    "type": "tool_use",
    "name": "attempt_completion",
    "params": {
        "result": "final result description"
    },
    "partial": false,
  }
  ```
