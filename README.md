# MBA-Admissions-A-Logistic-Regression-Approach
## Introduction;
Deciding to pursue an MBA is a big step for many aspiring business leaders. In this project, I will be diving into synthetic data generated from the Wharton Class of 2025's statistics to explore what factors might influence admission decisions. This dataset includes a range of variables like gender, race, GPA, GMAT scores, and work experience, which can provide some interesting insights into the admissions process.
Through this analysis, I will be looking at some key questions, such as:
•	How do admission rates differ among various demographic groups?
•	What impact do academic metrics like GPA and GMAT scores have on whether someone gets admitted?
•	Does work experience play a role in the decision-making process?
•	What are some identifiable relationships between the variables in the dataset? 
## Packages and tools used;
library(ggplot2) # Used for creating elegant data visualizations using the Grammar of Graphics.
library(readr) # Facilitates reading rectangular data (like CSV files) into R efficiently and conveniently.
library(dplyr) # Provides a set of functions for data manipulation, making it easier to transform and summarize data.
library(car) # Contains functions for regression analysis and diagnostic tools, particularly useful for checking multicollinearity and other model assumptions.
library(pROC) # Used for visualizing and analyzing Receiver Operating Characteristic (ROC) curves, including calculating the area under the curve (AUC).
