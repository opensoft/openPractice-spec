## ADDED Requirements

### Requirement: The demand module preserves exact staffing overlaps
The demand module SHALL derive staffing requirements from booked visit-type windows by location, role and qualification, as specified by AP-10. It SHALL compute concurrent requirements using the exact start and end of each window. Reporting buckets SHALL preserve peak simultaneous demand and total staff-minutes. Demand calculation SHALL operate from the visit book and visit-type requirements during a provider outage.

#### Scenario: Two procedures overlap within one reporting bucket
- **GIVEN** two procedures at the same site each require one procedure-qualified RN from 09:10 to 09:20
- **WHEN** openPractice calculates staffing requirements for the 09:00 to 09:30 reporting bucket
- **THEN** it reports two simultaneous qualified RNs for 09:10 to 09:20 and twenty RN-minutes in the bucket
