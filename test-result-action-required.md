# **Failed Test Cases – Action Required**

### 1. Missing metadata field for ETD-MS List of Records (View details in Edit mode, or refer to [here](/dspace-test-2.png) )
<description>...</description>
***<publisher>Lakehead University</publisher>***
<date>...</date>

<language>...</language>
***<degree>
      <name>Master of ...</name>
      <level>Master</level>
      <discipline>...</discipline>
      <grantor>Lakehead University</grantor>
</degree>
  <identifier>TC-OPAL-...</identifier>
  <identifier>https://knowledgecommons.lakeheadu.ca/bitstream/2453/.../1/....pdf</identifier>
  <format>application/pdf</format>***

### 2.  USER-01 - Password Reset: Reviewed on 25.12.12, can be ignored due to same credential with Lakehead Email.
When entering a registered email on the “Forgot Password” page, the system displays an error pop-up:
“Error when trying to reset password. An error occurred when attempting to reset the password for the account associated with the following email address: xxx@lakeheadu.ca.”<br>
However, when entering a non-registered email, the system incorrectly displays: “An email to reset password has been sent to [email address].” (No email is actually received)

### 2. OAI-01 - Identify Verb: Reviewed on 25.12.12
Test passed, but the E-Mail Contact field shows dspace-help@myu.edu instead of our institutional email address.

# Not Tested (No Access or Not Applicable)

- SUB-01: User Registration
- WF-01 / WF-02: Accept / Reject Submission (Reviewer)
- SYS-01: LDAP / SSO Login
- SYS-03: ORCID Integration
- SYS-04: Statistics Viewing

# Minor Issues

1. The previous version had Advanced Search Filters, but they are not visible in the new interface. Removed or missing?

2. On both the testing and previous sites, some items display en_US multiple times near different metadata fields.<br>
Example: **/items/13dea760-a9af-4f67-a713-4764f93f509d/full** vs **/items/3f970794-fbbb-4bbc-907c-101e1c68ebf0/full**

3. When searching by keyword, the previous site includes items in other languages if their translated metadata matches the keyword.The testing site does not include these results.<br>
Example: Searching "international students - Lakehead"<br>
Previous site: 11 results<br>
Testing site: 9 results<br>
The 2 missing items are in Spanish but contain translated metadata that matches the word “student.” see details [here](/dspace-test-1.png))<br>
Is this expected behavior in the new version?<br>
