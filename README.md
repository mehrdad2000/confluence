# confluence

## Get user login date (postgres)

```
WITH last_login_date AS
(SELECT user_id
      , to_timestamp(CAST(cua.attribute_value AS double precision)/1000) AS last_login
   FROM cwd_user_attribute cua
  WHERE cua.attribute_name = 'lastAuthenticated'
    AND to_timestamp(CAST(cua.attribute_value AS double precision)/1000) < (CURRENT_DATE))
SELECT c.user_name
     , li.successdate
     , g.group_name
  FROM cwd_user c
 INNER JOIN last_login_date l ON (c.id = l.user_id)
 INNER JOIN cwd_membership m  ON (c.id = m.child_user_id)
 INNER JOIN cwd_group g       ON (m.parent_id = g.id)
 INNER JOIN user_mapping um ON (c.user_name = um.username)
 INNER JOIN logininfo li ON (um.user_key = li.username)
 WHERE g.group_name LIKE '$GROUP_NAME%' ;
 ```
