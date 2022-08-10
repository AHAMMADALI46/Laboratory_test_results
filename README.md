# Laboratory_test_results
/*Laboratory test results----------LB
topic variable ---------LBTESTCD
Structure:One record/subject/labtest/visit/time-Point
common variables: studyid domain usubjid lbseq- are Required variables
LBGRPID------
LBREFID----- Lab specimen ID or sample id
LBSPID------CRF page number
LBTESTCD------short name of the test
LBTEST--------description of the test
LBORRES--------Original results
LBORRESU-------Orignial Units (Variable Qualifier)
LBSTRESC-------standard results in char (result Qualifier)
LBSTRESN-------Standard results in numeric
LBSTRESU-------standard units (Variabel Qualifier)
LBORNRLO----------Lower normal results
LBORNRHI---------Higher normal results
LBSTNRLO---------Standard Lower normal results
LBSTNRHI---------Standard higher normal results

LBCAT-------Category of the test
LBSCAT------sub-category

LBBLFL-----Baseline flag

/*Baseline: Non-missing results are capturing before administration of the study drug
LBDRVFL: Derivied flags
LBSPEC----------specimen type
LBNAM-----------Name of the laboratory

timing variables:
visit---------name of the visit
visitnum-------Numerical version of the visit
visitdy-------planned study day collection LBDTC-RFSTDTC
lbdy---------study day collection
LBDTC---------date and time collection of the lab data
LBENDTC-------End date of the sample received.

LBTPT-------Timepoint
LBTPTNUM-----Numerical Version of timepoint
LBELTM-------Planned timepoint
LBTPTREF-------reference time point
LBRFTDTC------date and time point collection of the sample.

For LAB data we have to use  LIONIC Dictionary******








