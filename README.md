# Healthcare-Data-Analytics-Patient-Visit-Satisfaction-Dashboard-
# Problem Statement

City General Hospital is one of the leading healthcare providers. Over the years, they have been facing challenges in managing emergency room operations effectively. Increasing patient load, long wait times, and uneven resource allocation have negatively impacted patient satisfaction.

To address this, the hospital management decided to leverage Business & Data Intelligence. However, they lack an internal analytics team, so they hired a data intelligence provider to analyze emergency room visit data and provide actionable insights to improve operations, resource allocation, and patient experience.

# Dataset

 **1. Hospital ER.csv**

# Steps:

**1. Load data into Power BI:**
Open Power BI - Get Data - Link Folder which contains csv files - convert binary to tables

**2. Data Transformation:**
1. Generate a new table called date for extracting Year, Month, WeekType, Weekday, MonthNum for time-series analysis and also created AM/PM flag to analyze visits by shift.
- Dax formula:
Date = 
ADDCOLUMNS(
    CALENDARAUTO(), Group 
    "Year", YEAR([Date]),
    "Month", FORMAT([Date],"mmm"),
    "WeekType", IF(WEEKDAY([Date])=1,"Weekend",IF(WEEKDAY([Date])=7,"Weekend","Weekday")),
    "Weekday",FORMAT([Date],"ddd"),
    "MonthNum",MONTH([Date])
)

2. Patient Demographic Transformations: Age Group Buckets(0–2 = Infancy, 3–8 = Early Childhood, 9–12 = Middle Childhood, 13–19 = Teenager, 20+ = Adult)
Age Group = 
 Var _patientAge = 'Patient Dataset'[patient_age]
 RETURN
 IF(
     _patientAge<=2, "Infancy",
     IF (
         _patientAge<=6, "Early Childhood",
          IF(
              _patientAge<=12, "Middle Childhood",
             IF(
                 _patientAge<=18, "Teenager",
             "Adult"
             )
          )
     )
 )

**3. Manage Relationship:**
Create a schema relationship between tables
<img width="1096" height="625" alt="image" src="https://github.com/user-attachments/assets/25a78b3b-a1db-4f7c-aae4-e2fe2d2ff3e9" />

**4. Create Measures:**
1. **% Administrative Schedule** = DIVIDE(COUNTROWS(FILTER('Patient Dataset', 'Patient Dataset'[patient_admin_flag]=TRUE())), [Total Patients])
2. **% Female Visit** = 
DIVIDE(
    CALCULATE(
        [Total Patients],
    'Patient Dataset'[patient_gender]="F"
    ),
    [Total Patients]
)
3. **% Male Visit** = 
DIVIDE(
    CALCULATE(
        [Total Patients],
    'Patient Dataset'[patient_gender]="M"
    ),
    [Total Patients]
)
4. **% No Ratings** = VAR _NoRatings = CALCULATE([Total Patients], 'Patient Dataset'[patient_sat_score]=BLANK()) RETURN(DIVIDE(_NoRatings, [Total Patients]))
5. **% None - Administrative Schedule** = DIVIDE(COUNTROWS(FILTER('Patient Dataset', 'Patient Dataset'[patient_admin_flag]=FALSE())), [Total Patients])
6. **% Refrred Patients** = 
VAR _FilterPatients = 
CALCULATE(
    [Total Patients],
    'Patient Dataset'[department_referral]<> "none")
    RETURN
    DIVIDE(_FilterPatients,
    [Total Patients])
7. **% Un Refrred Patients** = 
VAR _FilterPatients = 
CALCULATE(
    [Total Patients],
    'Patient Dataset'[department_referral]= "none")
    RETURN
    DIVIDE(_FilterPatients,
    [Total Patients])
8. **% Unknown Visit** = 
DIVIDE(
    CALCULATE(
        [Total Patients],
    'Patient Dataset'[patient_gender]="NC"
    ),
    [Total Patients]
)
9. **Average Satisfaction Score** = AVERAGE('Patient Dataset'[patient_sat_score])
10. **Average wait time** = AVERAGE('Patient Dataset'[patient_waittime])
11. **CF Max Point (Month)** = 
    VAR _PatientTable = 
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]), 
            "@TotalPatients",[Total Patients]
            ),
            ALLSELECTED()
            ) 
    VAR _MinValu = MINX(_PatientTable, [@TotalPatients])
    VAR _MaxValu = MAXX(_PatientTable, [@TotalPatients])
    VAR _TotalPatients = [Total Patients]
    RETURN
    SWITCH(
        TRUE(),
        _TotalPatients = _MinValu, 0 ,
        _TotalPatients =  _MaxValu,1)
12. **CF Max Point (Year)** = 
    VAR _PatientTable = 
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Year]), 
            "@TotalPatients",[Total Patients]
            ),
            ALLSELECTED()
            ) 
    VAR _MinValu = MINX(_PatientTable, [@TotalPatients])
    VAR _MaxValu = MAXX(_PatientTable, [@TotalPatients])
    VAR _TotalPatients = [Total Patients]
    RETURN
    SWITCH(
        TRUE(),
        _TotalPatients = _MinValu, 0 ,
        _TotalPatients =  _MaxValu,1)
13. **HitMap Caption** = 
VAR _SelectedMeasure =
SELECTEDVALUE(Parameter[Parameter Order])
RETURN
IF(_SelectedMeasure = 0, 
"The darkest GREEN on the SCALE denotes LOW Waiting TIME ON THE Age-Group",
"Patients are most SATISFIED when the SCALE shows the darkest GREEN on the Age-Group")
14. **Total Patients** = COUNTROWS('Patient Dataset')
15. **Values Max Point (Month)** = 
    VAR _PatientTable = 
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]), 
            "@TotalPatients",[Total Patients]
            ),
            ALLSELECTED()
            ) 
    VAR _MinValu = MINX(_PatientTable, [@TotalPatients])
    VAR _MaxValu = MAXX(_PatientTable, [@TotalPatients])
    VAR _TotalPatients = [Total Patients]
    RETURN
    SWITCH(
        TRUE(),
        _TotalPatients = _MinValu, [Total Patients] ,
        _TotalPatients =  _MaxValu,[Total Patients])
16. **Values Max Point (YEAR)** = 
    VAR _PatientTable = 
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Year]), 
            "@TotalPatients",[Total Patients]
            ),
            ALLSELECTED()
            ) 
    VAR _MinValu = MINX(_PatientTable, [@TotalPatients])
    VAR _MaxValu = MAXX(_PatientTable, [@TotalPatients])
    VAR _TotalPatients = [Total Patients]
    RETURN
    SWITCH(
        TRUE(),
        _TotalPatients = _MinValu, [Total Patients] ,
        _TotalPatients =  _MaxValu,[Total Patients])



