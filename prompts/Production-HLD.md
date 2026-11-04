# Task Overview
1. Peform the first part of this assignment which involves designing a production-ready full High-Level Design (HLD), with a detailed technical explanation. 
2. The full assignment document has been included with the prompt from the file "datachat-assignment.pdf"
3. THe chat-agent prototype has already been built and containerized and is this repository

# Task Details
Create a  High-Level wordx Design document that contains the following sections

## Introduction
Provide a detailed introduction to the overall architecture of the system. 

## High-Level Architecture

Describe and explain the following logical architecture layers and what each one provides. Organize the system into five logical layers:

1. Interface and security: User authentication, CLI/API, permissions.
2. Agent orchestration: LangGraph and its reasoning workflow.
3. Analytical tools: SQL generation, validation, execution, and report generation.
4. Knowledge and persistence: Golden Knowledge, conversation memory, saved reports.
5. Operations: Monitoring, evaluations, configuration, and resilience.

Use the supplied logical architecture  diagram in the file "High-Level-Logical-Diagram-Production.png" to describe in detail each layer. Iclude the diagram in the document. Clarify that this diagram and logical architecture describes how information is transferred and persisted.

Then describe and include the GCP deployment diagram in the file "GCP-Deployment-Diagram-Production.png" in order to claify where the components run in Cloud Run, Cloud SQL, GCS, Vertex AI, and BigQuery.

## Runtime Data flow
Describe the following executive question: 

    "Why did revenue decrease in California last quarter compared with Texas, and what should we do about it?"
    
The agent should execute something resembling the following sequence.

Include the data flow diagram in the file "Runtime-Dataflow-Diagram.png" and explain the various runtime aspects.

Make the following point(s)
<why-type-question>
An important detail: a request asking why something happened often requires multiple SQL queries.
For instance:
Calculate revenue by state and quarter.
Decompose revenue into order volume and average order value.
Compare product category contributions.
Examine the returning-customer mix.
Analyze discounts or returns if supported by the available data.
Generate a concise executive explanation.
The agent should use the results of one query to inform subsequent queries when necessary.
However, it should never automatically conclude that a correlation proves causality. If California had fewer orders, that could explain the revenue decomposition, but not necessarily the underlying reason customers placed fewer orders.
This is an excellent opportunity to demonstrate your data science judgment.
</why-type-question>


## Individual Requirements / Components
For each production HLD required in the supplied "Requirements-table-screenshot.png" image create an detailed individual section in the design document that explains the following:
    - What the requirement consists of and is
    - What purpose the requirement serves
    - How the requirement will be designed and/or provided
    - What decisions and/or assumptions were made in the design of this requirement/component
    - Whether this requirement is implemented and demoed in the POC. This will be true whenever the requirement is required in the POC per the requirements table.

## Relatiionships
Some of the requirements/components will either connect, communicate, depend on or simply related to other requirements/components in the overall architecture.
For each such relationship explain the following:
    - How they are related
    - What purposde dfoes the relationship serve
    - If they communicate how is this done
      - What protrocol is used
      - What validation mechanisms are in place
      - How are connect/communication handled


## Decisions / Assumptions
Claify any assumptions or decisions that were made that shaped, affected and/or constrained the architure of the system.

## Conclusion
Provide a reasonable conclusion:
1. Summarize concisely and precisely what the system does
2. Outline know limitations
3. Suggest improvements and extensions
