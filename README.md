# Test for Analytics

Design tests for Analytics functionality on a Battery Monitoring System.


## Analysis-functionality to be tested

This section lists the Analysis for which _tests_ must be written.

Battery Telemetrics are collected and stored on a server.
Data for a month is stored in a [csv file](https://en.wikipedia.org/wiki/Comma-separated_values).

Analysis must be done on this csv file to find the following:
- minimum
- maximum
- count of breaches (how many times did it cross the threshold in a month?)
- record trends (date & time when the reading was continuously increasing for 30 minutes)

A PDF report of the analysis must be stored every week.
Notification must be sent when a new report is available.

## Tasks

### List Dependencies

List the dependencies of the Analysis-functionality.

1. Access to the Server containing the telemetrics in a csv file 
1. Access to open & read the contents of csv file
1. External PDF Creator/Convertor utility
   - Proper functioning of PDF utility
   - Access of Analysis-functionality SW to utility
   - Access to server to store the PDF report
   - Server should not be full 
1. Notification Utility
   - Proper functioning of Notification utility like Email/Alerts 
   - Sending to the right/valid recipient 
   - Access to server to check if new Report is generated
   - Access to server for notification receiver to view the Report
 


### Mark the System Boundary

What is included in the software unit-test? What is not? Fill this table.

Assumptions:

Two external utilities are considered
1) PDF generator/converter
2) Notification utility - Email utility

These two utilities shall be called by functions generate_pdf , send_notification and return success/failure
These functions (generate_pdf , send_notification) shall be unit-tested.

In the below table, I am considering the external utilities alone.

| Item                      | Included?     | Reasoning / Assumption
|---------------------------|---------------|---
Battery Data-accuracy       | No            | We do not test the accuracy of data
Computation of maximum      | Yes           | This is part of the software being developed
Off-the-shelf PDF converter | No            | A mock shall be used in the place of External Email/other notifiers for unit-tests
Counting the breaches       | Yes           | This is part of the software being developed
Detecting trends            | Yes           | This is part of the software being developed
Notification utility        | No            | External Notifier utility functionality is not tested, a mock could be used for Unit Tests

### List the Test Cases

Write tests in the form of `<expected output or action>` from `<input>` / when `<event>`

Add to these tests:

1.Input Handling
  1. Write "Invalid input" to the PDF report content when the csv doesn't contain expected data / contains NaN values
  1. Write "Empty file" to the PDF Report content when the csv file is empty
  1. Write "Outdated Csv Data" to PDF report content when battery telemetrics are not updated/outdated
  1. Return "Server Inaccessible/E400" from server link/location when server containing csv/pdf report is not accessible/down
     
2.Write minimum and maximum to the PDF from a csv containing positive and negative readings
3.Breach Computation
 1. Return "Breach Exists" from csv data when the value is greater than threshold/ not in the normal range
 1. Increment Breach Count Value from csv data breach when breach is detected
 1. Return "Breach Does not Exist" from breach Count Value when there is no breach occurred

4.Trends 
  1. Write Trends Data (Values with date & time) to PDF when there is increasing battery telemetrics values for 30 mins
  1. Return "No Increasing Trend" when there are no trends observed
  1. Write "No Trends Occurred" to PDF Report contents when there are no increasing battery telemetrics values for 30 mins

5.PDF Report Creation
  1. Return "Successful Report Creation" from report contents when PDF Report is generated
  1. Return "Report Creation Failed" from report contents when PDF is not created/Utility issues
  1. Return "PDF Stored in Server" from PDF report when Report is stored every week after successful analysis
  1. Return "Server Space Full" from server location/address when storing report failed due to storage space issues

6.Notification
  1. Return "Notification Sent" when email/other notifier is successful
  1. Return "Notifier Error" when the email/other notifier is not successful
  1. Return "Link/PDF Attachment missing" when notification contents are missing
  1. Return "Invalid Email" when contents of notifier (Email) (Say wrong recipient/wrong subject) are incorrect
  1. Send alert/notification from pdf report everytime when there is new report created

### Recognize Fakes and Reality

Consider the tests for each functionality below.
In those tests, identify inputs and outputs.
Enter one part that's real and another part that's faked/mocked.

| Functionality            | Input        | Output                      | Faked/mocked part
|--------------------------|--------------|-----------------------------|---
Read input from server     | csv file     | Internal data-structure     | Fake the server store
Validate input             | Internal data-structure that contains csv data     | valid / invalid             | None - it's a pure function
Find minimum and maximum   | Valid Internal data-structure that contains csv data | Minimum/maximum values              | None- Pure Function (No external dependency)
Detect trend               | Valid Internal data-structure that contains csv data  | Trend Result (present/absent) , trend data              | None- Pure Function (No external dependency)
Write to PDF               | Min, Max, BreachCount, Trends with Date, Timestamp | PDF File             | Mock/Fake the PDF creation utility
Notify report availability | PDF File | Notification/Alert Mechanism  Result (NOTIFIED/ NOTIFIER_ERROR)               | Mock/Fake the notifier (Ex: Mock Email utility)
Report inaccessible server | Server Location/Link, CSV/PDF file Name| Accessible/Not Accessible              | Mock the Server store access which contains CSV & Generated PDF Report