# **Comprehensive Functional Test Plan for Knowledge Commons**

**Document Version:** 0.1

**Date:** November 26, 2025

**Author:** Digital Initiatives 

## **1. Introduction**

### **1.1. Purpose**
This document outlines the complete functional testing strategy for the DSpace Institutional Repository. The goal is to verify that all key features and user workflows operate correctly according to the specified requirements, ensuring data integrity, security, interoperability, and a positive user experience.

### **1.2. Scope**
This test plan covers the core functionalities of DSpace from the perspectives of different user roles:
- **Anonymous User (Public Visitor)**
- **Registered User (Researcher/Submitter)**
- **Collection/Community Administrator**
- **System Administrator**

It includes both the user interface and machine-readable interfaces (OAI-PMH) of the DSpace application.

### **1.3. Definitions & Acronyms**
- **IR:** Institutional Repository
- **UI:** User Interface
- **OAI-PMH:** Open Archives Initiative Protocol for Metadata Harvesting
- **Item:** A single, archived entry in the repository (e.g., a thesis, article, dataset)
- **Collection:** A container for Items
- **Community:** A top-level container for Collections or sub-communities
- **Workflow:** The process an Item goes through after submission (e.g., review, approval)
- **Metadata:** Descriptive information about an Item (e.g., title, author, date)
- **Bitstream:** A digital file contained within an Item

---

## **2. Test Approach & Strategy**

- **Environment:** Testing will be performed on a dedicated **Staging/Test Environment** that mirrors the production setup as closely as possible
- **Data:** Test data will be a mix of manually created content and sanitized copies of production data
- **Methodology:** Combination of **manual testing** for end-to-end user journeys and **exploratory testing** for usability and edge cases
- **Roles:** Testers will assume the personas of different user types to execute test cases
- **Schedule:** Testing will occur before production deployments, after major upgrades, and quarterly for regression testing

---

## **3. Test Environment & Data**

- **DSpace Version:** DSpace 8.2
- **URL:** https://xxx.lakeheadu.ca
- **Database:** PostgreSQL 13
- **Java Version:** OpenJDK 17


### **Prerequisites:**
- At least one Community and one Collection must be set up
- A workflow process must be configured (e.g., with an accept/reject step)
- OAI-PMH module must be enabled and configured
- Test files of various types and sizes available for upload

---

## **4. Test Cases**

### **Module 1: Public Access & Browsing**

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **PUB-01** | Browse Communities & Collections | Repository is populated | 1. Go to the homepage<br>2. Click on a Community<br>3. Click on a Collection | The hierarchical structure is displayed correctly. Items within the Collection are listed | Pass | All links are directing to the proper page.|
| **PUB-02** | Search for Items | Items exist with known metadata | 1. Use the global search bar<br>2. Enter a known keyword from an Item's title | The relevant Item appears in the search results | Pass | Tested: 1. "phrase searching"; 2. exclude -; 3. OR; <mark>Do we have an advance search page?</mark>|
| **PUB-03** | View Item Detail Page | An Item is publicly accessible | 1. From a search or browse result, click on an Item title | All Item metadata is displayed correctly. The "Download" button/link for the primary bitstream is visible and functional | Pass | Randomly checked 10 items, all metadata fields display properly. However, minor issue found on both testing and previous site: some are showing <mark>en_US multiple times near different fields</mark>, eg.https://tempserver-199.lakeheadu.ca/items/13dea760-a9af-4f67-a713-4764f93f509d/full vs https://tempserver-199.lakeheadu.ca/items/3f970794-fbbb-4bbc-907c-101e1c68ebf0/full  |
| **PUB-04** | Download Public File | An Item with a bitstream is publicly accessible | 1. Navigate to an Item Detail page<br>2. Click the "Download" button or a bitstream link | The file downloads successfully and is not corrupted | Pass | Randomly downloaded 10 different files, there was no corrupted and file integrity is validated after download. |
| **PUB-05** | Restricted Access Compliance | An Item or file has restricted access | 1. Attempt to access an Item with an embargo date in the future<br>2. Attempt to download a file with "Private" access | A "Request a copy of the document" or "Login Required" message is displayed. The file cannot be downloaded | Pass | Tessted to download ristriced documents when logged out, not allowing downloading as expected. |

Addtional note:
When searching with keyword, pervious site's result may contain the items in other languages whose translation matchs key word, but they won't show on the testing site. For example, when searching "international students -Lakehead", the previous site will show 11 resuult, while the testing site only show 9. The two extra results are in Spanish but matches the key word "student" after translation.(see details [here](/dspace-test-1.png)) <mark>Is this expected?</mark>


### **Module 2: User Submission & Workflow**

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **SUB-01** | User Registration | Registration is enabled | 1. Click "Register"<br>2. Fill out the form (email, password, etc.)<br>3. Submit | Account is created. A verification email is received (if configured). User can log in | N/A | Test email verification flow if enabled |
| **SUB-02** | Start New Submission | User is logged in. A Collection exists | 1. Go to a Collection that allows submissions<br>2. Click "Submit to this Collection" | The submission process/workspace starts | Pass | Created New Collections and Items |
| **SUB-03** | Complete Submission Steps | A submission is in progress | 1. Fill in all required metadata (e.g., Author, Title, Date)<br>2. Upload a file<br>3. Progress through all steps (e.g., Describe, Upload, License)<br>4. Click "Complete Submission" | Submission is successful. A success message is shown. The item moves to the workflow (e.g., "Waiting for review"). The submitter sees it in their "Submissions" list | Pass | Create New Item and reveive emaiil confirmation. Test with multiple file types(pdf, docx, png), item can be set to undiscoverable and worked. |
| **SUB-04** | Save Submission Draft | User has started a submission | 1. Fill in partial metadata<br>2. Click "Save for Later" | Submission is saved in user's personal workspace and can be resumed later | Pass | Draft can be resumed |
| **WF-01** | Accept Submission (Reviewer) | An item is in the review step | 1. Log in as a reviewer/editor<br>2. Go to "My Tasks" or the review pool<br>3. Select the task and "Accept" the item | Item moves to the next workflow step (e.g., to an admin for final approval) or is archived directly | | |
| **WF-02** | Reject Submission (Reviewer) | An item is in the review step | 1. Log in as a reviewer/editor<br>2. Go to "My Tasks"<br>3. Select the task and "Reject" the item. Provide a reason | Item is rejected and moved to the submitter's "Failed" submissions. The submitter is notified (if configured) | | Verify notification is sent |

### **Module 3: Administrative Tasks**

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ADMIN-01** | Create a Community | User has Admin rights | 1. Log in as an admin<br>2. Navigate to the "Communities & Collections" page<br>3. Click "Create Community"<br>4. Fill in details (name, logo, etc.) | The new community is created and visible on the homepage | Pass | Created New Community and Sub-community |
| **ADMIN-02** | Create a Collection | A Community exists | 1. Go to a Community<br>2. Click "Create Collection"<br>3. Fill in details (name, license, workflow groups) | The new collection is created within the community and is available for submissions | Pass | Test workflow group assignment |
| **ADMIN-03** | Manage Item (Edit Metadata) | An item exists | 1. Navigate to an Item's detail page<br>2. Log in as an admin/manager of its collection<br>3. Click "Edit"<br>4. Change a metadata value (e.g., title) and save | The changes are saved and immediately visible on the public item page | Pass | Test various metadata fields |
| **ADMIN-04** | Manage Item (Add Bitstream) | An item exists | 1. From the Item edit view, go to the "Bitstreams" section<br>2. Upload a new file | The new file is added to the item and is downloadable from the public view | Pass | Updated new file to existing items |
| **ADMIN-05** | Withdraw an Item | An item is publicly available | 1. Navigate to the Item<br>2. As an admin, select "Withdraw" | The item is no longer publicly findable via search/browse. A "This item has been withdrawn" tombstone page is displayed in its place | Pass | Verify tombstone page displays correctly. Admin received pending suggestion immediately. |
| **ADMIN-06** | Reinstate a Withdrawn Item | A withdrawn item exists | 1. Find the withdrawn item (via admin tools)<br>2. Select "Reinstate" | The item is restored and becomes publicly accessible again with all its original metadata and files | Pass | Resinstance from "Admin Search"(sidebar) - Filters - Withdrawn |
| **ADMIN-07** | Manage User Groups | User has Admin rights | 1. Navigate to Administration > Groups<br>2. Create a new group<br>3. Add users to the group<br>4. Assign group to a collection | Group is created and can be used for permissions and workflows | | |

### **Module 4: User Account Management**

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **USER-01** | Password Reset | A user account exists | 1. On the login page, click "Forgot Password"<br>2. Enter the account's email<br>3. Check email and follow the reset link<br>4. Set a new password | Password reset email is sent. User can log in with the new password | | Test email content and link expiry |
| **USER-02** | View/Edit User Profile | User is logged in | 1. Click on the user's profile name<br>2. Navigate to "Profile"<br>3. Edit information (e.g., phone number) and save | Changes are saved successfully | | |
| **USER-03** | View Submission History | User has made submissions | 1. Log in<br>2. Navigate to "My Submissions" or similar section | User can see all their submissions with current status (in progress, waiting review, published, etc.) | | |

### **Module 5: System & Integration**

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **SYS-01** | LDAP/SSO Login | LDAP/SSO is configured | 1. Go to the login page<br>2. Click the institutional login button<br>3. Enter institutional credentials | User is authenticated and logged into DSpace seamlessly | | Test various LDAP attributes mapping |
| **SYS-02** | Handle Persistence | Handle server is configured | 1. Submit a new item<br>2. After it is archived, note its Handle URL (e.g., `hdl:12345/67890`)<br>3. Access the item via that URL | The permanent Handle URL resolves correctly to the item page | | Verify Handle doesn't change after edits |
| **SYS-03** | ORCID Integration | ORCID is configured | 1. During submission or profile editing, attempt to link an ORCID iD<br>2. Authenticate with ORCID | The ORCID iD is successfully linked and displayed in the user's profile or the item's metadata | | Test both authentication and metadata display |
| **SYS-04** | Statistics Viewing | Statistics are enabled | 1. View an item detail page as admin<br>2. Check statistics reports | Item view/download statistics are visible and accurate | | Test various statistic reports |

### **Module 6: OAI-PMH Interface**

**Base OAI-PMH Endpoint URL:** `[e.g., https://your-repo.org/oai/request?verb=Identify]`

| Test Case ID | Description | Preconditions | Test Steps | Expected Result | Status (Pass/Fail) | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **OAI-01** | **Identify Verb** | OAI-PMH module is enabled | 1. Open a browser or tool like `curl`<br>2. Call the base URL with the `Identify` verb: `.../oai/request?verb=Identify` | An XML response is returned containing the repository's administrative details: `<repositoryName>`, `<baseURL>`, `<protocolVersion>2.0</protocolVersion>`, and `<adminEmail>` | | Validate XML structure |
| **OAI-02** | **ListMetadataFormats Verb** | Repository is populated | 1. Call the endpoint with the `ListMetadataFormats` verb: `.../oai/request?verb=ListMetadataFormats` | An XML response lists available metadata formats, including at a minimum `<metadataPrefix>oai_dc</metadataPrefix>` (Dublin Core). Others like `mods`, `mets`, `qualified-dc` may also be present | | |
| **OAI-03** | **ListSets Verb** | Communities/Collections exist in the repository | 1. Call the endpoint with the `ListSets` verb: `.../oai/request?verb=ListSets` | An XML response lists the top-level communities as "sets". Each set should have a `<setSpec>` (a unique identifier) and a `<setName>` (the human-readable name) | | Verify all major communities are listed |
| **OAI-04** | **ListIdentifiers Verb (Basic)** | Public items exist | 1. Call the endpoint with the `ListIdentifiers` verb and `oai_dc` format: `.../oai/request?verb=ListIdentifiers&metadataPrefix=oai_dc` | An XML response returns a list of `<header>` elements, each containing a unique `<identifier>` (the OAI-PMH ID, often based on the item's Handle) for each public item. No metadata is returned in this verb | | |
| **OAI-05** | **ListIdentifiers Verb (with Set)** | Items exist within a specific collection/community | 1. Perform **OAI-03** to get a `setSpec`<br>2. Call: `.../oai/request?verb=ListIdentifiers&metadataPrefix=oai_dc&set=[setSpec]` | The XML response only lists `<header>` elements for items that belong to the specified set | | Test with multiple sets |
| **OAI-06** | **ListRecords Verb (Basic Harvest)** | Public items exist | 1. Call the endpoint with the `ListRecords` verb: `.../oai/request?verb=ListRecords&metadataPrefix=oai_dc` | An XML response returns a list of `<record>` elements. Each record must include a `<header>` (with identifier, datestamp) and a `<metadata>` section containing the full Dublin Core metadata for the item | | Validate DC metadata completeness |
| **OAI-07** | **GetRecord Verb (Single Item)** | A known public item exists | 1. Perform **OAI-04** to get a specific item `<identifier>`<br>2. Call: `.../oai/request?verb=GetRecord&metadataPrefix=oai_dc&identifier=[OAI-ID]` | The XML response returns a single, complete `<record>` for the specified item, including all its Dublin Core metadata | | Compare with web UI metadata |
| **OAI-08** | **Resumption Token (Paging)** | The repository has more items than the `pageSize` limit (default is often 100) | 1. Perform **OAI-06**<br>2. Check the response for a `<resumptionToken>` element<br>3. Call the endpoint again with the token: `.../oai/request?verb=ListRecords&resumptionToken=[token]` | The initial response includes a `<resumptionToken>`. The subsequent call using this token returns the next "page" of records. This continues until a final, empty token is provided | | Test complete pagination flow |
| **OAI-09** | **Respects Deleted Records** | An item has been withdrawn from the repository. `item.oaipmh.delete` is set to `true` in `dspace.cfg` | 1. Perform **OAI-04** or **OAI-06** | In the `<header>` for the withdrawn item, the `status` attribute is set to `status="deleted"`. E.g., `<header status="deleted">...`. The `<metadata>` section is omitted for deleted records | | Verify tombstoning in OAI |
| **OAI-10** | **Respects Embargoes/Restrictions** | An item exists with an embargo date in the future | 1. Perform a `ListRecords` harvest | The metadata for the embargoed item **does not appear** in the OAI-PMH feed. OAI-PMH should only expose metadata for items that are publicly accessible | | Test various access conditions |
| **OAI-11** | **Date-Based Selective Harvesting** | Items were submitted on known dates | 1. Call the endpoint with a `from` and/or `until` date parameter: `.../oai/request?verb=ListRecords&metadataPrefix=oai_dc&from=2024-01-01` | The XML response only returns records for items that were added/modified on or after the specified `from` date | | Test multiple date ranges |

---

## **5. Defect Reporting**

Any failed test case should result in a defect report logged in your tracking system (e.g., Jira, GitHub Issues). The report should include:

- **Defect ID** (auto-generated by tracking system)
- **Title** (Short, descriptive summary)
- **Description** (Detailed steps to reproduce, including specific data used)
- **Test Case ID** (from this plan)
- **Severity** (Critical, Major, Minor, Trivial)
- **Priority** (High, Medium, Low)
- **Environment** (Test Environment URL, DSpace Version, Browser/OS if applicable)
- **Screenshots/Logs** (if applicable)
- **Expected vs. Actual Results**

### **Severity Definitions:**
- **Critical:** System crash, data loss, security vulnerability, core feature completely broken
- **Major:** Major feature not working as expected, workflow blockage
- **Minor:** Cosmetic issues, minor functionality problems, spelling errors
- **Trivial:** Very minor UI issues that don't affect functionality

---

## **6. Exit Criteria**

Testing will be considered complete when:

1. **All test cases** have been executed at least once
2. **All Critical and Major severity defects** have been resolved and verified through re-testing
3. **A minimum of 95% of all test cases** have passed
4. **Core user journeys** are fully functional and stable:
   - Submission → Workflow → Publication → Discovery
   - OAI-PMH harvesting of all public content
   - User registration and authentication
5. **No showstopper bugs** remain open for the current release
6. **Test summary report** has been completed and signed off by project stakeholders

---

## **7. Test Execution Schedule**

| Phase | Activities | Timeline | Responsible |
| :--- | :--- | :--- | :--- |
| **Pre-Test** | Environment setup, test data preparation, test case review | Week 1 | DevOps, QA Lead |
| **Cycle 1** | Execution of all test modules, defect logging | Week 2 | QA Team |
| **Re-Test** | Verification of fixed defects, regression testing | Week 3 | QA Team |
| **Cycle 2** | Final verification, OAI-PMH validation, performance spot checks | Week 4 | QA Team |
| **Post-Test** | Reporting, sign-off, environment cleanup | Week 4 | QA Lead |

---

## **8. OAI-PMH Validation Notes**

For production repositories, it is highly recommended to validate your OAI-PMH feed using the official **Repository Explorer** tool:

- **Tool:** [OAI-PMH Repository Explorer](https://www.openarchives.org/Register/ValidateSite)
- **Process:** Enter your base OAI-PMH URL and run comprehensive protocol validation
- **Frequency:** Perform this validation after any major DSpace upgrade or configuration change

### **Common OAI-PMH Issues and Solutions:**

- **"noMetadataFormat" error:** Ensure the `metadataPrefix` you are requesting is listed in `ListMetadataFormats`
- **"cannotDisseminateFormat" error:** Check individual item metadata availability
- **"idDoesNotExist" error:** Verify the identifier format and item accessibility
- **Missing sets:** Ensure communities are properly configured and publicly visible

---

## **9. Conclusion**

This comprehensive functional test plan provides a foundation for ensuring the quality, reliability, and interoperability of the DSpace Institutional Repository. It should be treated as a living document and updated as:

- New DSpace features are added or existing functionalities are modified
- Institutional requirements evolve
- New integration points are developed
- Community best practices emerge

Regular execution of this test plan will ensure the repository remains robust, compliant with standards, and effectively serves its users and the broader scholarly community.

**Document Approval:**

**QA Lead:** _________________________ Date: ___________ 

**Project Manager:** _________________________ Date: ___________ 

**System Administrator:** _________________________ Date: ___________
