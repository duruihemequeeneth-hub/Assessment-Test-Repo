**QA Assessment Report: Lendsqr Adjutor API Platform**

Prepared For: Capital Credit Limited  
Prepared By: Duruiheme Queeneth Ogochukwu, Senior Quality Assurance Engineer  
Date: September 8, 2025  
**Objective: For Capital Cash App Integration**

**Executive Summary**

This documentation summarizes a thorough quality assessment of Lendsqr’s Adjutor API platform, conducted to evaluate its suitability for powering the “Capital Cash” app. It covers functional, non-functional and vulnerability testing.

**Overall Conclusion:** The Adjutor API platform is a strong foundation with secure authentication practices and well structured endpoints for key management. However, the assessment uncovered:

* critical bugs in the lender onboarding (Signup) process and;  
* a non-standard behavior in the login flow that blocks a core functionality. 

A successful integration is contingent upon Lendsqr resolving these issues.

**Assessment Scope**

This assessment focused on three core functional modules:

1. Signup: process for a new lender to create an account  
2. Login: Process for an authenticated lender to access the platform  
3. API Key Retrieval: Process for a lender to access their integration credentials

**Tool Stack Utilized:** 

* Browser Developer Tools (Chrome): for API discovery, monitoring network traffic and inspecting headers and payloads.  
* Google Sheets: repository for test documentation, management and reporting  
* [Node.js](http://Noe.js): runtime environ for Postman’s execution  
* Postman: API exploration, manual testing and automation scripting

**Task Analysis**

**Task 1 \- API Discovery:** I traced the UI using browser dev. tools to monitor network activity while manually performing each task (signup, login, navigating to the API keys section) in the Adjutor web interface. This method helped discover the actual API endpoints, headers and payload structures used by the front-end.   
**Decisions and Outcomes:** 

* Decision: To document positive, negative (error condition) and edge cases where applicable for each module using google sheets.  
* Outcome: Comprehensive coverage. For example, the SignUp module included tests for valid data, duplicate emails and missing fields. Key discoveries: generic error messages.

**Task 2 \- Automated Test Script Creation:** I translated the manual test cases from Task 1 into automated scripts within Postman, configuring each request with its header and body. JavaScript validation scripts for status codes and response bodies were written on the Tests tab.    
**Setup and Execution:** 

1. Login into the Adjutor web UI while simultaneously using DevTools to access network  
2. Import API URL to GET or POST  
3. Set variable within the collection and navigate other collection runner in Postman  
4. Click SEND to execute all automated tests.

**Decisions and Outcomes:** 

* Decision:   
  * To use the API key and authorization token directly for authentication tests, bypassing the complex UI login flow.   
  * To validate both HTTP status codes and the response body.  
* Outcome: Stable and reliable automation, discovery of the logic flaw where the login PI returns a 400 error for a successful request. A finding that may be missed by validating status codes alone.

**Results Summary for Task 2:** 

| Module | API Endpoint | Test Case | Result | Evidence |
| :---: | :---: | :---: | :---: | :---: |
| Signup | POST https://vigil.lendsqr.com/pecunia/api/v2/onboard | Positive (Valid Data) | FAIL | 500 Internal Server Error. Severity \- critical |
|  |  | Negative | PASS | Correctly returned 400 (expected 500\) |
| Login | POST https://vigil.lendsqr.com/pecunia/api/v2/onboard | Positive (Valid Credentials) | PASS | Validated non-standard 400 response with redirect |
|  |  | Negative (Invalid Password) | PASS | Correctly returned 400 with specific error message |
| API Key Retrieval  | GET https://vigil.lendsqr.com/pecunia/api/v1/admin/adjutor/apps?page=0\&limit=5 | Positive (Authenticated) | PASS | All 5 validation tests passed |
|  |  | Negative (Authenticated) | PASS | Correctly returned 401 Unauthorized |

**Task 3 \- Performance Inference:** Data gathered from Postman’s response timings during functional testing indicated adequate performance for available endpoints under no-load conditions.  
**Recommendations:** 

1. Fix SignUp Endpoint: the 500 error represents a 0% success rate. Highest priority.  
2. Conduct Load Tests: test performance under realistic user loads to identify bottle necks  
3. Implement Rate Limiting: Protect authentication endpoints from unlimited requests

**Task 4 \- Security Vulnerabilities:** Analyzed API responses against OWASP API Security Top 10\.  
**Findings:** 

1. Broken Functionality (Critical): the POST /onboard \- Signup Module endpoint is completely non-functional (500 error).   
2. Improper HTTP Status Code Usage (Medium): the POST /login \- Login endpoint returns a 400 Bad Request for a successful login in the form of a redirect to the adjutor web. This could be a logic flaw.  
3. Generic Error Messages (Low): the use of  “an error occurred, please contact support” hinders debugging but also prevents information leakage (a positive).  
4. Robust Authentication (A positive): the x-api-key (client ID) and authorization token (user session) is a secure implementation.

**Conclusion**  
The Lendsqr Adjutor API shows promise. The Capital Cash app can be integrated upon a reassessment is scheduled and a confirmation that the high priority issues have been resolved . The automated test suite created in this assessment can then be re-executed to verify the fixes.