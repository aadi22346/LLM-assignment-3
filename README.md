# LLM-assignment-3

# Build-Your-Own SQL Database Agent (From Scratch)

This project is a lightweight, read-only SQL Database Agent built entirely from scratch, as required for the "Build-Your-Own SQL Database Agent" assignment. The agent interprets natural language questions and interacts with a SQL database using a ReAct (Reasoning + Acting) loop.

It is built without any external agent frameworks (like LangChain) and uses only an LLM client library and the standard `sqlite3` library.

## 🚀 Features

  * **Natural Language to SQL**: Answers natural language questions about the data in a SQL database.
  * **ReAct Loop**: Implements the Thought $\rightarrow$ Action $\rightarrow$ Observation loop from scratch to show its reasoning.
  * **Schema Discovery**: Can explore the database schema by listing tables and describing their columns.
  * **Safe & Read-Only**: The agent is restricted to `SELECT`-only queries and includes a safety validator to prohibit any database mutations.

## ⚙️ How to Run

1.  **Install Dependencies**:

    ```bash
    pip install google-generativeai python-dotenv
    ```

2.  **Set Up Database**:
    The agent connects to a pre-existing `database.db`. Ensure this file is present and populated.

3.  **Run a Query**:
    To run the agent, pass your question as a command-line argument to the main script.

    ```bash
    python main.py "How many users are in the database?"
    ```

    *(This assumes you have a `main.py` file that imports your `query` function and passes `sys.argv[1]` to it.)*

## 🛠️ Available Tools

The agent has access to the following tools, which are documented for the LLM:

| Tool Name | Parameters | Description |
| :--- | :--- | :--- |
| **`list_tables`** | None | Lists all tables available in the database. |
| **`describe_table`** | `table_name` (str) | Returns the schema (columns and types) for a specific table. |
| **`query_database`** | `query` (str) | Executes a single, safe, read-only SQL `SELECT` query. |

## 🔬 Example Trace

Here is a sample trace for the query: "How many users are in the database?"

```
Question: How many users are in the database?

Thought: I need to find the number of users in the database. I should start by listing all the tables to identify the relevant table.
Action: list_tables{}
PAUSE

 -- running list_tables with {}
Observation: ['users', 'orders']
Thought: The 'users' table is likely to contain information about users. I need to inspect the schema of the 'users' table to confirm and understand its structure before querying.
Action: describe_table{"table_name": "users"}
PAUSE

 -- running describe_table with {"table_name": "users"}
Observation: [('user_id', 'INTEGER'), ('username', 'TEXT'), ('email', 'TEXT')]
Thought: The 'users' table has columns like 'user_id', 'username', and 'email', which confirms it's the correct table for user information. To find out how many users there are, I need to count the number of rows in this table.
Action: query_database{"query": "SELECT COUNT(*) FROM users"}
PAUSE

 -- running query_database with {"query": "SELECT COUNT(*) FROM users"}
Observation: {'columns': ['COUNT(*)'], 'rows': [(5,)]}
Thought: The query returned a count of 5. This is the final answer.
FINAL ANSWER: There are 5 users in the database.
```
