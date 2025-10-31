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
