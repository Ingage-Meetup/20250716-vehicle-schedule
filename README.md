# C3 Vehicle Scheduling Kata

## The Challenge
Today we will be working to enable Customer Service Representatives (CSR) in the automative repair business to manage their repair appointments. The CSR will talk with the customers to determine an approximate number of hours based on their maintenance needs, and then use this new-fangled softwares to give them a list of time blocks for possible appointments. Once they have selected an appointment you can add it to the schedule so others CSR's don't double book bays / appointments.

Initially we will trial the scheduling software with our smallest shop that only has 1 bay. If we are successful we will want to allow it to expand to locations with multiple bays.

While we have shops located across the United States all of our shops currently work Monday to Friday from 8 AM to 5 PM local time, and appointments should adhere to this schedule. The shop for the trial is Located in the Greater Cincinnati Area which is in the Eastern Time Zone.

For example if a site has a single bay, and has one appointments from 8 am with 1 hour duration and another from 11:30 with 2 hour duration the resulting available blocks would be from 9:00 AM to 11:30 (2.5 hours) or from 1:30 pm to 5 pm (3.5 hours).

Appointments should only be scheduled on half hour boundaries, and duration of appointments should be in whole hours. 

## Proposed Structure
AppointmentService - Responsible for collaborating with the repository and calculator classes to provide a list of possible AppointmentBlocks and to schedule a new appointment.

AppointmentBlockCalculator - Responsible for looking at a list of appointments and finding the time blocks where a bay is available for an appointment. 

AppointmentRepository - Responsible for calling loading and storing appointments through an external REST based API.

## The Vehicle API
URL: [https://c3vehicleapi.azurewebsites.net/](https://c3vehicleapi.azurewebsites.net/)

Swagger: [https://c3vehicleapi.azurewebsites.net/swagger/index.html](https://c3vehicleapi.azurewebsites.net/swagger/index.html)

### Registration Endpoint
`POST /Registrations` with a payload of `{ "applicationName": "Your App name" }` to generate an `applicationKey`. Make sure to capture this value as this is the only time you have access to it.

### Notes on Authentication
The remaining endpoints require authorization which is accomplished by passing an `x-api-key` header to the endpoints. Unfortunately these calls don't currently work through swagger since the header isn't able to be set. I would suggest either looking at your IDE's support for `.http` files, PostMan, or even curl.

For `.http` support in VS Code install the `REST Client` extension

### Sample data
Registering an application also generates some sample data for your account.
* Sample sites were generated. The primary site has 1 bay, and the secondsary has 2 bays.
* Sample appointments were generated for the week of Monday 2025-07-21 through Friday 2025-07-25 for the primary site.

## Sites Endpoint
`GET /sites` - Get a list of ID's for sites that you have access to.

## Appointment Endpoints
`GET /sites/{siteId}/Appointments` - Returns a list of all scheduled appointments. This would be called from your AppointmentRepository
`POST /site/{siteId}/Appointment` - Takes a JSON payload with details of an appointment to schedule. 

Note: This isn't a very robust implementation yet. startTime should be in UTC, hoursDuration should be a positive integer, and Bay is 1 based and should match what's available at the site.

```
{
  "startTime": "2025-07-16T12:00:00Z",
  "hourDuration": 1,
  "customerName": "Ken Baum",
  "customerPhone": "555-5555",
  "bay": 1
}
```

Questions:
1. What is the first available time block we can get in a truck for an oil change that takes 1 hour.
1. What are the first five available time blocks we can get in a truck for an oil change that takes 1 hour.
1. How many time blocks are available for a truck needing engine maintenance that will take 3 hours to complete during the week of 2025-07-21?