# Flow Planning

This document contains the planning structure for the flow of actions to be implemented in the app.

## Main Flows

### New Service - Basic Flow
When a new service arrives and is scheduled for the next free time slot.
> Assumes the mechanic is free, accepts the service, does not need to wait for parts, and the client approved the budget.

```mermaid
sequenceDiagram
    actor Client
    actor Staff
    participant Server
    actor Mechanic

    Client->>Staff: Vehicle basic info and problem.
    Staff->>+Server: NewVehicleDTO
    Note over Staff, Server: Obs: Mechanic specified
    par Server to Staff
        Server -->>Staff: NewServiceAlertDTO
        Note over Server, Staff: Service Status:<br/>Pending Mechanic Acceptance
    and Server to Mechanic
        Server->>Mechanic: NewServiceAppointmentAlertDTO
    end
    Mechanic->>Server: NewServiceAcceptanceDTO
    Note over Mechanic, Server: Accepted
    Server -->>-Staff: ServiceStatusUpdateAlertDTO 
    Note over Server, Staff: Service Status: Budgeting
    par
        Staff ->> +Client: Budget
        Staff ->> Server: ServiceStatusUpdateDTO
        Note over Staff, Server: Service Status: Awaiting Budget Approval
    end
    Server -->> Mechanic: ServiceStatusUpdateAlertDTO
    Client ->> -Staff: Approval
    Staff ->> +Server: ServiceStatusUpdateDTO
    Note over Staff, Server: Service Status: Budget Approved
    Server -->> Mechanic: ServiceStatusUpdateAlertDTO
    Note over Server, Mechanic: Service Status: Budget Approved
    alt Staff signals
        Staff ->> Server: ServiceStatusUpdateDTO
        Note over Staff, Server: Service Status: Working on it
        Server -->> Mechanic: ServiceStatusUpdateAlertDTO
        Note over Server, Mechanic: Service Status: Working on it
    else Mechanic signals
        Mechanic ->> Server: ServiceStatusUpdateDTO
        Note over Mechanic, Server: Service Status: Working on it
        Server -->> Staff: ServiceStatusUpdateAlertDTO
        Note over Server, Staff: Service Status: Working on it
    end
    alt Staff signals
        Staff ->> Server: ServiceStatusUpdateDTO
        Note over Staff, Server: Service Status: Done
        Server -->> Mechanic: ServiceStatusUpdateAlertDTO
        Note over Server, Mechanic: Service Status: Done
    else Mechanic signals
        Mechanic ->> Server: ServiceStatusUpdateDTO
        Note over Mechanic, Server: Service Status: Done
        Server -->> -Staff: ServiceStatusUpdateAlertDTO
        Note over Server, Staff: Service Status: Done
    end
    Staff ->> Client: Inform the service is done 
```

### New Service - Expanded Flow
When a new service arrives and is scheduled for the next free time slot.
> Assumes there is no mechanic free the first moment, when one is appointed he accepts the service, some parts are missing, and the client accepts the budget.

```mermaid
sequenceDiagram
    actor Client
    actor Staff
    participant Server
    actor Mechanic

    Client->>Staff: Vehicle basic info and problem.
    Staff->>+Server: NewVehicleDTO
    Server -->>Staff: NewServiceAlertDTO
    Note over Server, Staff: Service Status:<br/>Waiting Mechanic Appointment 

    Staff->>+Server: ServiceUpdateDTO
    Note over Staff, Server: Appointing Mechanic
    par
        Server -->>Staff: ServiceUpdateAlertDTO
        Note over Server, Staff: Service Status:<br/>Pending Mechanic Acceptance
        Server->>Mechanic: NewServiceAppointmentAlertDTO
    end
    
    Mechanic->>Server: NewServiceAcceptanceDTO
    Note over Mechanic, Server: Accepted
    Server -->>-Staff: ServiceStatusUpdateAlertDTO 
    Note over Server, Staff: Service Status: Budgeting

    par
        Staff ->> +Client: Budget
        Staff ->> Server: ServiceStatusUpdateDTO
        Note over Staff, Server: Service Status: Awaiting Budget Approval
    end
    Server -->> Mechanic: ServiceStatusUpdateAlertDTO

    Client ->> -Staff: Approval
    Staff ->> +Server: ServiceStatusUpdateDTO
    Note over Staff, Server: Service Status: Budget Approved
    Server -->> Mechanic: ServiceStatusUpdateAlertDTO
    Note over Server, Mechanic: Service Status: Budget Approved

    loop
        alt Staff signals
            Staff ->> Server: ServiceStatusUpdateDTO
            Note over Staff, Server: Service Status: Working on it
            Server -->> Mechanic: ServiceStatusUpdateAlertDTO
            Note over Server, Mechanic: Service Status: Working on it
        else Mechanic signals
            Mechanic ->> Server: ServiceStatusUpdateDTO
            Note over Mechanic, Server: Service Status: Working on it
            Server -->> Staff: ServiceStatusUpdateAlertDTO
            Note over Server, Staff: Service Status: Working on it
        end

        opt
            alt Staff signals
                Staff ->> Server: ServiceStatusUpdateDTO
                Note over Staff, Server: Service Status: Waiting for parts
                Server -->> Mechanic: ServiceStatusUpdateAlertDTO
                Note over Server, Mechanic: Service Status: Waiting for parts
            else Mechanic signals
                Mechanic ->> Server: ServiceStatusUpdateDTO
                Note over Mechanic, Server: Service Status: Waiting for parts
                Server -->> Staff: ServiceStatusUpdateAlertDTO
                Note over Server, Staff: Service Status: Waiting for parts
            end
        end
    end


    alt Staff signals
        Staff ->> Server: ServiceStatusUpdateDTO
        Note over Staff, Server: Service Status: Done
        Server -->> Mechanic: ServiceStatusUpdateAlertDTO
        Note over Server, Mechanic: Service Status: Done
    else Mechanic signals
        Mechanic ->> Server: ServiceStatusUpdateDTO
        Note over Mechanic, Server: Service Status: Done
        Server -->> -Staff: ServiceStatusUpdateAlertDTO
        Note over Server, Staff: Service Status: Done
    end
    
    Staff ->> Client: Inform the service is done 
```

## Other Flows

### Mechanic does not accept a service
When the mechanic does not accept the service, it awaits a new appointment or, rarely, to be canceled.

```mermaid
sequenceDiagram
    actor Staff
    participant Server
    actor Mechanic1 as Mechanic 1
    actor Mechanic2 as Mechanic 2
    
    Note over Staff, Mechanic2: ...
    Staff->>+Server: NewVehicleDTO
    Server -->>Staff: NewServiceAlertDTO
    Note over Server, Staff: Service Status:<br/>Waiting Mechanic Appointment 

    Staff->>+Server: ServiceUpdateDTO
    Note over Staff, Server: Appointing Mechanic 1
    par
        Server -->>Staff: ServiceUpdateAlertDTO
        Note over Server, Staff: Service Status:<br/>Pending Mechanic Acceptance
        Server->>Mechanic1: NewServiceAppointmentAlertDTO
    end
    
    Mechanic1->>Server: NewServiceAcceptanceDTO
    Note over Mechanic1, Server: Rejected
    Server -->>-Staff: ServiceUpdateAlertDTO
    Note over Server, Staff: Service Status:<br/>Waiting Mechanic Appointment

    alt new Mechanic appointment
        Staff->>+Server: ServiceUpdateDTO
        Note over Staff, Server: Appointing Mechanic 2
        par
            Server -->>Staff: ServiceUpdateAlertDTO
            Note over Server, Staff: Service Status:<br/>Pending Mechanic Acceptance
            Server->>Mechanic2: NewServiceAppointmentAlertDTO
        end
        
        Mechanic2->>Server: NewServiceAcceptanceDTO
        Note over Mechanic2, Server: Accepted
        Server -->>-Staff: ServiceUpdateAlertDTO
        Note over Server, Staff: Service Status:<br/>Budgeting
        Note over Staff, Mechanic2: ...
    else cancellation
        Staff ->> Server: ServiceUpdateDTO
        Note over Staff, Server: Obs + Service Status: Canceled
    end 
```

### Mechanic doesn't have access to the system
When the mechanic doesn't have access to the system, by any reason (e.g. phone battery dies), or by superior orders the staff can enforce appointment of the mechanic, avoding the wait for the service acceptance.
> The system will not ensure the vehicle plate and Km be provided to start a service, to avoid constraints when the Staff is very busy, but the interface must present a visual representation that this information is missing and must be provided.

```mermaid
sequenceDiagram
    actor Staff
    participant Server
    actor Mechanic
    Staff->>+Server: NewVehicleDTO
    Server -->>Staff: NewServiceAlertDTO
    Note over Server, Staff: Service Status:<br/>Waiting Mechanic Appointment 
    Staff->>+Server: ServiceUpdateDTO
    Note over Staff, Server: Appointing Mechanic 1<br/>Enforce: True
    par
        Server-->>Staff: ServiceUpdateAlertDTO
        Note over Server,Staff: Service Status: Budgeting
        Server-->>Mechanic: NewServiceAlertDTO
        Note over Server, Mechanic: Service Status: Budgeting
    end
    Note over Staff, Mechanic: ...
```

### Updating service data
When there is a need to update wrong data or add new obs to a existing service.
```mermaid
sequenceDiagram
    actor Staff
    participant Server
    actor Mechanic

    Note over Staff, Mechanic: ...
    Staff->>Server: ServiceUpdateDTO
    par
        Server->>Staff: ServiceUpdateAlertDTO
        Server->>Mechanic: ServiceUpdateAlertDTO
    end
    Note over Staff, Mechanic: ...
```

### Updating app configuration
When the staff change a app setting to fit the system to the new need, e.g. always enforce the mechanic, when they will not use their phone to work.

```mermaid
sequenceDiagram
    actor Staff
    participant Server

    Note over Staff, Server: ...
    Staff-->>Server: UpdateConfigDTO
    Server-->>Staff: NewConfigAlertDTO
    Note over Staff, Server: ...
```