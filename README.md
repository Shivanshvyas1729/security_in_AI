# security_in_AI

<details><summary>MCP</summary>
Here are comprehensive notes based on the video **"MCP Explained: How AI Agents Connect to Tools | Client, Server & Architecture"**:

---

# MCP (Model Context Protocol) Explained

## 1. The Problem Without MCP

When building AI applications (such as a Travel Planning AI App), developers typically need to integrate multiple external services:

* **Weather API**
* **Google Drive / GitHub** (for file storage and repository management)
* **Databases** (to give the AI app memory to store user queries and details)
* **Flight APIs** (to check flight status)

### Key Disadvantages of Direct Integration:

1. **High Development Overhead:** Every API has a completely different authentication mechanism, request format, response format, and documentation. Developers have to write separate integration code for each service and learn all their individual quirks.

<img width="1920" height="1080" alt="MCP Explained_ How AI Agents Connect to Tools _ Client, Server   Architecture 2-11 screenshot" src="https://github.com/user-attachments/assets/a1e5f3eb-c6bc-49e0-a1af-8833148118f0" />

2. **Framework Lock-In:** If you build your agentic AI application using a specific framework (e.g., LangGraph) and later want to migrate or run the exact same logic using another framework (e.g., OpenAI Agents SDK, CrewAI, Agno), **you have to rewrite the entire application code** from scratch because each framework handles tool integration differently.
<img width="1920" height="1080" alt="MCP Explained_ How AI Agents Connect to Tools _ Client, Server   Architecture 3-15 screenshot" src="https://github.com/user-attachments/assets/27384bf9-2eb3-40e1-97b4-9e5905e25bed" />



---

## 2. The Solution: What is MCP?

**Model Context Protocol (MCP)** standardizes how AI applications connect to external tools and data sources.

* Instead of your AI application writing custom code to integrate directly with multiple disparate APIs, your application connects to an **MCP Server**.
* **Any Framework:** Whether your app is built in LangGraph, CrewAI, Agno, or OpenAI Agents SDK, it simply connects to the same MCP Server(s).
* **Separation of Concerns:** The MCP Server handles all the heavy lifting (managing requests, formatting payloads, handling authentication, and talking to the underlying APIs). Your application only needs to write a minimal amount of code to establish a connection with the MCP Server.
<img width="1920" height="1080" alt="MCP Explained_ How AI Agents Connect to Tools _ Client, Server   Architecture 5-29 screenshot" src="https://github.com/user-attachments/assets/f86627b5-03fe-472d-9f98-31a27974a330" />
<img width="1920" height="1080" alt="MCP Explained_ How AI Agents Connect to Tools _ Client, Server   Architecture 8-46 screenshot" src="https://github.com/user-attachments/assets/4d928483-8fc8-4960-a9c0-40c0d3cb0e80" />

---

## 3. Core Architecture & Components of MCP

MCP architecture primarily consists of two main components:

1. **MCP Client:**
* Lives inside your AI application.
* Acts as a bridge between your AI app/LLM and the MCP Servers.
* A single MCP Client can connect to **either a single MCP Server or multiple MCP Servers simultaneously**.


2. **MCP Server:**
* Exposes specific capabilities or **tools** (e.g., `get_current_weather`, `read_file`, `search_flights`).
* Can wrap around APIs, databases, local files, Google Drive, GitHub, operating system tools, or internal company systems.



---

## 4. How MCP Works (Step-by-Step Execution Flow)

1. **Initialization / Discovery:** When the AI app starts up, the **MCP Client** connects to all available **MCP Servers** and asks: *"What tools do you have?"* The servers return a list of available tools, which the MCP Client stores and makes available to the AI app.
2. **User Query:** The user asks a question (e.g., *"What is the weather in Delhi today?"*).
3. **LLM Evaluation:** The question goes to the **LLM** inside the AI app.
* If the LLM already knows the answer, it responds directly.
* If it doesn’t have the info (due to training cutoff limits), it inspects the list of available tools provided by the MCP servers and selects the appropriate tool (e.g., `get_current_weather`).


4. **Tool Call Generation:** The LLM generates a tool call request.
5. **Routing via MCP Client:** The request goes to the **MCP Client**, which recognizes which MCP Server owns that tool and establishes a connection with that specific server.
6. **Execution via JSON-RPC:** Communication between the MCP Client and MCP Server always happens using the **JSON-RPC message format** (standardized globally for all MCP servers).
7. **API Fetch & Response:** The MCP Server calls the underlying API, receives the result, wraps it back into a JSON-RPC response, and sends it back to the MCP Client.
8. **Final Output:** The MCP Client passes the data to the LLM, which converts it into a human-readable response for the user.

---

## 5. Types of MCP Servers

* **Local MCP Servers:** The AI app (and MCP client) and the MCP servers run on the exact same local machine/computer.
* **Rremote MCP Servers:** The AI app runs locally on your machine, but the MCP server is hosted remotely on the cloud and accessed via a network/internet connection.
* **Custom MCP Servers:** Built custom by developers (e.g., wrapping a private company database) to keep internal data secure and private.

---
<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/7e7593f7-f8ae-4bf4-be5a-405290647c43" />

## 6. MCP Transport Protocols

How messages travel between the MCP Client and MCP Server depends on where the server is located:

1. **Standard I/O (stdio):**
* Used when both the MCP Client and MCP Server are on the **same local machine**.
* Communicates via standard input and standard output streams.


2. **Streamable HTTP:**
* Used when the MCP Server is **remote (on the cloud)** and accessed over the internet.
* **Why "Streamable"?** AI operations are often long-running (e.g., analyzing a massive GitHub repository taking 10–20 seconds). Instead of waiting silently for a single traditional HTTP request/response cycle to finish, Streamable HTTP allows the server to send continuous progress updates (e.g., *25% complete, 50% complete, Done*) back to the client in real time.



---

## 7. Use Cases Beyond Custom Apps

You don't necessarily have to be a developer building an app from scratch to use MCP. Ready-made desktop applications (such as **Claude Desktop**) also contain built-in MCP clients that allow you to plug in local or remote MCP servers (like a File System MCP Server) directly out of the box.
</details>
