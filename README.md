# MBA-Admissions-A-Logistic-Regression-Approach
## Introduction
Deciding to pursue an MBA is a big step for many aspiring business leaders. In this project, I will be diving into synthetic data generated from the Wharton Class of 2025's statistics to explore what factors might influence admission decisions. This dataset includes a range of variables like gender, race, GPA, GMAT scores, and work experience, which can provide some interesting insights into the admissions process.
Through this analysis, I will be looking at some key questions, such as:
- How do admission rates differ among various demographic groups?
- What impact do academic metrics like GPA and GMAT scores have on whether someone gets admitted?
- Does work experience play a role in the decision-making process?
- What are some identifiable relationships between the variables in the dataset? 

## Install & load packages using install.packages() function 
- library(ggplot2); Used for creating elegant data visualizations using the Grammar of Graphics.
- library(readr); Facilitates reading rectangular data (like CSV files) into R efficiently and conveniently.
- library(dplyr); Provides a set of functions for data manipulation, making it easier to transform and summarize data.
- library(car); Contains functions for regression analysis and diagnostic tools, particularly useful for checking multicollinearity and other model assumptions.
- library(pROC); Used for visualizing and analyzing Receiver Operating Characteristic (ROC) curves, including calculating the area under the curve (AUC).

## Loading & data cleaning 
``` (R)
MBA<- read_csv("https://raw.githubusercontent.com/tafuti6/tonyafuti/refs/heads/main/MBA.csv")
glimpse(MBA)
```
### Clean data by;
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
## Data Analysis
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

## Findings
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

Admission count by Race;

```
admission_counts_race <- table(MBA_1$race, MBA_1$admission)
```
![image](https://github.com/user-attachments/assets/45946223-7a13-4c94-a708-564bcc1623db)

Admission rates by race in percentage;

```
admission_rates_race <- prop.table(admission_counts_race, margin = 1)  # Proportion by row (race)
admission_rates_race <- as.data.frame(admission_rates_race)  # Convert to data frame for easier handling
colnames(admission_rates_race) <- c("Race", "Admission", "Rate")  # Rename columns
admission_rates_race$Rate <- admission_rates_race$Rate * 100
# Print admission rates by race
print(admission_rates_race)
```
![image](https://github.com/user-attachments/assets/e658993b-f79d-462f-90a2-621b855f3948)

### Correlation between GPA and GMAT scores and admission decisions;
```
summary(MBA_1[, c("gpa", "gmat")])  # Summarizes GPA and GMAT columns
```
![image](https://github.com/user-attachments/assets/e315518f-6c66-4b0d-bb50-7b7c28861aa7)


Intepretation;

1. GPA Summary:
- Min.: 2.650: The lowest GPA recorded in the dataset is 2.650, indicating the minimum academic performance among the students.
-	1st Qu.: 3.150: The first quartile (25th percentile) GPA is 3.150, meaning that 25% of the students have a GPA lower than this value.
-	Median: 3.250: The median GPA is 3.250, which is the middle value when the GPAs are arranged in order. This suggests that half of the students have a GPA below 3.250 and half have a GPA above this value.
-	Mean: 3.251: The average GPA is approximately 3.251, which gives an overall indication of the students' academic performance.
-	3rd Qu.: 3.350: The third quartile (75th percentile) GPA is 3.350, meaning that 75% of the students have a GPA lower than this value.
-	Max.: 3.770: The highest GPA recorded is 3.770, indicating the maximum academic performance in the dataset.
2. GMAT Summary:
-	Min.: 570.0: The lowest GMAT score in the dataset is 570, which represents the minimum performance on this standardized test.
-	1st Qu.: 610.0: The first quartile GMAT score is 610, indicating that 25% of the students scored below this level.
-	Median: 650.0: The median GMAT score is 650, meaning half of the students scored below this value and half scored above it.
-	Mean: 651.1: The average GMAT score is about 651.1, providing a general sense of performance on this test across the student population.
-	3rd Qu.: 680.0: The third quartile GMAT score is 680, indicating that 75% of the students scored lower than this score.
-	Max.: 780.0: The highest GMAT score recorded is 780, indicating the maximum performance on this standardized test.

The code below then fits a logistic regression model to estimate the effect of GPA and GMAT on the probability of admission, which also calculates correlations between these predictors and the binary admission outcome.

```
Convert admission to a binary variable (1 for Admit, 0 for others)

MBA_1$admission_binary <- ifelse(MBA_1$admission == "Admit", 1, 0)

# Fit a logistic regression model
model <- glm(admission_binary ~ gpa + gmat, data = MBA_1, family = "binomial")

# Summary of the model
summary(model)

# Calculate correlation coefficients
gpa_correlation <- cor(MBA_1$gpa, MBA_1$admission_binary, use = "complete.obs")
gmat_correlation <- cor(MBA_1$gmat, MBA_1$admission_binary, use = "complete.obs")

cat("Correlation between GPA and Admission Decision:", round(gpa_correlation, 2), "\n")
cat("Correlation between GMAT and Admission Decision:", round(gmat_correlation, 2), "\n")

# Scatter plot of GMAT vs GPA, colored by Admission decision
ggplot(MBA_1, aes(x = gmat, y = gpa, color = admission)) +
  geom_point(size = 3, alpha = 0.7) +
  labs(title = "Comparison of Admission Decisions by GMAT and GPA",
       x = "GMAT Score",
       y = "GPA",
       color = "Admission Decision") +
  theme_minimal() +
  theme(legend.position = "bottom")
```
![image](https://github.com/user-attachments/assets/9ba76544-6121-45f5-baa2-6213f4cfb541)

Interpretation;
- (Intercept): The estimated intercept is -7.020632. This value represents the log-odds of being admitted when both GPA and GMAT are zero, which, while not interpretable in a real-world context, sets the baseline for the logistic model.
- GPA: The coefficient for GPA is 0.695146, but it has a high p-value (0.47243), indicating that it is not statistically significant at common alpha levels (e.g., 0.05). This suggests that GPA does not have a significant influence on the probability of admission in this model.
- GMAT: The coefficient for GMAT is 0.010096, with a p-value of 0.00119. This p-value indicates strong statistical significance, suggesting that GMAT scores have a meaningful positive relationship with the likelihood of being admitted. For every one-point increase in GMAT, the odds of admission increase significantly.
- Significance Codes:
The significance codes provide a quick way to assess the importance of the predictors. GMAT is statistically significant (indicated by ****), while GPA is not.
Deviance and AIC:
- Null deviance (650.17) represents the model's performance without any predictors.
- Residual deviance (630.07) shows the model's performance with GPA and GMAT included, indicating some improvement in the fit.
- AIC (636.07) is a measure of model quality, with lower values indicating a better fit while penalizing for the number of predictors.
Missing Data:
- The output notes that 5194 observations were deleted due to missingness, suggesting a significant amount of missing data in the predictors or the outcome variable, which may affect the model's reliability.

This logistic regression analysis indicates that while GPA does not significantly influence admission decisions, GMAT scores do have a significant positive impact. As GMAT scores increase, the likelihood of admission also increases. The model indicates a reasonable fit, but the large number of missing observations suggests caution in interpreting the results, as they may not fully represent the applicant population.

- Correlation between GPA and Admission Decision: 0.1
- Correlation between GMAT and Admission Decision: 0.14

Intepretation; The correlation between gpa and admission_binary is 0.1, suggesting a weak positive relationship. The correlation between gmat and admission_binary is 0.14, which is slightly stronger but still weak. From both the logistic regression and correlation analysis, GMAT scores appear to have a more significant relationship with admission decisions than GPA, though the correlation remains weak. This implies GMAT could be a contributing factor in the admission decision process. The low correlation and lack of statistical significance in the regression suggest that GPA does not play a major role in determining admission in this dataset.

- From the scatter plot;

![image](https://github.com/user-attachments/assets/f6d8be0e-8168-4118-a46c-5b93c2dbc5ec)

-	Higher GMAT and GPA combinations generally correspond to a higher concentration of “Admit” statuses.
-	The “Waitlist” status is distributed across various GMAT and GPA ranges, suggesting that waitlisted candidates have a diverse range of scores.
-	Gray points (NA) represent missing or unrecorded admission decisions and are scattered across all GMAT and GPA values.
In general, higher GMAT scores are positively associated with admission likelihood, indicating that they have a significant impact on the probability of being admitted. While GPAs also show a positive trend, they do not appear to have a statistically significant effect in this analysis. Therefore, GMAT scores seem to hold a slight advantage over GPA in influencing admission decisions, although individual cases may vary.

### Correlation between gender and race using chi-square test; 
```
# Create a contingency table of gender and race, and then perform chi-square test
gender_race_table <- table(MBA_1$gender, MBA_1$race)
chi_test <- chisq.test(gender_race_table)
chi_test
```
![image](https://github.com/user-attachments/assets/df6e1518-5caf-4909-bb71-985fdb046513)

Intepretation;

If the p-value were below 0.05, it would suggest a statistically significant association between gender and race. However, since the p-value is so high, there’s no statistically significant relationship between gender and race in the data. In other words, gender and race appear to be independent of each other.

### Demographic influence on admission decision; 
```
# Convert admission to a binary variable if necessary
MBA_1$admission_binary <- ifelse(MBA_1$admission == "Admit", 1, 0)  # Admitted = 1, Not Admitted = 0

# Enhanced logistic regression model including additional predictors
logit_model_enhanced <- glm(admission_binary ~ gender + race + gpa + gmat + work_exp, 
                            data = MBA_1, family = "binomial")

# Display the model summary
summary(logit_model_enhanced)
```
![image](https://github.com/user-attachments/assets/ba24e0fc-2523-49df-9659-a8dc314cbcb3)

Intepretation;

- Intercept: The intercept is significantly negative, suggesting that, in the absence of other factors, the likelihood of admission is low.
- Gender: The coefficient for genderMale is negative (-0.343046) but not statistically significant (p-value = 0.21656), indicating that being male does not significantly influence admission decisions in this dataset.
- Race: The coefficients for race categories (Black, Hispanic, Other, White) are also not statistically significant, suggesting that race does not have a strong effect on admission decisions based on this model.
- GPA: The coefficient for GPA is positive (2.389645) with a p-value of 0.06180, which is close to conventional significance levels (0.05). This suggests that higher GPAs may positively influence admission, although it's not quite statistically significant at the 0.05 level.
- GMAT Scores: The coefficient for GMAT (0.007694) has a p-value of 0.05217, which is also close to significance, indicating that higher GMAT scores may increase the likelihood of admission.
- Work Experience: The coefficient for work experience (0.197117) is positive but not statistically significant (p-value = 0.12055), suggesting it may not have a strong effect on admission outcomes.
- The enhanced logistic regression model indicates that while GPA and GMAT scores show some potential influence on admission decisions, demographic factors like gender and race do not appear to significantly affect the likelihood of being admitted based on this specific dataset. The model suggests that academic performance is a more important factor than demographics in this context, but the relationships are not yet strong enough to draw definitive conclusions. Further analysis is performed to prove the model's predictive power and validity;
```
# Check for multicollinearity to ensure that the predictor variables in our regression model are not too closely related to each other.
library(car)
vif(logit_model_enhanced)  # Variance Inflation Factor for multicollinearity check to remove inflated standard errors
```
![GVIF](https://github.com/user-attachments/assets/1c0c3aec-fdce-4aeb-a895-735ffd658bc8)


Interpretation;
- gender [VIF = 1.176906 (GVIF^(1/(2*Df)) = 1.084853)]; This indicates low multicollinearity for the gender variable.
- race	[VIF = 1.081942 (GVIF^(1/(2*Df)) = 1.009893)]; This also shows low multicollinearity, indicating that race does not significantly influence multicollinearity in your model.
- gpa [VIF = 1.587245 (GVIF^(1/(2*Df)) = 1.259859)]; This suggests moderate multicollinearity, but still within acceptable limits.
- gmat [VIF = 1.629671 (GVIF^(1/(2*Df)) = 1.276586)]; Like GPA, this indicates moderate multicollinearity.
- work_exp [VIF = 1.013235 (GVIF^(1/(2*Df)) = 1.006596)]; This shows very low multicollinearity, indicating work experience does not inflate standard errors significantly.

```
# Evaluate model performance
predicted_probabilities <- predict(logit_model_enhanced, type = "response")
predicted_classes <- ifelse(predicted_probabilities > 0.5, 1, 0)

# Confusion matrix
table(Predicted = predicted_classes, Actual = MBA_1$admission_binary)
# Encountered an error because all the arguments must have the same length which is likely due to instances of missing values in the admission binary.
sum(is.na(MBA_1$admission_binary))  # Count missing values in admission_binary which was 5194
length(predicted_classes)  # Length of predicted classes was 692
length(MBA_1$admission_binary)  # Length of actual admission values
# Predicting probabilities for the original dataset 6194
predicted_probabilities <- predict(logit_model_enhanced, newdata = MBA_1, type = "response")

# Create predicted classes based on a threshold (e.g., 0.5) for random guessing
predicted_classes <- ifelse(predicted_probabilities > 0.5, 1, 0)

# ROC curve to evaluate model performance
library(pROC) #package used for the ROC curve to visualize students that were accepted and those not accepted

roc_curve <- roc(MBA_1$admission_binary, predicted_probabilities)
plot(roc_curve, main = "ROC Curve")
auc(roc_curve) 
```
![image](https://github.com/user-attachments/assets/8a946396-2bc7-4862-8a46-e2737d2af444)

The area under the curve (AUC) of 0.6674 indicates that the model has a moderate ability to distinguish between admitted and not admitted applicants based on the demographic characteristics analyzed. While the model shows some predictive capability, it suggests that admissions may not be strongly influenced by these demographic factors alone, and there is still significant room for improvement in accuracy. The presence of missing values in the admission data may require attention to ensure a more comprehensive evaluation. Further enhancements to the model could lead to better predictions regarding admissions.

## Values obtained from analysis
![image](https://github.com/user-attachments/assets/b60ced14-e374-401a-b971-f0d82d27aa3c)

## What does the analysis tell us?
Based on the comprehensive analysis of the MBA admissions dataset, we can draw several key conclusions regarding the influence of demographic factors, academic performance indicators, and other variables on admission decisions.
1.	Demographic Factors: The analysis indicates that demographic characteristics such as gender and race do not exhibit significant effects on admission outcomes in the logistic regression models, suggesting that admissions decisions are primarily influenced by academic performance metrics rather than demographic attributes.
2.	Academic Performance Indicators: While both GPA and GMAT scores are positively associated with admission likelihood, GMAT scores demonstrate a significant impact, whereas GPA does not show statistical significance. This suggests that standardized testing may play a more crucial role in the evaluation process, potentially indicating a preference for quantifiable metrics in assessing candidate suitability.
3.	Influence of Work Experience: The inclusion of work experience as a variable showed a positive trend in its relationship with admission decisions, although it did not reach statistical significance in the enhanced model. This implies that relevant professional experience might enhance a candidate's profile, even if it is not decisively influential.
4.	Model Performance: The overall model performance, as indicated by the Area Under the Curve (AUC) value of 0.6674, reflects a fair ability of the model to distinguish between admitted and non-admitted candidates, indicating some room for improvement. This suggests that further refinement of the model or inclusion of additional predictors may yield better predictive power.
5.	Missing Data: A significant number of missing observations in the dataset highlights a limitation in the analysis, potentially affecting the reliability of the results. Addressing missing data and exploring additional factors in future research could enhance the understanding of admission decision influences.

## Conclusion
In conclusion, this analysis underscores the importance of academic performance, particularly GMAT scores, in the admissions process while suggesting that demographic factors may not significantly influence admission decisions. The findings provide a foundation for further investigation into the complex dynamics of admissions, emphasizing the need for a holistic approach that considers multiple factors to ensure fair and equitable decision-making in the graduate admissions process.

As I reflect on my journey analyzing the MBA admissions dataset, I am struck by the insights I have gained and the challenges I faced along the way. It became clear early on that demographic factors like gender and race did not significantly influence admission outcomes, which was an interesting finding. However, what really stood out to me was the powerful role of academic performance indicators, especially GMAT scores. These scores seem to carry a weight that GPA does not, leading me to think about how standardized tests might shape our perceptions of a candidate's potential.

One of the fun discoveries was how the inclusion of work experience added another layer to the analysis. While it did not show strong statistical significance, I appreciated how it reflected the multifaceted nature of an applicant's profile. Each variable felt like a piece of a puzzle that, when combined, could give us a clearer picture of what makes an applicant stand out.

However, the journey was not without its challenges. I grappled with a significant amount of missing data, which made it tough to draw definitive conclusions. It was frustrating at times to see how missing values could even lead to errors in the codes, and this reminds me of the importance of comprehensive data collection. This experience challenged me to explore alternative approaches to the analysis.

As I wrapped up my exploration of this dataset, I felt a sense of accomplishment and a renewed appreciation for the complexity of admission decisions. This experience taught me that while numbers and statistics tell part of the story, there’s a human element behind every application that we should not overlook. I am excited to carry these insights forward and continue exploring the intricacies of data analysis in my future work.
Thank you for being part of this journey with me!
