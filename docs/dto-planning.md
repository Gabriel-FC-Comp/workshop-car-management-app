# DTO Plannig

This document contains the planning structure for the DTOs to be used on the app.

---

### DTO Header
All DTOs sent by a user must contain this header to ensure accounting.
- Header:
    - user_id: the ID of the user who sent the DTO;
    - created_at: the timestamp of the moment when the request was made.

### NewVehicleDTO
**Staff -> Server**:
When a new vehicle is added to the service list.

- **vehicle_brand**: the brand of the new vehicle;
- **vehicle_model**: the model of the new vehicle;
- **mechanic_id**: the ID of the mechanic who will be assigned to deal with the new vehicle;
- **client_name**: the name of the client who brought the vehicle;
- **client_phone**: a phone number to contact the client;
- **service_datetime** *(optional)*: the datetime to schedule the service;
- **observations** *(optional)*: a free field to add observations about the problem reported by the client.

### NewServiceCreationAlertDTO
**Server -> Staff**: 
When the server processes the request to open a new service register, it sends to the staff the data of the new created pending service.

- **service_id**: the ID of the new service for further communication;
- **service_status**: the current status of the service:
    - *Waiting Appointment*;
    - *Scheduled*, or;
    - *Pending Mechanic Approval*.
- **vehicle_brand**: the brand of the new vehicle;
- **vehicle_model**: the model of the new vehicle;
- **mechanic_id**: the id of the mechanic who will be asssigned to deal with the new vehicle;
- **client_name**: the name of the client who broght the vehicle;
- **client_phone**: a phone number to contact the client;
- **service_datetime**: the datetime to schedule the service;
- **observations**: a free camp to add observations about the problem related by the client.

### NewServiceAppointmentAlertDTO
**Server -> Mechanic**: 
An alert emited to the mechanic when a new service is appointed to him.

- **service_id**: the ID of the new service to further communication;
- **vehicle_brand**: the brand of the vehicle of the new service;
- **vehicle_model**: the model of the vehicle of the new service.

### NewServiceAcceptanceDTO
**Mechanic -> Server**:
When the mechanic accepts the new service, he must also add the vehicle basic data to help OS mannaging and service history.

- **service_id**: the ID of the new service to further communication;
- **vehicle_plate**: the license plate of the vehicle;
- **vehicle_km**: the current km the car presents.

### ServiceUpdateAlertDTO
**Server -> Staff**: 
When a service info is updated in the server, the server emmits an alert to the users, depending of the meaning of the alert (e.g. Update staff info of today or later services, update mechanic info on current services).

- **service_id**: the ID of the service updated;
- **service_status** *(optional)*: the new status of the service:
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
- **vehicle_brand** *(optional)*: the brand of the new vehicle;
- **vehicle_model** *(optional)*: the model of the new vehicle;
- **vehicle_plate** *(optional)*: the license plate of the vehicle;
- **vehicle_km** *(optional)*: the current km the car presents;
- **mechanic_id** *(optional)*: the id of the mechanic who will be asssigned to deal with the new vehicle;
- **client_name** *(optional)*: the name of the client who broght the vehicle;
- **client_phone** *(optional)*: a phone number to contact the client;
- **service_datetime** *(optional)*: the datetime to schedule the service;
- **observations** *(optional)*: a free camp to add observations about the problem related by the client.