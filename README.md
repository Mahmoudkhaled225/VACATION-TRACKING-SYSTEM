title: "VACATION TRACKING SYSTEM — Vision & Requirements"
tags:
    - vision
    - requirements
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


use_cases:
    - id: UC-ManageTime
        name: "Manage Time"
        actor: "Employee"
        goal: "The employee wishes to submit a new request for vacation time."
        preconditions: "The employee is authenticated by the portal framework and identified as an employee with privileges to manage own vacation time."
        main_flow: |
            1. The employee selects a link from the intranet portal to the VTS.
            2. VTS looks up the current status of all the employee’s vacation requests and outstanding balances and displays information for the previous 6 months and up to 18 months in the future.
            3. The employee selects a vacation category with a positive balance.
            4. VTS prompts for date(s) and time with a visual calendar.
            5. The employee selects dates and hours per date, enters a short title and description (≤ 1 paragraph), and submits the request.
            6. If information is incomplete/invalid, the page is redisplayed with errors highlighted.
            7. The employee may change the information or cancel the request.
            8. If valid, the employee returns to the VTS home page; if manager approval is required, an email is sent to the authorized manager(s).
            9. The request is placed in a pending approval state.
            10. The manager follows the email link or logs in to the portal and navigates to VTS.
            11. The manager may be required to authenticate.
            12. VTS shows manager’s own requests and a section for subordinate requests pending approval.
            13. The manager views details, approves or rejects (rejection requires an explanation); the request state is updated accordingly.
            14. An email notification is sent to the employee; the manager returns to the VTS home page.
        artifacts:
            sequence_diagram: "diagrams/manage_time_sequence.png"
            flow_chart: "diagrams/manage_time_flowchart.png"
        notes: "Place the two diagram files in the repository under the diagrams/ directory. Reference them in the document body where you want them displayed."
