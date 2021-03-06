| 列                       | 类型                              | 注释 |
| :----------------------- | --------------------------------- | ---- |
| id                       | int [**0**]                       |      |
| name                     | varchar(255) *NULL* []            |      |
| org_id                   | int *NULL* [**0**]                |      |
| organization_name        | varchar(255) *NULL* []            |      |
| usage_limit              | varchar(255) *NULL* []            |      |
| description              | text *NULL*                       |      |
| start_date               | date *NULL*                       |      |
| end_date                 | date *NULL*                       |      |
| licence_key              | varchar(255) *NULL* []            |      |
| perpetual                | enum('no','yes') *NULL* [**no**]  |      |
| software_id              | int *NULL* [**0**]                |      |
| software_name            | varchar(255) *NULL* []            |      |
| finalclass               | varchar(255) *NULL* [**Licence**] |      |
| friendlyname             | varchar(255) *NULL*               |      |
| obsolescence_flag        | int [**0**]                       |      |
| obsolescence_date        | date *NULL*                       |      |
| org_id_friendlyname      | varchar(255) *NULL*               |      |
| org_id_obsolescence_flag | int [**0**]                       |      |
| software_id_friendlyname | varchar(511) *NULL*               |      |

```
SELECT DISTINCT
	`SoftwareLicence`.`id` AS `id`,
	`SoftwareLicence_Licence`.`name` AS `name`,
	`SoftwareLicence_Licence`.`org_id` AS `org_id`,
	`Organization_org_id`.`name` AS `organization_name`,
	`SoftwareLicence_Licence`.`usage_limit` AS `usage_limit`,
	`SoftwareLicence_Licence`.`description` AS `description`,
	`SoftwareLicence_Licence`.`start_date` AS `start_date`,
	`SoftwareLicence_Licence`.`end_date` AS `end_date`,
	`SoftwareLicence_Licence`.`licence_key` AS `licence_key`,
	`SoftwareLicence_Licence`.`perpetual` AS `perpetual`,
	`SoftwareLicence`.`software_id` AS `software_id`,
	`Software_software_id`.`name` AS `software_name`,
	`SoftwareLicence_Licence`.`finalclass` AS `finalclass`,
	cast( concat( COALESCE ( `SoftwareLicence_Licence`.`name`, '' )) AS CHAR charset utf8mb4 ) AS `friendlyname`,
	COALESCE (((
				`SoftwareLicence_Licence`.`perpetual` = 'no' 
				) 
			AND (( `SoftwareLicence_Licence`.`end_date` IS NULL ) = 0 ) 
			AND (
			`SoftwareLicence_Licence`.`end_date` < date_format(( now() - INTERVAL 15 MONTH ), '%Y-%m-%d 00:00:00' ))),
		0 
	) AS `obsolescence_flag`,
	`SoftwareLicence_Licence`.`obsolescence_date` AS `obsolescence_date`,
	cast( concat( COALESCE ( `Organization_org_id`.`name`, '' )) AS CHAR charset utf8mb4 ) AS `org_id_friendlyname`,
	COALESCE (( `Organization_org_id`.`status` = 'inactive' ), 0 ) AS `org_id_obsolescence_flag`,
	cast(
		concat(
			COALESCE ( `Software_software_id`.`name`, '' ),
			COALESCE ( ' ', '' ),
		COALESCE ( `Software_software_id`.`version`, '' )) AS CHAR charset utf8mb4 
	) AS `software_id_friendlyname` 
FROM
	((
			`softwarelicence` `SoftwareLicence`
			JOIN `software` `Software_software_id` ON ((
					`SoftwareLicence`.`software_id` = `Software_software_id`.`id` 
				)))
		JOIN (
			`licence` `SoftwareLicence_Licence`
			JOIN `organization` `Organization_org_id` ON ((
					`SoftwareLicence_Licence`.`org_id` = `Organization_org_id`.`id` 
					))) ON ((
				`SoftwareLicence`.`id` = `SoftwareLicence_Licence`.`id` 
			))) 
WHERE
	(
	0 <> 1)
```

