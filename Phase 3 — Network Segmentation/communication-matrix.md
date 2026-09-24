# Mapping Segment Communication Requirements

The VLAN structure defines the network boundaries. The next step was to identify which communication paths are required by the organization's operations and which should not require direct communication.

These requirements are based on the resource-access decisions established during Phase 1.

## Segments That Should Communicate

### IT → Lab

IT requires access to the Blood Analyzer, DNA Sequencer, and Lab Tech PC for technical administration and support.

### Lab → Patient Database

Lab technicians need to transfer laboratory results from the Blood Analyzer and DNA Sequencer to the Patient Database.

### Doctors → Patient Database

Doctors and medical reviewers require access to appropriate patient records.

### Front Desk → Patient Database

Front desk staff require access to the Patient Database to enter new patient information.

### Billing → Patient Database

Billing staff require view-only access to relevant patient information.

### IT → Patient Database

IT requires technical administrative access to the Patient Database.

### External Hospitals → DMZ

External hospitals require access to the Web Portal hosted in the DMZ.

## Segments That Should Not Require Direct Communication

Based on the Phase 1 network requirements, the following direct communication paths are not required:

- **Doctors → Lab**
    
- **Front Desk → Lab**
    
- **External Hospitals → Internal departments**
    
- **DMZ/Web Portal → Internal departments**
    
- **Web Portal → Patient Database**
    

The Web Portal is intended to act as the externally accessible application layer, while the Patient Database remains within the internal network.

The intended data flow is:

```text
Patient Database
       ↓
   Web Portal
       ↓
External Hospitals
```

Direct communication from the Web Portal back to the Patient Database is therefore not part of the intended communication model:

```text
Web Portal ──X──> Patient Database
```

These communication requirements establish the baseline for the security controls implemented in Phase 4. The purpose is not simply to separate the network into VLANs, but to define which communication paths are actually necessary so that unnecessary traffic can later be restricted.

---
