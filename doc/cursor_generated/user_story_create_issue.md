## User Story: Create Issue via CreateIssueAgent

**As** an authenticated Domino user  
**I want** to run the CreateIssueAgent to create a new issue record  
**So that** the issue is persisted with a unique ID, required fields are saved, and the assignee is optionally notified.

### Description
- The CreateIssueAgent creates a new `IssueForm` document with system-generated `IssueID`, user-supplied fields, defaults for required fields, and saves it to the Domino database.
- When `NotifyOnCreate` is set to `1`, the agent sends a notification to the assigned user.
- The agent completes without errors and provides confirmation.

### Acceptance Criteria
1) **Successful create**  
   - Given CreateIssueAgent runs, when it executes, then a new `IssueForm` document is saved.
2) **Unique IssueID**  
   - The saved document contains a generated `IssueID` following the expected pattern (e.g., `ISS-YYYYMMDD-HHMMSS-####`).
3) **Field persistence**  
   - Title, Description, Priority, Status, CreatedBy, CreatedDate, AssignedTo, and NotifyOnCreate values are stored on the new document.
4) **Defaults applied**  
   - If not provided, `Priority` defaults to Medium and `Status` defaults to New/Open per form/agent defaults.
5) **Notification opt-in**  
   - When `NotifyOnCreate=1`, an email is sent to `AssignedTo` with subject containing the `IssueID`; when `NotifyOnCreate` is not `1`, no email is sent.
6) **No duplicate save**  
   - Agent creates exactly one document per execution.
7) **Confirmation**  
   - Agent finishes without unhandled errors and can display/log a confirmation (e.g., messagebox or console log).

### Notes / Out of Scope
- Notification delivery mechanics (mail routing) beyond agent send call are out of scope.
- SLA fields and related views are not modified by this agent.

