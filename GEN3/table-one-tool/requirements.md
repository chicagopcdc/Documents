# PCDC Table One Requirements

This living document serves as a reference point for the requirements for designing and implementing Table One tool.

## Overview

> NOTE: The Table One tool's overall design is nearly identical to that of the Survival Analysis tool. Refer to [the requirements document](../survival-analysis-tool/requirements.md) for the Survial Analysis tool for comparison purposes.

### Architecture

The Table One tool can be seen as a combination of the following elements:

- React Component
  - A UI component that 1) allows users to provide input, 2) sends HTTP request to Microservice with the user input, 3) fetches the response JSON data from Microservice, and 4) generates/updates the table using the fetched data.
  - React Component consists of three UI child components: 1) a user input form, and 2) a table output.
- Microservice
  - A newly created web service that 1) listens to HTTP requests with user input as a payload, 2) fetches the data from Data Source with the filters, 3) generate table data using the fetched data with the exposure/confounding variables and test type, and 4) serve the table data to the client as the payload for HTTP response.
  - Microservice will be implemented as an endpoint of [`PcdcAnalysisTools`](https://github.com/chicagopcdc/PcdcAnalysisTools) service.
- Data Source
  - A database or an intermediary service that responds to database queries.

### Workflow

The following diagram demonstrates the expected task workflow of the survival analysis tool:

![Workflow](./images/workflow.png)

1. User input is provided, triggering Step 2
2. React Component sends HTTP request to Microservice with the user input as a payload
3. Microservice constructs data query based on user input and sends it to Data Source
4. Data Source sends query result to Microservice
5. Microservice creates table one data with the user input and the fetched query result, and packages the output as a JSON object
6. Microservice sends HTTP response to React Component with table data in JSON as a payload
7. React Component generates/updates table using the fetched output

## React Component

Table One tool's React Component will be part of the `<GuppyDataExplorer>` component of PCDC's Gen3 data portal.

`<GuppyDataExplorer>` includes two child components, `<ExplorerFilter>` and `<ExplorerVisualizations>`, and the Table One React Component will be part of `<ExplorerVisualizations>`. More specifically, React Component will be implemented so that it is one of the tabs ("Table One" table) on `<ExplorerVisualizations>`.

### Features

The following features and functionalities are required of React Component:

- Access to the filter values set by users via `<ExplorerFilter>` UI
- A child component to receive user input for "exposure variable", "confounding variables", and type of test (t-test or chi-squared test).
- A child component to display table output
  - Using vanila JSX elements (`<table>`, `<thead>`, `<tbody>`, etc.) is recommended for building the table; Using [React Table](https://react-table.tanstack.com/) is also acceptable as it is an existing dependency.

## Microservice

Table One tool's Microservice is a new endpoint for the [`PcdcAnalysisTools`](https://github.com/chicagopcdc/PcdcAnalysisTools) service added to the Gen3 infrastructure and responsible for generating table data from project data fetched from Data Source according to the user input provided by React Component.

### Request API

> :warning: Request API is currently not stable.

Microservice listens to POST request with the payload in JSON of the following shape:

```jsonc
{
  // request body
}
```

### Response API

> :warning: Request API is currently not stable.

Microservice sends response with data in JSON of the following shape:

```jsonc
{
  // response data
}
```
