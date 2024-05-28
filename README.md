# Recommendation-system
Creating a recommendation system for travel company

1. Create CSV Data Files:
Save the following data in CSV format and place them in a directory named `data`.

users.csv
user_id,name,age,preferred_destination,budget
1,Rahul,24, Paris,1500
2, Karan,32,Tokyo,2000
3,Aryan,25,New York,1000
4,Juhi,29,Paris,1800
5,Darshan,35,London,1200


packages.csv
```csv
package_id,destination,cost,duration,features
1,Paris,1200,5,cultural
2,Tokyo,1800,7,modern
3,New York,900,4,shopping
4,Paris,1500,6,historical
5,London,1000,5,cultural
6,Tokyo,2000,8,adventure
```

Preferences.csv
user_id,feature
1,cultural
2,modern
3,shopping
4,historical
5,cultural


2. Run the Do-File:

Save the following Stata code as `recommendation_system.do`.

 Happy Hunters Recommendation System
 Example Do-File

 Step 1: Import data from CSV files
clear all
set more off

 Importing users data
import delimited using "data/users.csv", clear
save "data/users.dta", replace

Importing packages data
import delimited using "data/packages.csv", clear
save "data/packages.dta", replace

 Importing preferences data
import delimited using "data/preferences.csv", clear
save "data/preferences.dta", replace

Step 2: Load the users data
use "data/users.dta", clear

Load the preferences data
merge 1:1 user_id using "data/preferences.dta"
assert _merge == 3
drop _merge

Load the packages data
merge m:1 destination using "data/packages.dta"
assert _merge == 3
drop _merge

Ensure merged data is saved
save "data/merged_data.dta", replace

 Step 3: Load merged data
use "data/merged_data.dta", clear

Create recommendations based on user preferences and budget
gen recommendation = ""

Loop through each user and recommend packages
{
    forvalues i = 1/`=_N' {
        local user_id = user_id[`i']
        local budget = budget[`i']
        local feature = feature[`i']

        * Find matching packages
        {
            gen match = 0
            replace match = 1 if features == "`feature'" & cost <= `budget'
        }

        * Recommend package if a match is found
        if sum(match) > 0 {
            local recommended_package = package_id[1] if match == 1
            replace recommendation = "Package ID: " + string(`recommended_package') + " (" + destination[1] + ")" if _n == `i'
        } else {
            replace recommendation = "No suitable package found" if _n == `i'
        }
    }
}

 Step 4: Display recommendations
list user_id name recommendation

Save final recommendations
save "data/final_recommendations.dta", replace

 Run the do-file using the command:
      do "path_to_your_do_file/recommendation_system.do"
