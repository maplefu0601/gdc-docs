# Overview

Accessing the GDC Data Submission Portal requires eRA Commons credentials with appropriate dbGap authorization. See [Obtaining Access to Submit Data]( https://gdc.nci.nih.gov/submit-data/obtaining-access-submit-data) for more details.

The [Authentication](../gdc/authentication-authorization999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999.md#authentication) section of the documentation provides more details about the process itself.

# Authorization

Permissions are attached to each user account. Access can be granted to one or more projects. Those permissions will apply throughout the GDC Data Submission Portal and will define how data is being displayed.

Based on the number of projects granted to the user, the GDC Data Submission Portal will adjust its layout and provides different functionalities.

# GDC Data Submission Portal Authentication

When accessing the GDC Data Submission Portal for the first time, users will be presented with splash page providing more details about the system and a login button.

[![GDC Data Submission Portal Login Splash Page](images/GDC_Submission_Login_Splash_page.png)](images/GDC_Submission_Login_Splash_page.png "Click on the image to see the full size version.")

Links to GDC Website are also available to provide the user with more details about dbGaP as well as more details about the submission process and tools.

By Clicking on "Login", a pop-up will open and users will be invited to authenticate through eRA Commons. If successful, the user will be redirected to the GDC Data Submission Portal Dashboard.

[![GDC Data Submission Portal eRA Commons login](images/99GDC_Submission_Login_eRA_Commons.png)](images/GDC_Submission_Login_eRA_Commons.png "Click on the image to see the full size version.")

The login page will show up again if users logout or upon expiration of their authentication token after 30 minutes of inactivity.

## Failed authentication

If the user fails to authenticate through eRA Commons and close the authentication pop-up, a "401 - UNAUTHORIZED" message will show up on the splash screen.

[![GDC Failed Authentication](images/GDC_Submission_Login_Splash_page-Failed_Login.png)](images/GDC_Submission_Login_Splash_page-Failed_Login.png "Click on the image to see the full size version.")

# Obtaining a Token

A Token is required to upload data to GDC, as well as to download controlled-access files, using the GDC Data Transfer Tool or the GDC Application Programming Interface.

The GDC Data Transfer Tool is optimized for large transfers with multi-part upload and integrity checking, making it the most efficient tool for molecular data submission.

Tokens are strings of characters provided with every GDC call requiring authentication. A file containing a token can be obtained through GDC Data Submission Portal once your NIH eRA Commons account has been established. Your GDC account will be automatically established upon first login and you will see a drop down menu next to your user name.

[![GDC Token Dropdown](images/GDC_Submission_Token_Download.png)](images/GDC_Submission_Token_Download.png "Click on the image to see the full size version.")

Users can download and save their token from here.

# Logout

Users can log-out from GDC Data Submission Portal by clicking on their profile icon on the top right corner (See screenshot above).

[![GDC Logout](images/GDC_Submission_Logout.png)](images/GDC_Submission_Logout.png "Click on the image to see the full size version.")

Logging out happens on eRA Commons website.
