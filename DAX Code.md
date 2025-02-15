The Dax code I used to perform Data preprocessing are shown below:

1. This DAX code calculates the number of active employees by taking the total number of employees and filtering out those whose attrition status is "No".
ActiveEmployees = 
CALCULATE(
    [Total Employees],
    FILTER(
        DimEmployee,
        DimEmployee[Attrition] = "No"
    )
)

2. This DAX code finds the latest review date in the FactPerformanceRating table. If no reviews exist, it returns "No Review Yet". Otherwise, it returns the most recent review date.
LastReviewDate = 
IF (
    MAX ( FactPerformanceRating[ReviewDate] ) = BLANK (),
    "No Review Yet",
    MAX ( FactPerformanceRating[ReviewDate] )
)

3. This DAX code finds the maximum SelfRating, but it does so considering the relationship between the FactPerformanceRating table and the DimRatingLevel table on the RatingID column.
SelfRating =
CALCULATE (
    MAX ( FactPerformanceRating[SelfRating] ),
    USERELATIONSHIP ( FactPerformanceRating[SelfRating], DimRatingLevel[RatingID] )
)

4. This DAX code calculates the date of the next review. If no reviews have been done, the next review date is calculated as 365 days after the hire date. Otherwise, it's 365 days after the latest review date.
NextReviewDate = 
VAR reviewOrHire =
IF (
    MAX ( FactPerformanceRating[ReviewDate] ) = BLANK (),
    MAX ( DimEmployee[HireDate] ),
    MAX ( FactPerformanceRating[ReviewDate] )
)
RETURN
reviewOrHire + 365
