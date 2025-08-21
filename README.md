# Workforce Analytics

---
## Project Summary
This project is a comprehensive Workforce Analytics built using Power BI. It provides a detailed look into key HR metrics, including headcount, turnover, diversity, and age demographics, to help the organization make data-driven decisions. The dashboard aims to transform raw employee data into actionable insights, helping to identify retention risks, understand workforce composition, and support strategic planning.

---

## Table of Contents

- [Project Summary](#project-summary)
- [Business Objective](#business-objective)
- [Tools & Skills Applied](#tools--skills-applied)
- [Data Source](#data-source)
- [Data Preparation & Modeling](#data-preparation--modeling)
- [DAX Calculated Measures and Columns](#dax-calculated-measures-and-columns)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [What Could Be Improved](#what-could-be-improved)
- [Final Reflection](#final-reflection)

---
## Business Objective
The primary objective of this project is to develop a dynamic and interactive dashboard that addresses critical business questions related to human resources. By visualizing key metrics, the dashboard helps to:

- Identify Turnover Drivers: Pinpoint departments or groups with high turnover rates to address underlying issues.

- Support Strategic Workforce Planning: Understand the current headcount, employee tenure, and age distribution to plan for future recruitment and succession needs.

- Enhance Diversity & Inclusion Initiatives: Analyze the demographic breakdown of the workforce by gender, race, and age to ensure a diverse and inclusive environment.

- Improve Employee Retention: Provide insights that can inform targeted strategies to reduce employee turnover and increase average tenure.
  
---

## Tools & Skills Applied
Tools:

- Power BI: Data modeling, visualization, and dashboard creation.

- DAX (Data Analysis Expressions): Used for creating calculated columns and measures for advanced analysis (e.g., employee age, turnover rate).

- Power Query (M Language): Utilized for data cleaning, transformation, and loading.

Skills:

- Data Cleaning & Transformation: Handling missing values, correcting data types, and preparing the dataset for analysis.

- Data Modeling: Creating relationships between tables to enable cross-functional analysis.

- HR Analytics: Applying core HR concepts such as headcount, turnover, etc. to a business context.

- Dashboard Design: Creating intuitive and visually appealing reports that effectively communicate insights.

---

## Data Source
- The dataset used for this project was obtained from Kaggle. It is a sample employee dataset containing various attributes for each employee.
- **Time Range**: 2018 - 2023

---

## Data Preparation & Modeling
The following steps were performed to prepare and model the data for analysis in Power BI:

Loading Data: The raw data was imported into Power BI's Power Query Editor.

Data Cleaning:
- Null values in the termination type column were handled by marking them with placeholders.
- Data types were corrected for all columns (e.g., converting 'Date of Birth' to a proper date format).
  
Data Transformation:
- A new measure for Average Tenure was created to measure how long employees have been with the company.
- Calculated columns for Employee Age and Generational Category were created from the Date of Birth column to enable demographic analysis.
- An Age Group column was created to categorize employees into specific age ranges for easier visualization.

Data Modeling:
- A simple star schema was established, with the main employee table serving as the fact table.
- Relationships were created to allow for filtering and analysis across different dimensions.

---

## DAX Calculated Measures and Columns

```dax

---Employee Age Calculated Column---
EmployeeAge = DATEDIFF('DIM_Employee'[DOB], TODAY(), YEAR)

---Turnover Rate---
Turnover Rate = DIVIDE([Total Terminations], [Total Employees])

---Average Tenure---
Average Tenure = AVERAGEX(
    FACT_EMPLOYMENT,
    VAR EndDate = IF(
        FACT_EMPLOYMENT[ExitDateKey] = 47848, 
        TODAY(),
        RELATED(DIM_DATE[Date])
    )
    RETURN 
    DATEDIFF(
        RELATED(DIM_DATE[Date]), 
        EndDate, 
        DAY
    )
) / 365.25

---Age Group Calculated Column---
Age Group = 
SWITCH(TRUE(),
    'DIM_EMPLOYEE'[Employee Age] <= 25, "18-25",
    'DIM_EMPLOYEE'[Employee Age] <= 35, "26-35",
    'DIM_EMPLOYEE'[Employee Age] <= 45, "36-45",
    'DIM_EMPLOYEE'[Employee Age] <= 55, "46-55",
    'DIM_EMPLOYEE'[Employee Age] <= 65, "56-65",
    'DIM_EMPLOYEE'[Employee Age] <= 75, "66-75", 'DIM_EMPLOYEE'[Employee Age] <= 85, "76-85", "85+"
)

---Total Headcount---
Total Employees = DISTINCTCOUNT(FACT_EMPLOYMENT[EmpID])

---Generational Category Calculated Column---
Percentage_of_Generational Category = DIVIDE(COUNT('DIM_EMPLOYEE'[EmpID]), CALCULATE(COUNT('DIM_EMPLOYEE'[EmpID]), ALL('DIM_EMPLOYEE'[Generational Category])))

---Total Terminations---

Total Terminations = CALCULATE(
    DISTINCTCOUNT(FACT_EMPLOYMENT[EmpID]),
    FACT_EMPLOYMENT[ExitDateKey] <> 47848
)

```

## Key Insights

Based on the dashboard's reports, the following key insights were uncovered:
<img width="1051" height="583" alt="Overview" src="https://github.com/user-attachments/assets/99d90bce-dfa1-40f7-b808-50a1cc29ca0b" />

### High Turnover: 
- The company's turnover rate is a staggering 51%, a major issue requiring immediate attention. Also, about 53% of the employees are contract, which might explain the high turnover rate.
  
### Diversity Balance: 
- The gender, race and marital status distribution is fairly balanced although when sliced through by divisions, then women lead in the distribution. This needs to be watched.

<img width="1047" height="587" alt="Diversity Analytics" src="https://github.com/user-attachments/assets/33db7304-3ce6-4a93-852e-6a1a842d9630" />

### Sales Department is a Focus Area: 
- While the overall turnover is high, the Departmental Insights report reveals that Sales have the highest turnover rates. The sales department is particularly concerning due to its sheer size, representing 2,099 of the 3,000 employees, although the data also shows that the Sales department is a combination of several divisions. This means that a large number of employee departures are concentrated in this department, which could be a symptom of management issues, high-pressure environments, or inadequate support. The Supervisory Span of Control chart for sales shows that some supervisors manage over 300 employees, which is far beyond the recommended span of control (typically 7-15 employees) and likely contributes to employee burnout and lack of individual attention.

### Aging Workforce: 
- The Age Group Analytics report highlights a significant demographic risk. The workforce is heavily concentrated in the Baby Boomer (33.0%) and Millennial (27.1%) generations. The large population of Baby Boomers, many of whom are likely approaching retirement, poses a significant risk of knowledge loss. When these highly experienced employees leave, they take with them years of institutional knowledge, specialized skills, and client relationships. This could create a leadership vacuum and disrupt business operations if a proper succession plan isn't in place.

<img width="1049" height="586" alt="Age Group Analytics" src="https://github.com/user-attachments/assets/645c67d4-aa34-44f0-a1bd-d4b8c6559adf" />


### Performance Management: 
The Performance Distribution chart reveals an unusual pattern, particularly in the Sales department, where a high percentage of employees are rated as "Fully Meets." There are very few employees in the "Exceeds" or "Needs Improvement" categories. This could indicate a few things: 
- "Grade Inflation", Managers may be hesitant to give low ratings, leading to an inaccurate representation of performance.
- Lack of Differentiation: The performance management system may not be effective at identifying and rewarding top performers or addressing poor performance.
- Lack of Training: Supervisors may not be properly trained to give objective and constructive feedback, which can demotivate high-performing employees who feel their efforts are not recognized.

<img width="1051" height="586" alt="Departmental Insights" src="https://github.com/user-attachments/assets/94500ba0-c9ad-46b8-b420-0d2821b69e5d" />

---

## Recommendations
### Implement a Comprehensive Employee Retention Strategy
- Given the critical turnover rate, the company must take immediate action. This should start with a deep-dive analysis to pinpoint the specific reasons for employee departures.
- Conduct Exit Interviews: Systematically interview all departing employees to gather qualitative data on why they are leaving. Key questions should focus on compensation, work-life balance, management effectiveness, and career development opportunities.
- Launch an Employee Engagement Survey: Conduct regular surveys (e.g., quarterly or semi-annually) to gather feedback from current employees. This can identify dissatisfaction and address potential issues before they lead to voluntary turnover.
- Enhance Compensation and Benefits: Benchmark salaries against competitors, especially in high-turnover departments like IT/IS and Sales, to ensure the company remains competitive.
### Restructure and Support the Sales Department
- The sales department requires targeted intervention to address its high turnover and supervisory issues.
- Reduce Supervisory Span of Control: Re-evaluate the ratio of managers to employees across all department. Consider promoting top performers to managerial roles or hiring new supervisors to ensure employees receive adequate coaching, feedback, and support.
- Revamp the Performance Management System: Provide comprehensive training to managers on how to conduct effective performance reviews. Introduce a more objective rating system that encourages managers to differentiate between performance levels, which can help in identifying and rewarding top talent and addressing underperformance.
### Develop a Proactive Succession and Knowledge Transfer Plan
- To mitigate the risk of losing institutional knowledge from an aging workforce, the company should invest in long-term strategic initiatives.
- Establish Mentorship Programs: Create formal mentorship programs that pair experienced Baby Boomers and Gen X employees with younger generations (Millennials and Gen Z). This facilitates the transfer of knowledge and skills.
- Create Knowledge Repositories: Encourage employees to document their processes, best practices, and project information in a centralized, accessible location. This ensures critical knowledge remains with the company even after an employee leaves.
- Identify and Nurture High-Potential Employees: Use performance data and supervisor recommendations to identify high-potential employees in each department. Provide them with targeted training and leadership development opportunities to prepare them for future senior roles.
### Optimize Recruitment and Onboarding
- The company’s high turnover rate suggests it’s constantly hiring to fill vacancies. This cycle is expensive and inefficient.
- Refine Recruitment Channels: Analyze which recruitment channels are bringing in the best long-term hires. For departments struggling to attract young talent, like IT/IS and Production, tailor recruitment strategies to appeal to younger generations, perhaps by highlighting opportunities for skill development and innovation.
- Improve the Onboarding Process: A solid onboarding program can improve new hire engagement and reduce early turnover. Ensure new employees feel supported, connected, and clear on their roles and responsibilities from day one.
### Diversity Broadening
- Consider broadening the gender category to include employees who might identify differently.

---

## What Could Be Improved
- More Granular Data: The analysis could be improved with more detailed data, such as employee salary, performance review scores, and reason for termination (this is included in the data but it is not cognitive, alluding to data quality issues). This would allow for a more in-depth analysis of turnover drivers.
- Predictive Analytics: The project could be extended to include a machine learning model to predict which employees are at the highest risk of leaving.
- Real-time Data: Integrating the dashboard with a live data source (e.g., an HRIS system) would provide real-time insights instead of relying on a static dataset.

---

## Final Reflection
This project successfully demonstrated the power of Power BI in transforming raw data into meaningful business intelligence. It highlights how a well-designed HR dashboard can serve as a critical tool for strategic decision-making, enabling an organization to move from reactive to proactive workforce management. The insights gained from this analysis serve as a solid foundation for addressing significant challenges and improving overall organizational health.
