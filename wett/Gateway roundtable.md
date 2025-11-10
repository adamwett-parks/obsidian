In legacy, pages are very idiosyncratic, and it can be difficult to write code that can reused across pages. 
For example in the Preapprovals rewrite, we use a query like the following to get a user's (Joe Shimel's) pending approvals.

```sql
SELECT
	  report_date,
	  pa_number,
	  section,
	  district,
	  center_code,
	  center_description,
	  pay_center,
	  user_id,
	  system_entry_date,
	  purchase_description,
	  ncas_account,
	  account_description,
	  unit_quantity,
	  unit_cost,
	  sum(unit_quantity*unit_cost) as 'requested_amount',
	  purchase_type,
	  purchase_date,
	  justification,
	  center_approved,
	  district_approved,
	  disu_comments,
	  section_approved,
	  section_comments,
	  division_approved,
	  bo_comments,
	  osbm_approved,
	  re_number
FROM purchase_request_3
WHERE
	section_approved='u' AND
	division_approved='u' AND 
	(center_code IN ('ADMI', 'ADMN', 'ASRO', 'BOCR', 'CABE', 'CACR', 'CEDI', 'CENT', 'CHRO', 'CLNE', 'COMM', 'CONC', 'CRMO', 'DERI', 'DIRE', 'DISW', 'EDGS', 'ELKN', 'ENRI', 'FALA', 'FITE', 'FOFI', 'FOMA', 'FULA', 'GISP', 'GLSG', 'GOCR', 'GORG', 'GRAN', 'GRMO', 'GRST', 'HABE', 'HARI', 'HARO', 'INTE', 'ITOP', 'ITPR', 'JONE', 'JORD', 'JORI', 'KELA', 'LAHY', 'LAJA', 'LANO', 'LAPP', 'LAWA', 'LOHA', 'LURI', 'MARI', 'MEMI', 'MEMO', 'MOJE', 'MOMI', 'MOMO', 'MOTS', 'NARA', 'NDTO', 'NEDI', 'NERI', 'NWDI', 'OPRA', 'PACR', 'PAPL', 'PETT', 'PIMO', 'PIVI', 'PMET', 'PUIS', 'RARO', 'REEV', 'RELE', 'REMA', 'REMO', 'RERE', 'SACR', 'SAFE', 'SEDI', 'SILA', 'SOMO', 'SPHR', 'STMO', 'STPA', 'STWD', 'SUMT', 'SWDI', 'TRGR', 'VADE', 'VOLS', 'WAMI', 'WARE', 'WEBS', 'WEWO', 'WILD', 'WIUM', 'YEMO', 'dede', 'none', 'stwd', 'usfw') OR user_id = 'Shimel63')
GROUP BY pa_number
ORDER BY system_entry_date DESC
LIMIT 2500
```

It's generated with the following function:

```php
// arguments omitted & query shortened for brevity
function selectPreapprovals($connection, $params, $temp_id) {
	// example of using one of the optional params
	if (isset($params['entered_before'])) {
		$where .= " AND system_entry_date <= '" . $params['entered_before'] . "'";
	}
	
	// ... more optional params ....
	
	// temp_id is required
	$highest = getMyHighestApprovalLevel($temp_id) . "_approved";
	$where = $where . " AND $highest = 'u'";
	
	$sql = "SELECT
				...
			WHERE $where"
	$result = mysqli_query($connection, $sql);
	
	$arr = [];
	while ($row = mysqli_fetch_assoc($result)) {
		$arr[] = $row;
	}
	return $arr;
}
```

For Preapprovals, Zelda wrote a component that displays the amount of pending approvals a user has:
![component.png]()
