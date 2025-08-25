# LLM Agent POC - Project Structure and Setup

## File Structure
```
llm-agent-poc/
├── index.html                 # Main HTML file with embedded JavaScript
├── README.md                  # This file
├── package.json              # NPM package file (optional, for development)
├── config.example.js         # Example configuration file
└── docs/
    ├── api-setup.md          # API setup instructions
    └── usage-examples.md     # Usage examples
```

## Quick Start

1. **Download the files** and save them in a folder called `llm-agent-poc`

2. **Open `index.html`** in a modern web browser (Chrome, Firefox, Safari, Edge)

3. **Configure API keys** in the interface:
   - **LLM Provider API Key**: Get from OpenAI, OpenRouter, or use AIPipe
   - **Google Search API Key**: Get from Google Cloud Console
   - **Search Engine ID**: Create a Custom Search Engine

4. **Start chatting** with the agent!

## API Setup Instructions

### OpenAI API
1. Visit https://platform.openai.com/api-keys
2. Create a new API key
3. Select "OpenAI" as provider and enter your key

### OpenRouter API
1. Visit https://openrouter.ai/keys
2. Create a new API key
3. Select "OpenRouter" as provider and enter your key

### AIPipe Proxy
1. Visit https://aipipe.org/
2. Sign up for an account
3. Get your AIPipe token
4. Select "AIPipe Proxy" as provider and enter your token

### Google Search API
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable the "Custom Search API"
4. Create credentials (API Key)
5. Go to [Custom Search Engine](https://cse.google.com/)
6. Create a new search engine
7. Copy the Search Engine ID

## Features

### Core Agent Loop
- Takes user input
- Calls LLM with conversation context and available tools
- Executes tool calls as needed
- Loops until task completion

### Supported Tools

1. **Google Search**: Web search with snippet results
2. **AIPipe Proxy**: Flexible API calls through the AIPipe service
3. **JavaScript Execution**: Safe code execution in browser sandbox

### UI Features
- Bootstrap-based responsive design
- Real-time chat interface
- Error handling with alerts
- Code execution results display
- Model and provider selection

## Usage Examples

### Basic Conversation
```
User: Hello, what can you do?
Agent: I'm an LLM agent that can help you with various tasks using tools like web search, API calls, and code execution. What would you like to work on?
```

### Search Example
```
User: Search for recent news about artificial intelligence
Agent: I'll search for recent AI news for you.
[Calls google_search tool]
Agent: I found several recent articles about AI developments...
```

### Code Execution Example
```
User: Calculate the fibonacci sequence up to 10 numbers
Agent: I'll calculate the Fibonacci sequence for you.
[Calls execute_javascript tool]
Agent: Here's the Fibonacci sequence calculated: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### AIPipe Example
```
User: Use AIPipe to get a weather forecast
Agent: I'll make an API call through AIPipe to get weather information.
[Calls aipipe_call tool]
Agent: Here's the weather forecast I retrieved...
```

## Development Notes

### Security Considerations
- JavaScript execution is sandboxed to prevent harmful operations
- No access to file system, network (except allowed APIs), or sensitive browser APIs
- Console output is redirected to the chat interface

### Extensibility
The code is designed to be easily extended:
- Add new tools by extending the `handleToolCall` method
- Modify the UI by editing the HTML/CSS
- Add new LLM providers by extending the `callLLM` method

### Browser Compatibility
- Requires modern browser with fetch API support
- No localStorage/sessionStorage dependencies
- All state stored in memory during session

## Troubleshooting

### Common Issues

1. **API Key Errors**: Ensure you have valid API keys for your chosen providers
2. **CORS Errors**: Some APIs may require proxy setup for browser access
3. **Tool Execution Failures**: Check API quotas and network connectivity
4. **JavaScript Sandbox Errors**: Some operations may be restricted for security

### Error Messages
All errors are displayed using Bootstrap alerts with dismissible notifications.

## Technical Implementation

### Agent Loop Logic
The core loop follows the Python pseudocode but implemented in JavaScript:

```javascript
async processAgentLoop() {
    while (true) {
        const { output, toolCalls } = await this.callLLM();
        
        if (output) {
            this.addMessage(output, 'agent');
        }

        if (toolCalls && toolCalls.length > 0) {
            for (const toolCall of toolCalls) {
                const result = await this.handleToolCall(toolCall);
                this.messages.push({
                    role: 'tool',
                    tool_call_id: toolCall.id,
                    content: JSON.stringify(result)
                });
            }
        } else {
            break; // No more tools needed
        }
    }
}
```

### Tool Call Format
Uses OpenAI's standard tool calling format with JSON Schema for parameters.

### Message Handling
Maintains conversation state with proper role assignments (user, assistant, tool).

## License
This is a proof-of-concept educational project. Use responsibly and ensure compliance with API provider terms of service.

# Usage Examples and API Documentation

## Example Conversations

### 1. Interview Assistant
```
User: Interview me to create a blog post.
Agent: I'd be happy to help you create a blog post! Let me ask you some questions to get started. What topic would you like to write about?

User: About IBM
Agent: Let me search for current information about IBM to help create a comprehensive blog post.
[Tool Call: google_search("IBM recent news 2025")]
Agent: I found some recent information about IBM. Based on my search, IBM has been focusing on AI, cloud computing, and enterprise solutions. Now let me ask you:

1. What angle do you want to take for your blog post? (e.g., IBM's history, recent developments, business analysis)
2. Who is your target audience?
3. What's your experience or connection with IBM?

User: I want to focus on their AI initiatives for business executives.
Agent: Perfect! That's a very timely topic. Let me gather more specific information about IBM's AI initiatives.
[Tool Call: google_search("IBM watsonx AI business 2025")]
Agent: Excellent! I found information about IBM's watsonx platform and their enterprise AI strategy. Now I'll create an outline for your blog post...
```

### 2. Data Analysis Task
```
User: Help me analyze some sales data. Can you create a simple calculator?
Agent: I'll create a sales data analyzer for you using JavaScript.

[Tool Call: execute_javascript(`
// Sales Data Analyzer
const salesData = [
    { month: 'Jan', sales: 10000 },
    { month: 'Feb', sales: 15000 },
    { month: 'Mar', sales: 12000 },
    { month: 'Apr', sales: 18000 },
    { month: 'May', sales: 22000 }
];

// Calculate total sales
const totalSales = salesData.reduce((sum, item) => sum + item.sales, 0);

// Calculate average
const averageSales = totalSales / salesData.length;

// Find best month
const bestMonth = salesData.reduce((max, item) => 
    item.sales > max.sales ? item : max
);

// Calculate growth rate
const growthRates = salesData.map((item, index) => {
    if (index === 0) return { month: item.month, growth: 0 };
    const prevSales = salesData[index - 1].sales;
    const growth = ((item.sales - prevSales) / prevSales * 100).toFixed(2);
    return {