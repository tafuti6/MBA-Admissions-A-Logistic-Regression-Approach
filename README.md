# MBA-Admissions-A-Logistic-Regression-Approach
## Introduction;
Deciding to pursue an MBA is a big step for many aspiring business leaders. In this project, I will be diving into synthetic data generated from the Wharton Class of 2025's statistics to explore what factors might influence admission decisions. This dataset includes a range of variables like gender, race, GPA, GMAT scores, and work experience, which can provide some interesting insights into the admissions process.
Through this analysis, I will be looking at some key questions, such as:
- How do admission rates differ among various demographic groups?
- What impact do academic metrics like GPA and GMAT scores have on whether someone gets admitted?
- Does work experience play a role in the decision-making process?
- What are some identifiable relationships between the variables in the dataset? 

## Install and load packages; 
- library(ggplot2); Used for creating elegant data visualizations using the Grammar of Graphics.
- library(readr); Facilitates reading rectangular data (like CSV files) into R efficiently and conveniently.
- library(dplyr); Provides a set of functions for data manipulation, making it easier to transform and summarize data.
- library(car); Contains functions for regression analysis and diagnostic tools, particularly useful for checking multicollinearity and other model assumptions.
- library(pROC); Used for visualizing and analyzing Receiver Operating Characteristic (ROC) curves, including calculating the area under the curve (AUC).

## Loading data;
``` (R)
MBA<- read_csv("https://raw.githubusercontent.com/tafuti6/tonyafuti/refs/heads/main/MBA.csv")
glimpse(MBA)
```
## Clean data by;
- 1. Checking for missing values to prevent errors in analysis and also ensure data integrity.
```
missing_values <- colSums(is.na(MBA))
```
- 2. Converting categorical variables to factors for R to interpret them correctly in the context of statistical modeling and analysis to ensure accurate results and meaningful insights.
```
MBA$gender <- as.factor(MBA$gender)
MBA$race <- as.factor(MBA$race)
MBA$international <- as.factor(MBA$international)
```
- 3. Removing duplicates to ensure the analysis is accurate, unbiased, efficient, and trustworthy.
```
MBA <- MBA %>% distinct(application_id, .keep_all = TRUE)
```
- 4. Saving the cleaned data in respective working directory; The cleaned data is a refined, reliable version of the original dataset, ensuring that the analysis or model-building is based on accurate, high-quality information. 
```
write_csv(MBA, "cleaned_MBA_data.csv")
```
## Data Analysis;
- Load the cleaned data; Cleaned data can be loaded from your specified directory. Since the new csv is saved in my working directory, the code I used is; 
```
MBA_1 <- read_csv("C:/Users/tafut/Desktop/GITHUB/Data/cleaned_MBA_data.csv")
```
- Alternatively, I have uploaded the cleaned data on my GitHub so you can load;
```
MBA_1 <-read_csv("https://raw.githubusercontent.com/tafuti6/tonyafuti/refs/heads/main/cleaned_MBA_data.csv") # Note that cleaned data is assigned to object MBA_1
```
- 1. Application for Admission; I coded a bar plot to read the total number of applicants, differentiated by Gender and also the respective admission decision that was made. The position="dodge" function used in this code is to show side-by-side bars.
```
ggplot(MBA_1, aes(x = gender, fill = admission)) +
  geom_bar(position = "dodge") +
  labs(title = "Admission Application by Gender",
       x = "Gender",
       y = "Applications") +
  theme_minimal()
```
![Admission Applications](https://github.com/user-attachments/assets/56ed4e38-bb92-4adc-a547-75f784cb8524)

To get a table with counts;
```
summary_data <- MBA_1 %>%
  group_by(gender, admission) %>%
  summarise(count = n(), .groups = 'drop')
```
![image](https://github.com/user-attachments/assets/17eaa921-b8bd-4f01-bc30-8cb3805f61d2)

Bar plots with counts;
```
ggplot(summary_data, aes(x = gender, y = count, fill = admission)) +
  geom_bar(stat = "identity") + 
  geom_text(aes(label = count), position = position_stack(vjust = 0.5)) + 
  labs(title = "Application Rates by Gender",
       x = "Gender",
       y = "Admissions") +
  theme_minimal()
```
![Application Rates by Gender](https://github.com/user-attachments/assets/88959928-7d35-458c-9199-196fdc04897c)

- Admission based GPA and GMAT Score Distribution;
For GPA
```
ggplot(MBA_1, aes(x = gpa, fill = admission)) +
  geom_density(alpha = 0.5) +
  labs(title = "GPA Distribution by Admission Status")
```
![image](https://github.com/user-attachments/assets/3152a946-4fea-4d98-9f22-ea10bad5a30d)
For GMAT
```
ggplot(MBA_1, aes(x = gmat, fill = admission)) +
  geom_density(alpha = 0.5) +
  labs(title = "GMAT Score Distribution by Admission Status")
```
![image](https://github.com/user-attachments/assets/0f3978c7-0111-4acf-abf3-3e2d8ad43a41)

Create GPA and GMAT bins to count values with visualization
```
MBA_1 <- MBA_1 %>%
  mutate(gpa_bins = cut(gpa, breaks = seq(0, 4, by = 0.5), include.lowest = TRUE, right = FALSE),
         gmat_bins = cut(gmat, breaks = seq(200, 800, by = 100), include.lowest = TRUE, right = FALSE

#Count the number of admissions by GPA and GMAT bins
count_data <- MBA_1 %>%
  group_by(gpa_bins, gmat_bins, admission) %>%
  summarise(count = n(), .groups = 'drop')

ggplot(count_data, aes(x = gpa_bins, y = count, fill = admission)) +
  geom_bar(stat = "identity", position = "stack") +  # Stack the admissions
  facet_wrap(~ gmat_bins, scales = "free_y") +  # Create facets for GMAT bins
  labs(title = "Admissions by GPA and GMAT",
       x = "GPA Bins",
       y = "Count of Admissions") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  
```
![image](https://github.com/user-attachments/assets/b8f78d86-2250-490b-a9df-83ea947b70dd)

- Admission by Work Experience;
```
ggplot(MBA_1, aes(x = work_exp, fill = admission)) +
  geom_histogram(binwidth = 0.5, position = "identity", alpha = 0.5) +
  labs(title = "Work Experience Distribution by Admission Status")
```
![image](https://github.com/user-attachments/assets/895420d2-3000-47f8-97a2-0a87ff4a9d04)

- Admission by Race;
```
ggplot(MBA_1, aes(x = race, fill = admission)) +
  geom_bar(position = "dodge") +  
  labs(title = "Admissions by Race",
       x = "Race",
       y = "Number of Admissions") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  
```
![image](https://github.com/user-attachments/assets/ed7de1df-0884-41a7-9f55-04c87af13c22)

Admission Counts by Race
```
admission_counts <- table(MBA_1$race, MBA_1$admission)
view(admission_counts)
```
![image](https://github.com/user-attachments/assets/68fe9fb2-a83e-40c3-90cb-6ae5d705ef34)

## Key Insights;
- Overall admission rates, and how they differ by gender and race;
```
total_applicants <- nrow(MBA_1)
total_admitted <- sum(MBA_1$admission == "Admit", na.rm = TRUE)  # Check for "Admit"
overall_admission_rate <- total_admitted / total_applicants
overall_admission_rate_percentage <- overall_admission_rate * 100
# Print the overall admission rate
cat("Overall Admission Rate:", round(overall_admission_rate_percentage, 2), "%\n")
```
Overall Admission Rate: 14.53 %
- Admission counts by gender;
```
admission_counts_gender <- table(MBA_1$gender, MBA_1$admission)
```
![image](https://github.com/user-attachments/assets/00a680dc-75c9-44f6-9787-5b32f0e021ad)

Calculate admission rates by gender
```
admission_rates_gender <- prop.table(admission_counts_gender, margin = 1)  # Proportion by row (gender)
admission_rates_gender <- as.data.frame(admission_rates_gender)  # Convert to data frame 
colnames(admission_rates_gender) <- c("Gender", "Admission", "Rate (%)")  # Rename columns
print(admission_rates_gender)
```
![image](https://github.com/user-attachments/assets/2a0de274-6ec5-4f77-ac66-ddabdff20192)
Admission rate by Race
By counts;
```
admission_counts_race <- table(MBA_1$race, MBA_1$admission)
```
![image](https://github.com/user-attachments/assets/45946223-7a13-4c94-a708-564bcc1623db)

Calculate admission rates by race in percentage;
```
admission_rates_race <- prop.table(admission_counts_race, margin = 1)  # Proportion by row (race)
admission_rates_race <- as.data.frame(admission_rates_race)  # Convert to data frame for easier handling
colnames(admission_rates_race) <- c("Race", "Admission", "Rate")  # Rename columns
admission_rates_race$Rate <- admission_rates_race$Rate * 100
# Print admission rates by race
print(admission_rates_race)
```






