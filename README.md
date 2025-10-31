---
title: "VACATION TRACKING SYSTEM  — Vision & Requirements"
---

# Vision
VTS aims to increase employee independence by improving internal business processes. It provides a solution to manage employment agreements and leave requests, reducing time and cost.

# Actors
- Workers
- Managers
- Clerks

# Functional Requirements
- Implements a flexible rules-based system for validating and verifying leave-time requests.  

- Enables manager approval (optional).  

- Provides access to requests for the previous calendar year, and allows requests to be made up to a year and a half in the future.  

- Uses e-mail notification to request manager approval and notify employees of request status changes.  

- Uses existing hardware and middleware.  

- Is implemented as an extension to the existing intranet portal system, and uses the portal’s single-sign-on mechanisms for all authentication.  

- Keeps activity logs for all transactions.  

- Enables the HR and system administration personnel to override all actions restricted by rules, with logging of those overrides.  

- Allows managers to directly award personal leave time (with system-set limits).  

- Provides a Web service interface for other internal systems to query any given employee’s vacation request summary.  

- Interfaces with the HR department legacy systems to retrieve required employee information and changes.  

# Non-Functional Requirements
- The system must be easy to use.

# Use Case — Manage Time

Use case name: Manage Time  
Actor: Employee  
Goal: The employee wishes to submit a new request for vacation time.  
Preconditions: The employee is authenticated by the portal framework and identified as an employee of the company with privileges to manage his or her own vacation time.

Main flow:
1. The employee begins by selecting a link from the intranet portal to the VTS.
2. The VTS uses the employee’s credentials to look up the current status of all the employee’s vacation time requests and outstanding balances. Information is displayed for the previous 6 months and up to 18 months in the future.
3. The employee wants to create a new request. The employee selects one of the categories of vacation time with a positive balance to use.
4. The VTS prompts the employee for the date(s) and time for which to request vacation time. The employee should have access to a visual calendar to help select and compare chosen dates.
5. The employee selects the desired dates and hours per date (e.g., four hours might indicate a half-day vacation time request). The employee enters a short title and description (no more than a paragraph in length) so that the manager will have more information with which to approve this request. When all the information is entered, the employee submits the request.
6. If the submitted information is incomplete or incorrect or does not pass validation, the Web page is redisplayed, with the errors highlighted and documented.
7. The employee has an opportunity to change the information or cancel the request.
8. If the information is complete and passes validation, the employee is returned to the main VTS home page. If the employee’s vacation time requests require manager approval, an e-mail is immediately sent to the manager(s) authorized to approve the employee’s requests.
9. The vacation time request is placed in a state of pending approval.
10. The manager responds to the e-mail by clicking on a link embedded in the e-mail or by explicitly logging into the intranet portal and navigating to the main VTS home page.
11. The manager may be required to supply necessary authentication credentials to gain access to the portal and VTS application.
12. The VTS home page lists the manager’s own vacation time requests and outstanding balances but also has a separate section listing requests pending approval by subordinate employees. The manager selects each of these one at a time to individually approve or deny.
13. The VTS displays the details of the requested time and prompts the manager to approve or disapprove the request. If the request is disapproved, the manager is required to enter an explanation. Once the manager submits the result, the internal state of the request is changed to approved or rejected.
14. Whether a request is approved or rejected, an e-mail notification is immediately sent to the employee who made the request. The manager’s screen returns to the main VTS home page, and the manager may approve other outstanding requests, make a request for him- or herself, or simply leave the VTS application.

and this is the sequence diagram of this use case
[sequence diagram img](SequenceDiagram.png)

and this is the flow chart of this use case
[flow chart img](FlowChart.png)

and finally Psudeocode 

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
        EmailService.send(to=managerId, subject="Approval Required", body=approvalLink)

        RETURN "Request submitted and pending manager approval"

    ELSE
        requestId = Database.saveRequest(workerId, requestData, status="APPROVED")
        EmailService.send(to=workerId, subject="Request Approved", body="Your request has been auto-approved.")
        RETURN "Request approved automatically"
    ENDIF
END FUNCTION


FUNCTION handleManagerApproval(managerId, requestId, decision):

    IF NOT AuthService.isAuthenticated(managerId):
        RETURN "Error: Manager not authenticated"
    ENDIF

    request = Database.getRequestById(requestId)
    workersData = Database.getWorkersUnderManager(managerId)

    IF decision == "APPROVE":
        Database.updateRequestStatus(requestId, "APPROVED")
        EmailService.send(to=request.workerId, subject="Request Approved",
                          body="Your manager has approved your request.")

    ELSE IF decision == "REJECT":
        Database.updateRequestStatus(requestId, "REJECTED")
        EmailService.send(to=request.workerId, subject="Request Rejected",
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
    FUNCTION getWorkersUnderManager(managerId)
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
