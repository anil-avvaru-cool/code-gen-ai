## User Story: Create Issue via PostIssue (HTTP Context)

**As** an external client or web UI  
**I want** to call the `PostIssue` HTTP agent to create a new issue  
**So that** my request fields are stored correctly and I receive a confirmation JSON response.

### Description
- The `PostIssue` agent is invoked in an HTTP context and reads request items for `Title`, `Description`, and `Priority`.
- It creates a new `IssueForm` document, maps the incoming values to the corresponding fields, sets `Status` to `New`, and populates metadata such as `IssueID`, `CreatedBy`, and `CreatedDate`.
- The agent returns a JSON response indicating success and echoing the created `IssueID`.

### Acceptance Criteria (Gherkin)

```gherkin
Feature: Create Issue via PostIssue HTTP agent
  As an external client or web UI
  I want to call the PostIssue HTTP agent
  So that a new issue is created and I receive a confirmation JSON response

  Scenario: Successful issue creation with Title, Description, and Priority
    Given an HTTP request is sent to the PostIssue agent
      And the request contains a "Title" value
      And the request contains a "Description" value
      And the request contains a "Priority" value
    When the PostIssue agent executes
    Then a new IssueForm document is created and saved
      And the document's Title matches the request "Title"
      And the document's Description matches the request "Description"
      And the document's Priority matches the request "Priority"
      And the document's Status is set to "New"
      And the document has a non-empty IssueID
      And the document has CreatedBy set to the executing user
      And the document has CreatedDate set to the current date and time
      And the HTTP response Content-Type is "application/json"
      And the HTTP response body contains "status":"ok"
      And the HTTP response body contains the created IssueID value

  Scenario: Missing Priority defaults and still returns JSON
    Given an HTTP request is sent to the PostIssue agent
      And the request contains a "Title" value
      And the request contains a "Description" value
      And the request does not contain a "Priority" value
    When the PostIssue agent executes
    Then a new IssueForm document is created and saved
      And the document's Title matches the request "Title"
      And the document's Description matches the request "Description"
      And the document's Priority is set to the default (for example "Medium")
      And the document's Status is set to "New"
      And the document has a non-empty IssueID
      And the HTTP response Content-Type is "application/json"
      And the HTTP response body contains "status":"ok"
      And the HTTP response body contains the created IssueID value
```


