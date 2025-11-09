# 🌴 VACATION TRACKING SYSTEM (VTS) — Vision & Requirements

## 🧠 Vision
VTS aims to increase employee independence by improving internal business processes.  
It provides a solution to manage employment agreements and leave requests — reducing time and cost.

---

## 🏢 Domain Problem
Centered around the management of employee vacation time, sick leave, and personal time off within an organization.

---

## 👥 Actors
- **Workers**
- **Managers**
- **Clerks**

---

## ⚙️ Functional Requirements
- Implements a flexible, rules-based system for validating and verifying leave-time requests.  
- Enables manager approval (optional).  
- Provides access to requests for the previous calendar year and allows requests up to 18 months in the future.  
- Uses email notifications to request manager approval and notify employees of request status changes.  
- Uses existing hardware and middleware.  
- Is implemented as an extension to the existing intranet portal system and uses the portal’s single sign-on mechanisms for authentication.  
- Keeps activity logs for all transactions.  
- Enables HR and system administration personnel to override restricted actions (with logging).  
- Allows managers to directly award personal leave time (within system-set limits).  
- Provides a web service interface for querying employee vacation summaries.  
- Interfaces with HR legacy systems to retrieve employee information and updates.

---

## 🧭 Non-Functional Requirements
- The system must be **easy to use**.
- Use existing hardware and middleware.
- The portal’s single-sign-on mechanisms manages roles and permissions the HR legacy has a working API to retrieve employee data.

---

## 🎯 Use Case — Manage Time

**Use case name:** Manage Time  
**Actor:** Employee  
**Goal:** Submit a new vacation time request.  
**Preconditions:** The employee is authenticated via the portal and has privileges to manage vacation time.

### 🪜 Main Flow
1. The employee opens the VTS link from the intranet portal.  
2. VTS retrieves the employee’s current vacation requests and balances (past 6 months and up to 18 months ahead).  
3. The employee selects a vacation category with a positive balance.  
4. VTS prompts for vacation dates and hours (with a visual calendar).  
5. The employee enters the desired dates, hours, title, and description, then submits.  
6. If data is incomplete or invalid, the form is redisplayed with errors highlighted.  
7. The employee can modify or cancel the request.  
8. If valid, VTS confirms submission and, if required, sends an approval email to the manager.  
9. The request status becomes **Pending Approval**.  
10. The manager accesses the approval link or logs into the VTS.  
11. The manager authenticates (if required).  
12. VTS shows the manager’s own requests and those pending from subordinates.  
13. The manager reviews each request and approves or rejects it (with reason if rejected).  
14. The system notifies the employee by email and updates the request status.

---

## 🧩 Sequence Diagram
![Sequence Diagram](SequenceDiagram.png)

---

## 🔁 Flow Chart
![Flow Chart](FlowChart.png)

---

## 🔁 State Management
![Cancel Request State Diagram](Cancel-State-Diagram.png)

---

## 💻 Pseudocode

```plaintext
FUNCTION processWorkerRequest(workerId, requestData):

    IF NOT AuthService.isAuthenticated(workerId):
        RETURN "Error: User not authenticated"
    ENDIF

    validationResult = Validator.validateRequest(requestData)
    IF validationResult == INVALID:
        RETURN "Error: Invalid request data"
    ENDIF

    workerType = Database.getWorkerType(workerId)

    IF workerType.requiresManagerApproval == TRUE THEN
        requestId = Database.saveRequest(workerId, requestData, status="PENDING")

        managerId = Database.getWorkerManager(workerId)
        approvalLink = WebServer.generateApprovalLink(requestId, managerId)
        EmailService.send(to=managerId,
                          subject="Approval Required",
                          body=approvalLink)

        RETURN "Request submitted and pending manager approval"
    ELSE
        requestId = Database.saveRequest(workerId, requestData, status="APPROVED")
        EmailService.send(to=workerId,
                          subject="Request Approved",
                          body="Your request has been auto-approved.")
        RETURN "Request approved automatically"
    ENDIF
END FUNCTION


FUNCTION handleManagerApproval(managerId, requestId, decision):

    IF NOT AuthService.isAuthenticated(managerId):
        RETURN "Error: Manager not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)

    IF decision == "APPROVE":
        Database.updateRequestStatus(requestId, "APPROVED")
        EmailService.send(to=request.workerId,
                          subject="Request Approved",
                          body="Your manager has approved your request.")
    ELSE IF decision == "REJECT":
        Database.updateRequestStatus(requestId, "REJECTED")
        EmailService.send(to=request.workerId,
                          subject="Request Rejected",
                          body="Your manager has rejected your request.")
    ELSE
        RETURN "Error: Invalid decision value"
    ENDIF

    RETURN "Manager decision processed successfully"
END FUNCTION


MODULE AuthService:
    FUNCTION isAuthenticated(userId):
        // Verify session or token validity
        RETURN TRUE or FALSE
    END FUNCTION
END MODULE


MODULE Validator:
    FUNCTION validateRequest(data):
        // Apply business rules for validation
        RETURN VALID or INVALID
    END FUNCTION
END MODULE


MODULE Database:
    FUNCTION getWorkerType(workerId)
    FUNCTION getWorkerManager(workerId)
    FUNCTION saveRequest(workerId, data, status)
    FUNCTION updateRequestStatus(requestId, status)
    FUNCTION getRequestById(requestId)
END MODULE


MODULE EmailService:
    FUNCTION send(to, subject, body):
        // Queue email for delivery
    END FUNCTION
END MODULE


MODULE WebServer:
    FUNCTION generateApprovalLink(requestId, managerId):
        RETURN "https://intranet.company.com/approve?req=" + requestId + "&mgr=" + managerId
    END FUNCTION
END MODULE

```
---

## 🎯 Withdraw Vacation Request

**Use case name:** Withdraw Vacation Request  
**Actor:** Employee  
**Goal:** Employee withdraws a pending vacation request before it is approved or rejected.
**Preconditions:** Employee must be authenticated The request status must be `Pending`.

### 🪜 Main Flow
1. Employee selects a pending request to withdraw.  
2. System prompts confirmation message.  
3. Employee confirms withdrawal.  
4. System updates request status to `Withdrawn`.  
5. Notification emails are sent to both manager and employee.  
6. Manager dashboard is refreshed with updated requests.

---

## 🧩 Sequence Diagram

#### Employee - Withdraw Vacation Request
![WithdrawRequest Emp Sequence Diagram](WithdrawRequest-Emp-SequenceDiagram.png)

---

#### Manager - Withdraw Vacation Request
![WithdrawRequest Manager Sequence Diagram](WithdrawRequest-Manager-SequenceDiagram.png)

---

## 🔁 Flow Chart
#### Employee Flow - Withdraw Vacation Request
![WithdrawRequest Emp Flowchart](WithdrawRequest-Emp-FlowChart.png)


---
#### Manager Flow - Withdraw Vacation Request
![WithdrawRequest Manager Flowchart](WithdrawRequest-Manager-FlowChart.png)

---

## 💻 Pseudocode

```plaintext
MODULE AuthService:
    FUNCTION isAuthenticated(userId):
        // Check session or token validity
        RETURN TRUE or FALSE
    END FUNCTION
END MODULE


MODULE Database:
    FUNCTION getRequestById(requestId):
        // Retrieve vacation request details from database
        RETURN RequestObject
    END FUNCTION

    FUNCTION updateRequestStatus(requestId, newStatus):
        // Update vacation request status in database
    END FUNCTION

    FUNCTION getManagerPendingRequests(managerId):
        // Get all pending requests for manager
        RETURN ListOfRequests
    END FUNCTION
END MODULE


MODULE EmailService:
    FUNCTION send(to, subject, body):
        // Send email notification
    END FUNCTION
END MODULE


MODULE UI:
    FUNCTION promptConfirmation(message):
        // Show confirmation dialog to user
        RETURN TRUE or FALSE
    END FUNCTION

    FUNCTION refreshDashboard(data):
        // Refresh manager dashboard UI
    END FUNCTION
END MODULE


FUNCTION processWithdrawRequest(employeeId, requestId):

    IF NOT AuthService.isAuthenticated(employeeId):
        RETURN "Error: Employee not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)

    IF request.status != "PENDING":
        RETURN "Error: Only pending requests can be withdrawn"
    ENDIF

    confirmation = UI.promptConfirmation("Are you sure you want to withdraw this request?")
    IF confirmation == FALSE:
        RETURN "Action canceled by employee"
    ENDIF

    Database.updateRequestStatus(requestId, "WITHDRAWN")

    managerId = request.managerId
    EmailService.send(to=managerId,
                      subject="Vacation Request Withdrawn",
                      body="Employee " + request.employeeName + " has withdrawn their vacation request.")

    EmailService.send(to=employeeId,
                      subject="Withdrawal Confirmed",
                      body="Your vacation request has been successfully withdrawn.")

    RETURN "Vacation request withdrawn successfully"
END FUNCTION


FUNCTION handleManagerNotification(managerId):

    IF NOT AuthService.isAuthenticated(managerId):
        RETURN "Error: Manager not authenticated"
    ENDIF

    updatedRequests = Database.getManagerPendingRequests(managerId)
    UI.refreshDashboard(updatedRequests)

    RETURN "Manager dashboard updated with latest requests"
END FUNCTION
```

---

## 🎯 Cancel Approved Request

**Use case name:** Cancel Approved Request  
**Actor:**   
**Goal:** The employee wants to cancel an approved vacation time request.
**Preconditions:** The employee has a vacation time request that has been approved and is scheduled for some time in the future or the recent past (previous 5 business days).

### 🪜 Main Flow
1. The employee navigates to the VTS home page through the intranet portal application, which identifies and authenticates the employee with the privileges necessary for using the VTS.
2. The VTS home page contains a summary of vacation time requests, outstanding balance per category of time, and the current status of all active vacation time requests for the previous 6 months and up to 18 months in the future.
3. The employee selects a vacation time request to cancel, one that is in the future (or recent past) and has been approved.
4. If the request is in the future, the employee is prompted to confirm the cancellation. If the request is in the recent past, the employee is prompted to confirm the cancellation and provide a short explanation. 
If the employee approves the cancellation and provides the required information, an e-mail notification is sent to the manager, and the state of the request is changed to canceled. The time allowances used to make the request are returned to the employee. The employee can also abort the cancellation, effecting no changes to the current requests.
5. The employee is returned to the main VTS home page. The summaries are updated to reflect any changes made to the employee’s outstanding vacation time requests.
   
---

## 🧩 Sequence Diagram

#### Employee - Cancel Approved Request
![Employee Cancel Request Sequence](Employee-CancelRequest-SequenceDiagram.png)

---

#### Manager - Handle Canceled Request
![Manager Handle Canceled Sequence](Manager-HandleCanceled-SequenceDiagram.png)

---

## 🔁 Flow Chart

#### Employee Flow - Cancel Approved Request
![Employee Cancel Flow](Employee-Cancel-Flowchart.png)

---

#### Manager Flow - Handle Canceled Request
![Manager Cancel Flow](Manager-Cancel-Flowchart.png)

---

## 🔁 State Management
![Cancel Request State Diagram](Cancel-State-Diagram.png)

---

## 💻 Pseudocode

```plaintext
FUNCTION processEmployeeCancellation(employeeId, requestId):

    IF NOT AuthService.isAuthenticated(employeeId):
        RETURN "Error: Employee not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)
    IF request.status != "APPROVED":
        RETURN "Error: Only approved requests can be canceled"
    ENDIF

    IF request.date >= Today():
        confirmation = UI.promptConfirmation("Cancel upcoming request?")
        reason = NULL
    ELSE IF request.date >= Today() - 5 days:
        (confirmation, reason) = UI.promptWithReason("Cancel recent request?", "Provide reason:")
    ELSE
        RETURN "Error: Too old to cancel"
    ENDIF

    IF NOT confirmation:
        RETURN "Canceled aborted by employee"
    ENDIF

    Database.updateRequestStatus(requestId, "CANCELED")
    Database.returnVacationAllowance(employeeId, request.duration)
    EmailService.send(to=request.managerId, subject="Request Canceled", body="Employee canceled approved vacation")
    UI.refreshHomePage(employeeId)

    RETURN "Request successfully canceled"
END FUNCTION


FUNCTION handleManagerCancellationNotification(managerId, requestId):

    IF NOT AuthService.isAuthenticated(managerId):
        RETURN "Error: Manager not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)
    IF request.status == "CANCELED":
        UI.displayNotification(managerId, "Employee canceled approved request")
    ELSE
        RETURN "No action required"
    ENDIF

    RETURN "Manager notified successfully"
END FUNCTION

```

---

## 🎯 Edit Pending Request

**Use case name:** Cancel Approved Request  
**Actor:**   
**Goal:** The employee wants to edit the description or title of a pending request.
**Preconditions:**  An employee has made a vacation time request, and thatrequest has yet to be approved or denied by an authorized manager. 
**Preconditions:** An employee has made a vacation time request, and that request has yet to be approved or denied by an authorized manager.

### 🪜 Main Flow
1. The employee navigates to the VTS home page through the intranet portal application, which identifies and authenticates the employee with the privileges necessary for using the VTS.
2. The VTS home page contains a summary of vacation time requests, outstanding balances per category of time, and the current status of all
active vacation time requests for the previous 6 months and up to 18 months in the future.
3. The employee selects a request to edit, one that is pending approval.
4. The VTS displays an editable view of the request. The employee is allowed to change the title, comments, or dates. The employee can also
choose to delete or withdraw this request.
5. The employee changes request information and submits the changes to the system.
6. If the employee withdraws the request, the VTS prompts for confirmation before withdrawing the request. If changes are made only to the information, the changes are accepted, and the screen returns to the main VTS home page. If there are errors or problems with the information changes, the VTS redisplays the editing page and highlights and
explains all problems.
   
---

## 🧩 Sequence Diagram

#### 🧑‍💼 Employee - Edit Pending Request
![Employee Edit Pending Sequence](Employee-EditPending-SequenceDiagram.png)

---

## 🔁 Flow Chart

#### Employee Flow
![Employee Edit Pending Flowchart](Employee-EditPending-Flowchart.png)

---

## 🔁 State Management
![Edit Pending State Diagram](EditPending-StateDiagram.png)
---

## 💻 Pseudocode

```plaintext
FUNCTION processEditPendingRequest(employeeId, requestId, updatedData, action):

    IF NOT AuthService.isAuthenticated(employeeId):
        RETURN "Error: User not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)
    IF request.status != "PENDING":
        RETURN "Error: Only pending requests can be edited or withdrawn"
    ENDIF

    IF action == "WITHDRAW":
        confirmation = UI.promptConfirmation("Withdraw this request?")
        IF NOT confirmation:
            RETURN "Withdrawal canceled by employee"
        ENDIF

        Database.updateRequestStatus(requestId, "WITHDRAWN")
        EmailService.send(to=request.managerId,
                          subject="Request Withdrawn",
                          body="Employee has withdrawn a pending request.")
        RETURN "Request withdrawn successfully"
    ELSE IF action == "EDIT":
        validation = Validator.validateRequest(updatedData)

        IF validation == INVALID:
            UI.displayFormErrors(validation.errors)
            RETURN "Error: Invalid updated data"
        ENDIF

        Database.updateRequest(requestId, updatedData)
        RETURN "Request updated successfully"
    ELSE
        RETURN "Error: Unknown action"
    ENDIF

END FUNCTION


MODULE AuthService:
    FUNCTION isAuthenticated(userId)
        // check session or token
    END FUNCTION
END MODULE

MODULE Validator:
    FUNCTION validateRequest(data)
        // check fields and rules
    END FUNCTION
END MODULE

MODULE Database:
    FUNCTION getRequestById(requestId)
    FUNCTION updateRequest(requestId, updatedData)
    FUNCTION updateRequestStatus(requestId, status)
END MODULE

MODULE EmailService:
    FUNCTION send(to, subject, body)
END MODULE

MODULE UI:
    FUNCTION promptConfirmation(message)
    FUNCTION displayFormErrors(errors)
END MODULE

```

---

What if we need to have in the future another status like HR_Pending, HR_Approval with minimum change?

IF During Designing we get it as fact that the request stauts will be changes it will better to design it as Table in Database not as enum so the change will not need Code change 

