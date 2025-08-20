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
<img width="1096" height="625" alt="image" src="https://github.com/user-attachments/assets/25a78b3b-a1db-4f7c-aae4-e2fe2d2ff3e9" />



