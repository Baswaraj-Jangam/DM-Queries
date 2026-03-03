/*=================AJG IN COMPENSATION REPORT DATA MODEL QUERY 09-Feb-2026===================================*/
/*====================AFTER PARAMETER CHANGES======================================*/

/*========================DATA SET 1 BEGINS=========================*/
WITH a AS (
    SELECT pj.name
	     ,pg.name 
         ,rel.payroll_relationship_number                                     AS payroll_relationship_number
         , comp.effective_start_date                                           AS effective_start_date
         , comp.effective_end_date                                             AS effective_end_date
         , rel.person_id                                                       AS person_id
         , papf.person_number                                                  AS person_number  -- keep as VARCHAR2
         , per_extract_utility.get_decoded_lookup(
               'ORA_HRX_IN_FBP_COMP_NAME',
               MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMP_NAME', ri.value1, NULL))
           )                                                                   AS comp_name
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMP_NAME', vd.effective_start_date, NULL)) AS Plan_Creation_Date
         , per_extract_utility.get_decoded_lookup(
               'ORA_HRX_IN_FBP_FREQUENCY',
               MAX(DECODE(pvd.base_name,'ORA_HRX_IN_FBP_FREQUENCY', pri.value1, NULL))
           )                                                                   AS comp_frequency
         , per_extract_utility.get_decoded_lookup(
               'ORA_HRX_IN_FBP_COMP_TYPE',
               MAX(DECODE(vd.base_name,'ORA_HRX_IN_FBP_COMP_TYPE', ri.value1, NULL))
           )                                                                   AS comp_type
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMPONENT_VALUE', ri.value1, NULL))         AS comp_value
         , MAX(DECODE(pvd.base_name,'ORA_HRX_IN_FBP_PLAN_NAME', pri.value1, NULL))              AS plan_name
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_MINIMUM_VALUE',  ri.value1, NULL))          AS min_value
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_MAXIMUM_VALUE',  ri.value1, NULL))          AS max_value
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMP_SEQUENCE',  ri.value1, NULL))          AS comp_sequence
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMPONENT_VALUE', fnd_date.date_to_canonical(ri.last_update_date), NULL)) AS update_date
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_CALC_VALUE',     ri.value1, NULL))          AS comp_calc_value
         , MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_OLD_VALUE',      ri.value1, NULL))          AS comp_old_value
    FROM    pay_pay_relationships_dn    rel,
            pay_dir_cards_f             card,
            pay_dir_card_definitions    cdef,
            pay_dir_card_components_f   pcomp,
            pay_dir_card_comp_defs_f    pdef,
            pay_old_value_defns_f       pvd,
            pay_old_range_items_f       pri,
            pay_dir_card_components_f   comp,
            pay_dir_card_comp_defs_f    def,
            pay_old_value_defns_f       vd,
            pay_old_range_items_f       ri,
            per_all_people_f            papf,
			per_all_assignments_m       paam,
			per_jobs          	        pj,
			per_grades                  pg
			
    WHERE   cdef.base_display_name            = 'ORA_HRX_IN_FBP_CARD'
      AND   card.dir_card_definition_id       = cdef.dir_card_definition_id
      AND   card.payroll_relationship_id      = rel.payroll_relationship_id
      AND   comp.dir_card_id                  = card.dir_card_id
      AND   comp.dir_card_comp_def_id         = def.dir_card_comp_def_id
      AND   pcomp.dir_card_id                 = card.dir_card_id
      AND   pcomp.dir_card_comp_def_id        = pdef.dir_card_comp_def_id
      AND   pdef.legislation_code             = 'IN'
      AND   pdef.base_component_name          = 'ORA_HRX_IN_FBP_SAL_STRUCTURE'
      AND   def.base_component_name           = 'ORA_HRX_IN_FBP_SAL_STRUCT_COMP'
      AND   def.legislation_code              = 'IN'
      AND   comp.dir_card_comp_id             = vd.source_id
      AND   vd.source_type                    = 'PDCC'
      AND   ri.value_defn_id                  = vd.value_defn_id
      AND   pcomp.dir_card_comp_id            = pvd.source_id
      AND   pvd.source_type                   = 'PDCC'
      AND   pri.value_defn_id                 = pvd.value_defn_id
      AND   pcomp.component_sequence          = comp.context_value2
      AND   rel.person_id                     = papf.person_id
	  
	  AND  paam.person_id = papf.person_id
	  AND  pj.job_id      = paam.job_id
	  AND pj.name is NOT NULL
	  AND pg.name is NOT NULL
	  AND  paam.effective_latest_change = 'Y'
      AND  paam.assignment_type         = 'E'
      AND  paam.legislation_code        = 'IN'
      AND  paam.primary_flag            = 'Y'
      AND  paam.assignment_status_type  = 'ACTIVE'
	   AND  pg.grade_id    = paam.grade_id
	  AND  :pEffectiveDate BETWEEN paam.effective_start_date AND paam.effective_end_date
	  AND  :pEffectiveDate BETWEEN pj.effective_start_date   AND pj.effective_end_date
	  AND  :pEffectiveDate BETWEEN pg.effective_start_date   AND pg.effective_end_date
	  
      AND   :pEffectiveDate BETWEEN rel.start_date               AND rel.end_date
      AND   :pEffectiveDate BETWEEN card.effective_start_date    AND card.effective_end_date
      AND   :pEffectiveDate BETWEEN comp.effective_start_date    AND comp.effective_end_date
      AND   :pEffectiveDate BETWEEN vd.effective_start_date      AND vd.effective_end_date
      AND   :pEffectiveDate BETWEEN ri.effective_start_date      AND ri.effective_end_date
      AND   :pEffectiveDate BETWEEN pcomp.effective_start_date   AND pcomp.effective_end_date
      AND   :pEffectiveDate BETWEEN pvd.effective_start_date     AND pvd.effective_end_date
      AND   :pEffectiveDate BETWEEN pri.effective_start_date     AND pri.effective_end_date
      AND   :pEffectiveDate BETWEEN papf.effective_start_date    AND papf.effective_end_date
	  
	 
	  
	  AND (coalesce(NULL , :JOB_NAME) is null or pj.JOB_ID in (:JOB_name))
	  AND (coalesce(NULL , :person_number) is null or papf.person_id in (:Person_number))
      AND (coalesce(NULL, :Location_Name) is NULL or paam.location_id in (:Location_Name))



    GROUP BY rel.payroll_relationship_number,
             comp.dir_card_comp_id,
             comp.effective_start_date,
             comp.effective_end_date,
             rel.person_id,
             papf.person_number,
			 pj.name,
			 pg.name 
    HAVING  per_extract_utility.get_decoded_lookup(
                'ORA_HRX_IN_FBP_COMP_NAME',
                MAX(DECODE(vd.base_name, 'ORA_HRX_IN_FBP_COMP_NAME', ri.value1, NULL))
            ) IS NOT NULL
),
comp AS (
    SELECT 
           payroll_relationship_number,
           effective_start_date,
           effective_end_date,
           person_id,
           person_number, -- VARCHAR2
           TO_CHAR(Plan_Creation_Date, 'DD-Mon-YY', 'NLS_DATE_LANGUAGE = English') AS Plan_Creation_Date,
           NVL(MAX(CASE WHEN comp_name = 'Special Allowance'                        THEN comp_value END), 0) AS Special_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Total AGP'                                THEN comp_value END), 0) AS Total_AGP,
           NVL(MAX(CASE WHEN comp_name = 'Shift Allowance'                          THEN comp_value END), 0) AS Shift_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Festival Bonus'                           THEN comp_value END), 0) AS Fest_Bonus,
           NVL(MAX(CASE WHEN comp_name = 'Meal Allowance'                           THEN comp_value END), 0) AS Meal_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Children Hostel Allowance'                THEN comp_value END), 0) AS Hostel_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Statutory Pay'                            THEN comp_value END), 0) AS Stat_Pay,
           NVL(MAX(CASE WHEN comp_name = 'Performance Based Incentive'              THEN comp_value END), 0) AS Performance_Incentive,
           NVL(MAX(CASE WHEN comp_name = 'House Rent Allowance'                     THEN comp_value END), 0) AS HRA,
           NVL(MAX(CASE WHEN comp_name = 'Car Lease Driver Salary'                  THEN comp_value END), 0) AS Car_Sal,
           NVL(MAX(CASE WHEN comp_name = 'Driver Salary'                            THEN comp_value END), 0) AS Driver_Sal,
           NVL(MAX(CASE WHEN comp_name = 'Employer''s Contribution to Provident Fund' THEN comp_value END), 0) AS PF_Contri,
           NVL(MAX(CASE WHEN comp_name = 'NPS'                                      THEN comp_value END), 0) AS NPS,
           NVL(MAX(CASE WHEN comp_name = 'Medical Insurance Premium'                THEN comp_value END), 0) AS Medical_Insu,
           NVL(MAX(CASE WHEN comp_name = 'Car Lease'                                THEN comp_value END), 0) AS Car_Lease,
           NVL(MAX(CASE WHEN comp_name = 'Transport Allowance'                      THEN comp_value END), 0) AS Transport_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Health Club Membership'                   THEN comp_value END), 0) AS Health_Club_Mem,
           NVL(MAX(CASE WHEN comp_name = 'NPS Contribution'                         THEN comp_value END), 0) AS NPS_Contri,
           NVL(MAX(CASE WHEN comp_name = 'Children Education Allowance'             THEN comp_value END), 0) AS Edu_Alw,
           NVL(MAX(CASE WHEN comp_name = 'Employee Asset Assistance'                THEN comp_value END), 0) AS Asset_Ast,
           NVL(MAX(CASE WHEN comp_name = 'Overall Fixed Salary'                     THEN comp_value END), 0) AS Fixed_Sal,
           NVL(MAX(CASE WHEN comp_name = 'Overall Flexible Salary'                  THEN comp_value END), 0) AS Flexible_Sal,
           NVL(MAX(CASE WHEN comp_name = 'Gratuity'                                 THEN comp_value END), 0) AS Gratuity,
           NVL(MAX(CASE WHEN comp_name = 'Car Lease Fuel Reimbursement'             THEN comp_value END), 0) AS Fuel_Reimb,
           NVL(MAX(CASE WHEN comp_name = 'Fuel and Maintenance'                     THEN comp_value END), 0) AS Fuel_Maint,
           NVL(MAX(CASE WHEN comp_name = 'Basic Salary'                             THEN comp_value END), 0) AS Basic_Sal,
           NVL(MAX(CASE WHEN comp_name = 'Residential Telephone and Broadband'      THEN comp_value END), 0) AS Telephone_and_BB,
           NVL(MAX(CASE WHEN comp_name = 'Leave Travel Allowance'                   THEN comp_value END), 0) AS LTA,
           NVL(MAX(CASE WHEN comp_name = 'Conveyance'                               THEN comp_value END), 0) AS Conveyance,
           NVL(MAX(CASE WHEN comp_name ='Short Term Incentive'                      THEN comp_value END), 0) AS Short_Term_Incentive
    FROM a
    GROUP BY payroll_relationship_number,
             effective_start_date,
             effective_end_date,
             person_id,
             person_number,
             Plan_Creation_Date
)
SELECT *
FROM comp
ORDER BY person_number


/*=============================DATA SET1 END========================================================*/




/*=============================DATA SET2 BEGIN========================================================*/

SELECT DISTINCT
           papf.person_id                         AS emp_id
         , papf.person_number                     AS "Employee Number"  
         , ppnf.full_name                         AS "Employee Name"
         , pj.name                                AS "Job"
         , pg.name                                AS "Grade"
         , hl.location_name                       AS "Location"
		 , rel.payroll_relationship_number 
         , CASE
               WHEN pg.name LIKE '%-BCK' THEN 'BUCK'
               WHEN pg.name LIKE '%-GRE' THEN 'GRE'
               ELSE 'GCoE'
           END                                    AS Entity
    FROM   per_all_people_f          papf,
           per_all_assignments_m     paam,
           per_person_names_f        ppnf,
           per_jobs                  pj,
           per_grades                pg,
           hr_locations              hl,
		   pay_pay_relationships_dn    rel
    WHERE  1=1
      AND  paam.person_id = papf.person_id
      AND  ppnf.person_id = papf.person_id
      AND  pj.job_id      = paam.job_id
      AND  pg.grade_id    = paam.grade_id
      AND  hl.location_id = paam.location_id
	      AND   rel.person_id                     = papf.person_id
      AND  :pEffectiveDate BETWEEN paam.effective_start_date AND paam.effective_end_date
      AND  :pEffectiveDate BETWEEN papf.effective_start_date AND papf.effective_end_date
      AND  :pEffectiveDate BETWEEN ppnf.effective_start_date AND ppnf.effective_end_date
      AND  :pEffectiveDate BETWEEN pj.effective_start_date   AND pj.effective_end_date
      AND  :pEffectiveDate BETWEEN pg.effective_start_date   AND pg.effective_end_date
      AND  paam.effective_latest_change = 'Y'
      AND  paam.assignment_type         = 'E'
      AND  paam.legislation_code        = 'IN'
      AND  paam.primary_flag            = 'Y'
      AND  paam.assignment_status_type  = 'ACTIVE'
      AND  ppnf.name_type               = 'GLOBAL'
      AND  ppnf.legislation_code        = 'IN'
      AND  hl.country                   = 'IN'
	  
	   AND (coalesce(NULL , :JOB_NAME) is null or pj.JOB_ID in (:JOB_name))
	
	
/*=============================DATA SET2 END========================================================*/


/*=============================DATA SET3 BEGIN========================================================*/

SELECT
    per.person_number,


    TO_CHAR(MAX(dcd.effective_start_date), 'MM-DD-YYYY') AS effective_start_date,
    TO_CHAR(MAX(dcd.effective_end_date),   'MM-DD-YYYY') AS effective_end_date,

    COALESCE(
        MAX(
            CASE
                WHEN dcd.dir_information_char4 = 'Y' THEN 'New Tax Regime'
                WHEN dcd.dir_information_char4 = 'N' THEN 'Old Tax Regime'
                ELSE NULL
            END
        ),
        'Old Tax Regime'
    ) AS tax_regime

FROM per_all_people_f per

LEFT JOIN pay_dir_comp_details_f dcd
       ON dcd.person_id = per.person_id
      AND dcd.dir_information_category = 'HRX_IN_DIR_INCOME_TAX'
      AND TRUNC(SYSDATE) BETWEEN dcd.effective_start_date AND dcd.effective_end_date
      
      AND dcd.effective_start_date BETWEEN per.effective_start_date AND per.effective_end_date

LEFT JOIN pay_dir_card_components_f comp
       ON dcd.dir_card_comp_id = comp.dir_card_comp_id
      AND dcd.effective_start_date BETWEEN comp.effective_start_date AND comp.effective_end_date

LEFT JOIN pay_dir_card_comp_defs_f def
       ON comp.dir_card_comp_def_id = def.dir_card_comp_def_id
      AND def.base_component_name = 'ORA_HRX_IN_CIR_INCOME_TAX'

WHERE TRUNC(SYSDATE) BETWEEN per.effective_start_date AND per.effective_end_date
 AND  (coalesce(NULL , :person_number) is null or per.person_id in (:Person_number))

GROUP BY per.person_number


/*=================================DATA SET3 END=======================*/


/*================================================LOV QUERIES===================================================================*/
/*================================PERSON NUMBER=========================================*/

SELECT DISTINCT
       papf.person_number,papf.person_id
FROM   per_all_people_f            papf,
       per_all_assignments_m       paam,
       per_person_names_f          ppnf,
       per_jobs                    pj,
       per_grades                  pg,
       hr_locations                hl,
       pay_pay_relationships_dn    rel
WHERE  paam.person_id        = papf.person_id
AND    ppnf.person_id        = papf.person_id
AND    pj.job_id             = paam.job_id
AND    pg.grade_id           = paam.grade_id
AND    hl.location_id        = paam.location_id
AND    rel.person_id         = papf.person_id
AND    TRUNC(SYSDATE) BETWEEN paam.effective_start_date AND paam.effective_end_date
AND    TRUNC(SYSDATE) BETWEEN papf.effective_start_date AND papf.effective_end_date
AND    TRUNC(SYSDATE) BETWEEN ppnf.effective_start_date AND ppnf.effective_end_date
AND    TRUNC(SYSDATE) BETWEEN pj.effective_start_date   AND pj.effective_end_date
AND    TRUNC(SYSDATE) BETWEEN pg.effective_start_date   AND pg.effective_end_date
AND    paam.effective_latest_change = 'Y'
AND    paam.assignment_type         = 'E'
AND    paam.legislation_code        = 'IN'
AND    paam.primary_flag            = 'Y'
AND    paam.assignment_status_type  = 'ACTIVE'
AND    ppnf.name_type               = 'GLOBAL'
AND    ppnf.legislation_code        = 'IN'
AND    hl.country                   = 'IN'
ORDER BY papf.person_number
/*================================================================================================================*/


/*============================================Location Name=============================================*/
SELECT distinct hl.location_name, hl.location_id                  
FROM per_all_people_f          papf,
     per_all_assignments_m     paam,
     per_person_names_f        ppnf,
     per_jobs                  pj,
     per_grades                pg,
     hr_locations              hl
WHERE paam.person_id = papf.person_id
  AND ppnf.person_id = papf.person_id
  AND pj.job_id      = paam.job_id
  AND pg.grade_id    = paam.grade_id
  AND hl.location_id = paam.location_id
 
  AND trunc(sysdate) BETWEEN hl.effective_start_date   AND hl.effective_end_date
  AND paam.effective_latest_change = 'Y'
  AND paam.assignment_type         = 'E'
  AND paam.legislation_code        = 'IN'
  AND paam.primary_flag            = 'Y'
  AND paam.assignment_status_type  = 'ACTIVE'
  AND ppnf.name_type               = 'GLOBAL'
  AND ppnf.legislation_code        = 'IN'
  AND hl.country                   = 'IN'
/*============================================================================================================*/

/*==============================================JOB NAME======================================================*/
SELECT DISTINCT
      
      
        
          pj.name               
,pj.job_id
         
    FROM   per_all_people_f          papf,
           per_all_assignments_m     paam,
           per_person_names_f        ppnf,
           per_jobs                  pj,
           per_grades                pg,
           hr_locations              hl,
		   pay_pay_relationships_dn    rel
    WHERE  1=1
      AND  paam.person_id = papf.person_id
      AND  ppnf.person_id = papf.person_id
      AND  pj.job_id      = paam.job_id
      AND  pg.grade_id    = paam.grade_id
      AND  hl.location_id = paam.location_id
	      AND   rel.person_id                     = papf.person_id
      AND  TRUNC(SYSDATE) BETWEEN paam.effective_start_date AND paam.effective_end_date
      AND  TRUNC(SYSDATE) BETWEEN papf.effective_start_date AND papf.effective_end_date
      AND  TRUNC(SYSDATE) BETWEEN ppnf.effective_start_date AND ppnf.effective_end_date
      AND  TRUNC(SYSDATE)BETWEEN pj.effective_start_date   AND pj.effective_end_date
      AND  TRUNC(SYSDATE) BETWEEN pg.effective_start_date   AND pg.effective_end_date
      AND  paam.effective_latest_change = 'Y'
      AND  paam.assignment_type         = 'E'
      AND  paam.legislation_code        = 'IN'
      AND  paam.primary_flag            = 'Y'
      AND  paam.assignment_status_type  = 'ACTIVE'
      AND  ppnf.name_type               = 'GLOBAL'
      AND  ppnf.legislation_code        = 'IN'
      AND  hl.country                   = 'IN'
/*========================================================================================================================*/
