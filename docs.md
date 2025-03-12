# Docs

# Workflow Engine
This repository contains a Workflow Engine that can execute a series of connected tasks (nodes) defined in a workflow definition file (YAML). The engine can handle user input, file I/O, API requests, data transformation, bash command execution and conditional branching.

*Some code assistance was provided by **Claude 3.7 Sonnet** to enhance specific implementations.*

### 🚀 Features
1. User Input and Output (User I/O)
2. File Read and Write (File I/O)
3. API Requests (HTTP)
4. Bash Command Execution
5. Data Transformation
6. Conditional Branching

### 📜 Workflow Definition Structure
Each workflow is defined in a YAML file with the following structure:

```yaml
name: <Workflow Name>
description: <Description of the workflow>
start: <Starting Node Name>

nodes:
  <node_name>:
    type: <Node Type>
    config:
      <key>: <value>
    next:
      <default>: <next_node> 
```
#### Fields Definition
**start**

The **start** field indicates the starting node of a specific workflow.This is a **required** field

**type** 

The **type** field defines the type of operation the node performs. This is a **required** field. Supported types are:

| Type          | Description                                                                     |
|--------------|---------------------------------------------------------------------------------|
| input        | Accepts input from the user via stdin.                                          |
| output       | Prints output to stdout.                                                        |
| file_reader  | Reads data from a file.                                                         |
| file_writer  | Writes data to a file.                                                          |
| command      | Executes a bash command.                                                        |
| http_request | Makes an API request to a URL.                                                  |
| transform    | Performs data transformations like find/replace, extract json or concatenation. |
| conditional  | Routes the workflow based on a condition (true/false).                          |

**use**

The **use** field is used to define which task data to access by a specific task. For example, in the below node definition print_console, we are instructing the workflow engine to use data from write node. This is an optional field and if you have to access any data from a different task, you can access it from the `context` variable.
```yaml
  print_console:
    type: output
    config:
      message: "Write complete, (Location, Bytes): {data}."
      use: write
    next:
      default: find_and_replace
```

**next**

The **next** field is used to instruct the engine the flow from one node to another. It is a **required** field. The permitted values are
1. `default`- Used to depict the next node. This is the default behaviour.
```yaml
  get_name:
    type: input
    config:
      prompt: "What is your name? "
    next:
      default: check_name
```
The above definition snippet indicates the next task after `get_name` is `check_name`.

2. `{}` - Used to indicate there is no other task or the current node is the terminating node.
```yaml
  print_console:
    type: output
    config:
      message: "Transformed data: {data}"
      use: concat
    next: {}
```
3. `True` or `False`- These sub-fields are used when the current node is a conditional node. If the condition evaluates to `True`, the control goes to the node specified in that field. Same for the `False` condition.
```yaml
  check_name:
    type: conditional
    config:
      condition_type: equals
      value: Bob
      use: get_name
    next:
      "True": greet_bob
      "False": greet_other
```

### 🔥 Example Workflows
Workflows are maintained in the `src/resources/workflows` path.
1. **File I/O Workflow**-
Reads from a file, writes to another file, transforms data, and outputs it. 
File: workflow2.yaml

2. **Bash Command Workflow**-
Prompts the user for a directory path and lists its contents. File: workflow3.yaml

3. **API Request Workflow**-
Prompts the user for an API URL, makes a request, and handles success/failure conditions. File: workflow4.yaml

### 💡 Running the Workflow Engine
You can execute any workflow by running:
```commandline
just run run-workflow <PATH TO WORKFLOW>

```
eg: ``
just run run-workflow src/resources/workflows/workflow5.yaml``

### 📜 Future Improvements
 - Support for parallel execution of nodes.
 - Enhanced error handling.
 - Support for more node types (like database queries, FTP requests).
 - YAML schema validation.
 - More unit tests.




