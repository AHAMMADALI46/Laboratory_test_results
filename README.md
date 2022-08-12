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

Options validvarname=upcase missing=' '
        nofmterr;
Proc format;
value $ lbtcd;
"Albumin"="ALB"
"Chloride"="CL"
"Chloresterol_total"="Chol"
"creatine_kinase"="CK"
"Creatinine"="Creat"
"Eosinophils"="Eos"
"Glucose"="Gluc"
"Granulocytes"="Gran"
"Hematocrit"="HCT"
"Hemoglobin"="HGB"
"Large_unstained_cell"="LGLUCLE"
"Lymphocytes"="LYM"
"Monocytes"="Mono"
"Neutrophils"="neut"
"Phosphorus"="Phos"
"Potassium"="K"
"Protein_total"="Albprot"
"P_hki_272_lmm_p"="PHK"
"Relative_actual_time"="Reltime"
"Reltv_act_time_unit"="Reltu"
"Sodium"="NA"
"triglycerides"="TRIG"
"troponin_I_cardiac"="Troponi"
"Troponin_T_Cardiac"="Tropont"
"Urea"="Urea"
"Uric_acid"="urate"
"UR_amorphous_sed"="UAMOR"
"UR_amorph_crystals_c"="Cyamporph"
"UR_bacteria"="BCT"
"UR_CGRAN_CASTS"="CSGRAN"
"UR_Epithelial_cells"="Epic"
"Ur_epi_renam_cells"="Epituce"
"UR_EPI_SQUAM_Cells"="Episqce"
"UR_EPI_TRANS_CELL"="Epitce"
"UR_FGRAN_CASTS"="CSGRANF"
"UR_GLucose_sugar"="UGLUC"
"UR_PROTEIN_ALBUMIN"="PROT"
"UR_RBC_NUM"="URBC"
"UR_SPECIFIC_GRAVITY"="SPGRAV"
"UR_UNSPEC_CRYSTALS"="UCRY"
"UR_URIC_ACID_CRYSTALS"="CYRIAC"
"UR_UROBILINOGEN"="UROBIL"
"UR_USPECIFIED_CASTS"="UCASTS"
"UR_WBC_CASTS"="CSWBC"
"UR_YEAST_C"="YEAST";

Value $ LTEST
"CK_MB"="Creatine kinase MB"
"CK_MB_MEAS"="Creatine Kinase MB MEAS"
"SGOT_AST"="Aspartate Aminotransferase"
"SGPT_ALT"="Alanine Aminotransferase"
"UR_CAOX_CASTS"="Calcium Oxalate casts in urine"
"UR_CAOX_CRYSTALS"="Calcium Oxalate crystals in urine"
"UR_WBC_NUM"="Number of white blood cells in urine"
"WBC_MIXED"="Leukocytes; white blood cells Mixed"
"RBC"="Erythrocytes; red blood cells"
"Urine_beta_hcg"="pregnancy test"
"serum_beta_hcg"="choriogonadotropin Beta";
run;

data lb1 (keep=studyid domain usubjid lbtestcd lbtest lbcat lborres lbstnrlo lbstnrhi lbnrind lbspec lbdtc lbfast visitnum
set raw.labtest;
studyid=study;
domain="LB";
usubjid=catx("-", study, stdysite, patient);
lbtestcd=put(Lparm, $LbTCD.);
lbtest=put(Lparm, $Ltest.);
Lbcat=;
Lborres=lvalc;
lborresu=Unit;
lbornrlo=Range_l;
Lbornrhi=range_h;
lbstresc=Put(Lvaln_P, $ 15.);
if Lborres Eq "Neg" then lbstresc="neg";
else if Lborres Eq "POS" then Lbstresc="pos";
Lbstresn=lvaln_p;
Lbstresu=translate(unit_P, "%","L/L");
lbstnrlo=Range_lp;
Lbstnrhi=Range_hp;
Lbrind=LNH;
if LBCAT="Hematology" then lbspec="SERUM";
else if lbcat="blodd chemistry" then lbspec="Blood";
else if lbcat="Urinalysis" then lbspec="urine";
Lbdtc=Put(datepart(tstdt), Is8601DA.);
Lbendtc=put(datepart(tstdt), IS8601DA.); 
if fstatus not in ("Unknown" "Not done" "No") then LBfast="Y";
else if fstatus="No" then lbfast="N";
Ele lbfast=" ";
Lbfast=fstatus;
visit_=CPENM;
Visitnum=Visit;
run;

/*Baseline flag data base*/
proc sort data=lb1; by usubjid lbdtc lbtestcd lbcat; run;
data base (rename=(visit_=visit) keep=usubjid lbb lfl);
set lb1 (where=(visitnum=1 And LBORRES NE ' '));
by usubjid lbdtc lbtestcd lbcat;
if last.Lbdtc then lbblfl="Y";
else lbblfl=" ";
run;

/*Merge back with the previous LB dataset*/
proc sort data=lb1; by usubjid ; run;
proc sort data=base; by usubjid; run;

data lb2;
merge lb1 (in=a) base (keep usubjid lblfl);
if a;
by usubjid;
run;

proc sort data=lb2; by usubjid; run;
proc sort data=sdt.sdtm_dm out=dm (keep=usubjid rfstdtc rfendtc); by usubjid; run;
data lb_dm;
merge lb2 (in=A) DM;
by usubjid;
if a;

/*LBDY-Study Day*/
Proc sort data=Lb_dm; by Usubjid lbdtc lbcat lbtestcd; run;
Data study_day (drop=X Y);
set Lb_dm;
X=input(LBDTC, YYMMDD10.);
Y=Input(RFSTDTC, YYMMDD10.);
if X NE . AND Y NE. Then do;
if X<Y then LBDY=X-Y;
if X >=Y then LBDY=(X-Y)+1;
end;
run;

Proc sort data=study_day; by usubjid LBDTC LBCAT LBTESTCD; run;
data lbseq;
set study_day;
by USUBJID LBDTC LBCAT LBTESTCD;
if First.USUBJID then LBSEQ=1;
else LBSEQ+1;
run;
        
/*Epoch: it is the part of the study
if LBDTC<RFSTDTC then Epoch="Screening";
Else if LBDTC>=RFSTDTC and LBDTC<=RFENTDC then epoch="Treatment";
else if LBDTC>RFENDTC THen EPOCH="Follow-Up";
*/

If X<Y Then Epoch="Screening";
else if x>=Y And X<=Y then Epoch="Treatment"; 
else if x>Y then Epoch="Follow-up";
run;

data sdtm_db;
retain studyid domain usubjid lbseq lbtestcd lbtest lbcat lborres 


/*few more variables*/
LBSTAT-------status completion
LBREASND------Reason for not done;

Data LB1_;
set LB1;















