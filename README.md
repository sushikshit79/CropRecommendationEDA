# CropRecommendationEDA

## Project Title
Crop Recommendation - Machine Learning Model

**Author**
Sushikshit Billa

### Executive summary

Crop selection plays a critical role in determining agricultural productivity, profitability, and financial stability for farmers and the broader agricultural ecosystem. Selecting crops that are well suited to the geographic and environmental conditions of a region can significantly improve yields and reduce operational risk. However, many crop decisions continue to rely on traditional practices, intuition, or incomplete information, which can lead to suboptimal outcomes and increased financial exposure for both farmers and agricultural lenders.

This project develops a data-driven crop recommendation approach by leveraging historical agricultural production data, including yield and irrigation information at the state and county levels. Using machine learning techniques, the model analyzes geographical agricultural data to identify crops and sub-commodity categories that are most suitable for a given region. The resulting model is capable of recommending the most likely crop options for a specific location, along with ranked alternatives, providing practical decision support for agricultural planning.

### Rationale
Crop selection is a critical decision in agriculture because it directly affects yield, profitability, and the financial stability of farming operations. When this decision is made without reliable, data-driven insights, farmers often rely on traditional practices, personal experience, or incomplete information about regional crop performance. While these approaches may work in some cases, they may not fully capture historical production trends or geographic variability in crop suitability. As a result, farmers may select crops that are not well suited for their region, which can lead to lower yields, reduced profitability, and increased financial risk.

The lack of objective, data-driven guidance also impacts other stakeholders in the agricultural ecosystem. Agricultural lenders, financial institutions, and agribusiness organizations must evaluate the viability and risk associated with crop production when making lending or investment decisions. Without analytical insights into which crops historically perform well at the state and county levels, it becomes more difficult to assess regional crop suitability and associated risks. Developing a data-driven model to analyze historical agricultural data can therefore help farmers and stakeholders make more informed decisions and improve the overall effectiveness and stability of agricultural planning.

### Research Question
Can a machine learning model accurately recommend the most suitable agricultural commodity for a farmer based on state and county level production and irrigation data?

### Data Sources
The primary data source for this project is an agricultural production dataset containing state- and county-level crop information, including irrigated and non-irrigated acreage and yield. The data is obtained from the USDA (United States Department of Agriculture) QuickStats database and is downloaded as a CSV report from https://quickstats.nass.usda.gov/. This dataset serves as the core data source for model training and evaluation.

### Data Analysis

#### Data Features and Descriptions 
* Program – USDA provide the data as two programs SURVEY and CENSUS, this project chooses information collected as part of Survey Program.
* Year – Year in which the data was collected.
* Period – Reporting period (e.g., YEAR). this project choose to evaluate information collected on yearly basis.
* Geo Level – Level at which the geographic detail was collected like STATE or COUNTY.
* State – Name of the state.
* State ANSI – Numeric ANSI/FIPS code for the state.
* Ag District –  It is a designated geographic area, typically initiated by landowners and certified by state/local governments, designed to protect and promote active farming.
* Ag District Code – Code for the agricultural district.
* County – Name of the County in a state.
* County ANSI – Numeric ANSI/FIPS code for the county.
* Zip Code – ZIP code at county level.
* Region – Farm Resource Regions" are categorized on shared characteristics rather than rigid state boundaries. These regions are derived from cluster analysis of commodities produced, soil, climate, and water. 
* watershed_code – Numeric code for watershed area.
* Watershed – It is defined as a specific area of land—ranging from small plots to vast regions—where all water, including rain and snowmelt, drains to a common outflow point, such as a creek, river, lake, or reservoir.
* Commodity – Type of Crop type.
* Data Item – It is a field that provide a combination of crop sub type and yield measured in BU(Bushels) or Tonnes per Acre 
* Domain – It refers to the sub-classification attribute used to break down or subset a data item , such as production, acreage, or yield. With classification of measurement in TOTAL or IRRIGATED. This project choose to limit the data attributes for TOTAL based on  yield.
* Domain Category – Subcategory of domain is not specified by USDA.

#### Data Observations

* Several data attribues like **Program, Period, Geo Level, watershed_code, Domain and Domain Category** provided by USDA contains constant or non-informative values. Therefore, will not contribute to predictive performance.
    * Program has constant value "SURVEY"
    * Period has constant value "YEAR"
    * Geo Level has constant value "COUNTY"
    * watershed_code has constant value "'00000000"
    * Domain has constant value "TOTAL"
    * Domain Category has constant value "NOT SPECIFIED"

* Data attributes like **Week Ending, Zip Code, Region and Watershed** are empty.

* **Commodity** provides a generic classification of crops but does not capture further details such as specific varieties within the same crop for example **corn grain vs silage**. The absence of this information may result in farmers, lenders, and other agricultural stakeholders making decisions based on less productive crop options. This limitation can increase lending risk, reduce farm profitability, and lead to higher inventory and operational costs for stakeholders that provide services across the crop production cycle.

* **Data Item** carries a significant amount of information, including sub-categories of crops and the associated yield measurements expressed in different units such as bushels or tonnes. The field by itself is not  useful for analysis and requires additional processing. Decomposing the Data Item into multiple structured features, such as crop sub-category and standardized yield units, enables more meaningful analysis and supports better outcomes for farmers, lenders, and other agricultural stakeholders.

* The **Value** of the crop can be misleading because yield values are represented in different units, such as bushels or tonnes, as indicated in the **Data Item** column. These values must be standardized to a common unit to enable correct prediction based on crop performance.

#### Missing Data Analysis
* Over 80% of the data is missing in **CV (%)**, which limits its usefulness for analysis and modeling. Given its high level of missingness and secondary relevance to crop prediction, retaining this column does not add meaningful value.
* **Ag District** has 204 missing values. Additionally, Ag District remains consistent for a given combination of State and County and does not provide additional variance beyond what is already captured by State and County. As a result, it is not relevant to the current business requirement.
* **County ANSI** is a coded representation of the County attribute and does not introduce additional dimensional variance compared to the County name itself. Replacing County with County ANSI for modeling purposes would require additional conversion logic between inputs and outputs without offering meaningful predictive benefits. 
* Based on these observations, removing the columns **Ag District, County ANSI, and CV (%)** helps reduce dimensionality while retaining the most relevant features that directly impact prediction performance.

Below is the summary table of missing values

![https://github.com/sushikshit79/CropRecommendationEDA/blob/main/images/missing_data_summary.png](https://github.com/sushikshit79/CropRecommendationEDA/blob/main/images/missing_data_summary.png)

### Data Cleanup and Preparation
* Data attribues like Program, Period, Geo Level, watershed_code, Domain and Domain Category provided by USDA contains constant or non-informative values. Therefore, will not contribute to predictive performance, hence removing to simplify feature dimesions.
* Data attributes like Week Ending, Zip Code, Region and Watershed are empty, hence removing them to simplify feature dimesions
* Removal of CV (%) Column: The CV (%) column was removed because approximately 80% of the values were missing, making it unreliable and not useful for meaningful analysis or modeling.
* Handling Missing County ANSI Codes: Missing values in the County ANSI column were replaced with the placeholder value 999, which is commonly used in U.S. data systems to represent unknown or aggregated counties. This approach preserves dataset completeness while avoiding incorrect misclassification.
* Extraction of Structured Fields from Data Item: The Data Item field contained multiple pieces of information embedded in text. This field was parsed to extract crop name, sub-category, and measurement unit, allowing these attributes to be represented as structured columns/features suitable for analysis and modeling.
* Standardization of Sub_Commodity Values: The Sub_Commodity column was standardized by removing identifiers such as “irrigated” and “non-irrigated”. This ensures that the same crop sub-category is consistently represented across records, reducing category fragmentation and improving input quality.
* Standardization of Yield Units: A yield conversion reference sheet was created through research using crop information available from USDA resources and other agricultural data sources. This reference was used to convert yield measurements reported in bushels per acre (BU/Acre) and tons per acre into a standardized unit of pounds per acre (lbs/acre). Standardizing yield units ensures that crop yields are comparable across different commodities and measurement systems.


### Methodology
This project will use supervised machine learning classification techniques, where the target variable is the commodity (crop) and all other attributes are treated as features. The analysis will follow the CRISP-DM strategy and will include business understanding, data preprocessing, feature engineering, categorical encoding, and model training. Baseline models such as Dummy calssifiers and Logistic Regression will be used for interpretability, along with some hyper parameters with advanced models such as Logistic Regression and Random Forests to capture non-linear relationships. Model performance will be evaluated using accuracy and confusion matrices to assess precision and recall characteristics.

### Results
What did your research find?

### Next steps
What suggestions do you have for next steps?

### Outline of project

- [Link to notebook 1]()
- [Link to notebook 2]()
- [Link to notebook 3]()


#### Contact and Further Information
