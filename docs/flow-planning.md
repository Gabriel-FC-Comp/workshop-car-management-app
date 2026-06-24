# DTO Planning

This document contains the planning structure for the flow of actions to be implemented in the app.

## New Service - Basic Flow
When a new service arrives and is scheduled for the next free time slot.
> Assumes the mechanic is free, accepts the service, and does not need to wait for parts.

```mermaid
sequenceDiagram
    actor Client
    actor Staff
    participant Server
    actor Mechanic

    Client->>Staff: Vehicle basic info and problem.
    Staff-->>Server: NewVehicleDTO
    Server-->>Mechanic: NewServiceAppointmentAlertDTO
    Mechanic-->>Server: NewServiceAcceptanceDTO
    Server -->>Staff: ServiceUpdateAlertDTO 
    Note over Server, Staff: Service Status: Done 
```
