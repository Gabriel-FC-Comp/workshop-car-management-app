# Functional and No-Functional Requisites

This document compiles the **functional** and **no-functional** requisites of the system and bussinness logic.

### To be Categorized
- To register a new service, a staff user must provided the client name and phone, and the vehicle brand and model.
- A service can be scheduled.
- A service without a shceduled date must be scheduled to the day it's registred.
- A scheduled service must change it's status from *"Scheduled"* to *"Waiting Mechanic Appointment"* whe the scheduled day arrives.
- The staff user interface must exhibit a visual alert when the vehicle plate and/or km is missing.
- A mechanic user must provide the vehicle plate and km to accept a service.
- The staff user can enforce the appointment of a service to a mechanic.
- A mechanic user can reject a service appointment.
- A mechanic user can signalize when it is wating for parts or the service is done.
- A staff user can change almost all services data, except for audit data.
- Audit data cannot be edited.
- Only staff users can register new services.
- Users can be either staff or mechanic.
- A service status must be one of the bellow:
    - *Scheduled*;
    - *Waiting Mechanic Appointment*;
    - *Pending Mechanic Acceptance*;
    - *Budgeting*;
    - *Awaiting Budget Approval*; 
    - *Budget Approved*;
    - *Working On It*;
    - *Waiting for Parts*;
    - *Done*, or;
    - *Canceled*;
- The standart service status cannot be modified.
- New service status can be registred.
- A non standart service status cannot be deleted if there is at least one service register with it.
- The access and server alerts to all mechanic users can be blocked by a staff user changing the config to always enforce service appointments.
- The admin user must be a staff user.
- The admin user cannot be deleted or changed to a mechanic user, only have it's info updated.

## Functional Requisites

## No-Functional Requisites