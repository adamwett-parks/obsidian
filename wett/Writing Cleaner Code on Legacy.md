In this tutorial we will be refactoring `fire/burn_history.php`. I'm using an actual file from legacy so the ideas I describe stand out more and can map directly to a real example instead of an abstracted one. I chose `burn_history.php` in particular 

Here's what the file looks like at the start of the process.

```php
<?php
//added global functions that are useful in every php file
include_once($_SERVER['DOCUMENT_ROOT'].'/php_functions/globalFunctions.php');
//PA-537 log the file, user, and parent file
logFileAccess(__FILE__);
include("menu.php");

extract($_REQUEST);
if(empty($park_code) AND empty($unit_id)){exit;}

$table_1="burn_history";
$table_2="units";

include("../../include/iConnect.inc");
include("../../include/get_parkcodes_dist.php");
mysqli_select_db($connection,'fire');

if(!empty($history_id) AND @$del=="delete")
	{
	$sql="Delete
	from $table_1
	where history_id='$history_id'
	";  //echo "$sql"; exit;
	$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql");
	}

$sql="SELECT unit_id,unit_name 
from $table_2 
where park_code='$park_code'
order by unit_name";
$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql ");
while($row=mysqli_fetch_assoc($result))
	{
	$unit_name=$row['unit_name'];
	if($unit_name=="An unnamed unit")
		{
		$unit_name="(This unit has NOT been named.)";
		$message_1="Assign a name to an unnamed unit by selecting \"Burn Units\" in the first drop-down menu.";
		}
	$ARRAY_unit[$row['unit_id']]=$unit_name;
	}
if(empty($ARRAY_unit))
	{
	echo "<font color='red'>A Burn Unit must first be entered BEFORE a Burn History can be started.</font>"; exit;
	}
//echo "<pre>"; print_r($ARRAY_unit); echo "</pre>";

if(!empty($unit_id))
	{
	$sql="SELECT * 
	from prescriptions
	where park_code='$park_code' and unit_id='$unit_id'
	order by id";
	$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql ");
	while($row=mysqli_fetch_assoc($result))
		{
		$ARRAY_prescription[$row['id']]=$row['unit_prescription'];
		$ARRAY_prescription_name[$row['id']]=$row['file_name'];
		}
//	echo "<pre>"; print_r($ARRAY_prescription_name); echo "</pre>"; // exit;
	}
$file="burn_history.php";
	
if(!empty($ARRAY_unit))
	{
	echo "<table><tr>";
		echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\">
		<option selected=''>Select a Unit:</option>";
	foreach($ARRAY_unit as $k=>$v)
		{
		if(@$unit_id==$k)
			{
			$s="value";
			$pass_unit_name="$v";
			}
		else
			{$s="value";}
		echo "<option $s='$file?park_code=$park_code&unit_id=$k'>$v</option>";
		}
		echo "</select>";
		if(!empty($message_1)){echo "<br />$message_1";}
	echo "</form></td>";
	
	}

if(empty($_REQUEST['unit_id'])){exit;}


$sql="DELETE FROM burn_history where date_='0000-00-00'";  //echo "$sql";
$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql");

$sql="SELECT t1.*, t2.unit_name
from $table_1 as t1
LEFT JOIN $table_2 as t2 on t2.unit_id=t1.unit_id
where t2.unit_id='$unit_id'";  //echo "$sql";
$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql");
while($row=mysqli_fetch_assoc($result))
	{
	$ARRAY_history[]=$row;
	}

if(empty($ARRAY_history) OR @$submit=="Add")
	{
	$sql="INSERT IGNORE INTO $table_1 set park_code='$park_code', unit_id='$unit_id'";
	$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql ");
//	$new_id=mysqli_insert_id();
	
	$sql="SELECT history_id
	from $table_1 where park_code='$park_code' and unit_id='$unit_id' and date_='0000-00-00'";
	$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql ");
	$row=mysqli_fetch_assoc($result);
		extract($row);
	}

echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\"><option selected=''>Select a History for a $pass_unit_name:</option>";
	foreach($ARRAY_history as $index=>$array)
		{
		$u=$array['unit_id'];
		$k=$array['history_id'];
		$v=$array['date_'];
		echo "<option value='$file?park_code=$park_code&unit_id=$u&history_id=$k'>$v</option>";
		}
		echo "</select></form>";
	echo "</td>";

	
if(!empty($ARRAY_history))
	{
	echo "<td>&nbsp;&nbsp;&nbsp;<a href='burn_history.php?submit=Add&park_code=$park_code&unit_id=$unit_id'>Add</a> a Burn History for $pass_unit_name at $park_code</td>";
	
	}
echo "</tr></table>";

if(empty($history_id)){exit;}

unset($ARRAY);
$sql="SELECT t1.*, group_concat(concat(t2.burn_history_file_upload_id,'*',t2.evaluation)) as evaluation, group_concat(concat(t2.burn_history_file_upload_id,'*',t2.original_name)) as original_name
from $table_1 as t1
LEFT JOIN burn_history_uploads as t2 on t1.history_id=t2.history_id and t1.unit_id=t2.unit_id
where t1.history_id='$history_id'
group by t1.history_id";
$result = mysqli_query($connection,$sql) or die ("Couldn't execute select query. $sql");
while($row=mysqli_fetch_assoc($result))
	{
	$ARRAY[]=$row;
	}
//echo "$sql<pre>"; print_r($ARRAY); echo "</pre>"; // exit;
//echo "<pre>"; print_r($_REQUEST); echo "</pre>";  exit;

$sql="SELECT t3.burn_boss, t3.dpr_name, t3.non_dpr_name
from burn_history as t1
LEFT JOIN units as t2 on t1.unit_id=t2.unit_id
LEFT JOIN participants as t3 on t1.history_id=t3.history_id
where t1.history_id='$history_id'
";
$result=mysqli_query($connection,$sql) or die ("Couldn't execute query. $sql ".mysqli_error($connection));
while($row=mysqli_fetch_assoc($result))
	{
	$participant_ARRAY[]=$row;
	}
// echo "$sql<pre>"; print_r($participant_ARRAY); echo "</pre>"; // exit;
echo "<hr />";

echo "<form method='post' action='burn_history_upload.php' enctype='multipart/form-data'>";

echo "<table align='center'>";

$d=$ARRAY[0]['date_'];
echo "<tr><th colspan='2'><font color='brown' size='+1'>Burn History $d for Unit $pass_unit_name at $parkCodeName[$park_code]</font></th></tr>";

$skip=array("id","park_code","history_id","unit_id","original_name");
$rename=array("unit_name"=>"Unit Name and/or ID", "date_"=>"Date of Burn","acres_burned"=>"Acres Burned","unit_history_prescription"=>"Rx used on this fire,<br />may be used multiple times.","evaluation"=>"Evaluation of Burn","comments"=>"Comments","gis_done"=>"GIS entry completed","burn_type"=>"Type of Burn","latitude"=>"Latitude","longitude"=>"Longitude");

//echo "<pre>"; print_r($ARRAY); echo "</pre>";

$input_size=array("unit_name"=>"50", "acres_burned"=>"5");
foreach($ARRAY as $index=>$array)
	{
	foreach($array as $fld=>$value)
		{
		if(in_array($fld,$skip)){continue;}
		echo "<tr><td>$rename[$fld]</td>";
		if(array_key_exists($fld,$input_size))
			{$size="size='".$input_size[$fld]."'";}else{$size="";}
		$input="<input type='text' name='$fld' value=\"$value\" $size>";
		if($fld=="comments")
			{
			echo "<td><textarea name='$fld' rows='12' cols='75'>$value</textarea></td></tr>";
			continue;
			}
		if($fld=="burn_type")
			{
			if($value=="prescribed" or empty($value))
				{$ckp="checked"; $ckw="";}else{$ckp=""; $ckw="checked";}
			$input="<input type='radio' name='$fld' value='prescribed' $ckp>Prescribed";
			$input.="<input type='radio' name='$fld' value='wild' $ckw>Wild";
			}
		if($fld=="gis_done")
			{
			if(!empty($value)){$ck="checked";}else{$ck="";}
			$input="<input type='checkbox' name='$fld' value='x' $ck>";
			}
		if($fld=="date_")
			{
			$input="<img src=\"../../jscalendar/img.gif\" id=\"f_trigger_c\" style=\"cursor: pointer; border: 1px solid red;\" title=\"Date selector\"
      			onmouseover=\"this.style.background='red';\" onmouseout=\"this.style.background=''\" />&nbsp;<input type='text' name='date_' value='$value' size='12' id=\"f_date_c\" READONLY> yyyy-mm-dd";
			}
		if($fld=="unit_history_prescription")
			{
			echo "<td><select name='unit_history_prescription'><option selected=''></option>\n";
			foreach($ARRAY_prescription_name as $k=>$v)
				{
				echo "<option value='$k'>$v</option>\n";
				}
			echo "</select>";
			if(empty($ARRAY_prescription_name)){echo " No prescription. Add one using \"Burn Units & Prescriptions\" from first drop-down menu.";}
		
			if(!empty($value))
				{
				@$name=$ARRAY_prescription_name[$value];
				@$link=$ARRAY_prescription[$value];
				echo "&nbsp;&nbsp;&nbsp;View Prescription: <a href='/fire/$link' target='_blank'>$name</a>
				<input type='hidden' name='unit_history_prescription' value='$k'></td>";
				}
			echo "</td>";
			continue;
			}
		if($fld=="evaluation")
			{
			$input="Select your files to add (can include burn maps, evaluation forms).<br />
			<input type='file' name='file_upload_evaluation'  size='40'>";
			if(!empty($value))
				{
				$exp=explode(",",$value);
				$exp2=explode(",",$ARRAY[$index]['original_name']);
				foreach($exp as $k=>$v)
					{
					$exp1=explode("*",$v);
					$file_id=$exp1[0];
					$file=$exp1[1];
					$del_link="burn_history_upload.php?delete=$file_id";
					
					$exp3=explode("*",$exp2[$k]);
					$original_file=$exp3[1];
					$input.="<br />View Evaluation: <a href='$file' target='_blank'>file</a> ==> <b>$original_file</b>";
					if($level>3)
						{
						$input.=" ===> <a href='$del_link' onclick=\"return confirm('Are you sure you want to delete this File?')\">delete</a>";
						}
					}
			//	$evaluation=$value;
				}
			}
		echo "<td>$input</td>
		</tr>";
		}
	}

$unit_id=$ARRAY[0]['unit_id'];

if(!empty($unit_map))
	{
	echo "<input type='hidden' name='unit_map' value='$unit_map'>";
	}
echo "<tr><td colspan='2' align='center'>
<input type='hidden' name='form_name' value='burn_history'>
<input type='hidden' name='history_id' value='$history_id'>
<input type='hidden' name='unit_id' value='$unit_id'>
<input type='hidden' name='park_code' value='$park_code'>
<input type='submit' name='submit' value='Update'>
</td></tr>";

echo "</form>";

echo "<tr><td></td><td align='right'>Enter <a href='participants.php?park_code=$park_code&unit_id=$unit_id&history_id=$history_id'>Participants</a>";
if(!empty($participant_ARRAY[0]['burn_boss']))
	{
	$bb=$participant_ARRAY[0]['burn_boss'];
	echo "<br /><br /><b>Burn Boss: $bb</b><br />";
	foreach($participant_ARRAY as $index=>$array)
		{
		foreach($array as $k=>$v)
			{
			if($k=="burn_boss"){continue;}
			if($v==""){continue;}
			echo "$v<br />";
			}
		
		}
	}
echo "</td></tr>";

if($level>3)
	{
	
echo "<tr><td><a href='burn_history.php?park_code=$park_code&history_id=$history_id&del=delete' onclick=\"return confirm('Are you sure you want this Burn History?')\">Delete</a></td></tr>";
	}

echo "</table>";

echo "</div></div>";
echo "
<script type=\"text/javascript\">
    Calendar.setup({
        inputField     :    \"f_date_c\",     // id of the input field
        ifFormat       :    \"%Y-%m-%d\",      // format of the input field
        button         :    \"f_trigger_c\",  // trigger for the calendar (button ID)
        align          :    \"Tl\",           // alignment (defaults to \"Bl\")
        singleClick    :    true
	    });
	</script>
</html>";
?>
```

## 1 - Formatting

Before we make any code changes, let's format the file correctly with our IDE and make a commit for that. This way it will be easier to read and make it clear what changes we've made that aren't just automatic formatting changes. To format, I have the Intellephense extension. If you choose to use a PHP extension like Intellephense, you can usually set a target PHP version. Since we are running 5.6, this is useful to warn us when we newer features like `??` or `str_contains` that are incompatible with our production environment.

If you aren't sure how to set up a formatter or a PHP extension, that is not a requirement for following along in this tutorial, but I highly recommend you use these tools when working in legacy as they really do make your life easier.

## 2 - Addressing Missing Variables

Let's define a new term: "missing variables"

> **"Missing variable"**
> 
> A *missing variable* is a variable used in a file that is not explicitly defined. In most cases they are either:
> - defined in another file and brought into scope with `include`
> - defined in an `extract` statement

Our first goal will be to eliminate all missing variables, that is, explicit defining them so our IDE is aware of where they come from. Intellisense is your friend.

Most of the missing variables will originate at the top of the file.

The first chunk to address is this:

```php
<?php
//added global functions that are useful in every php file
include_once($_SERVER['DOCUMENT_ROOT'].'/php_functions/globalFunctions.php');
//PA-537 log the file, user, and parent file
logFileAccess(__FILE__);

include("menu.php");

extract($_REQUEST);
if(empty($park_code) AND empty($unit_id)){exit;}
```

Since the include to `globalFunctions` and the call to `logFileAccess`  don't define any variables, they are of little interest to us.

Our first two missing variables are `$park_code` and `$unit_id`. Logically, if they are not defined in `menu.php`, the are set via `extract($_REQUEST)`. A quick `Ctrl+F` search in `menu.php` shows that they aren't set here. `menu.php` includes `_base_top_fire.php` and they aren't set here either, so most likely they are extracted from the request.

In this case, the chain of `include` statements was short and easy to follow. This might not always be the case. We can write a simple test to identify exactly where they are coming from.

```php
// see if the missing variables exist before extracting the request
include("menu.php");
$menu_park_code = $park_code;
$menu_unit_id = $unit_id;
// log to the browser console
\Utils\Logging::consolejson(["menu", $menu_park_code, $menu_unit_id]);

// see if these come from the request
extract($_REQUEST);
$req_park_code = $_REQUEST['park_code'];
$req_unit_id = $_REQUEST['unit_id'];
// log to the browser console
\Utils\Logging::consolejson(["request", $req_park_code, $req_unit_id]);

if(empty($park_code) AND empty($unit_id)){exit;}
```

Now navigate to the page via the GUI flow, the same way a user logging into the application would. Looking at the console we see:

```json
[
	"menu"
    "",
    null
]
[
	"request"
    null,
    null
]
```

This doesn't look very helpful at first. But there's probably a reason that `exit` statement is there. There's two dropdowns on the page right now. One says "Select a Park" and the other says "Select a Unit"

Selecting a park reloads the page. After we have selected a park our console reads:

```json
[ 
	"menu",
	"CRMO", 
	null
]
[
	"request",
    "CRMO", 
    null
]
```

`$park_code` is defined in the request, but also in `menu.php`. However we will pick the request as our *single source of truth* since our extract statement comes after our include. No matter what `menu.php` says `$park_code` is, if our request contains `park_code` then it will override it.

Great! Let's define `$park_code` right below the extract.

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
```

We need that `isset` because when we visited the page at first, that request param was not set. It's good practice to do this and will make it clear that we are handling the case if this parameter is set or not.

Now that our variable is defined, we can right click it and select "Find all References" from the context menu. This should open a buffer that shows everywhere in this file that this variable is used. If we are ever unsure if a variable has been defined or not, we can right click it and select "Find Definition" to bring us to where we defined it.

"Find Definition" won't work as intended right now, since there are statements that redefine it later in the file, but it will serve useful once we clean those up, and help us find the places where we are redefining it.

Next, let's open the second dropdown and select a unit. I'm going to pick "Central Unit". The page refreshes, and now our console reads:

```json
[
	"menu",
	"CRMO", 
	"95"
]
[
	"request",
    "CRMO", 
    "95"
]
```

Deducting the same way as before, we know that `$unit_id` is set in the request. Let's define it & get rid of our logging statements to clean up the console. We'll also add a comment above this section of the code

```php
include("menu.php");

// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;

if(empty($park_code) AND empty($unit_id)){exit;}

$table_1 = "burn_history";
$table_2 = "units";
```

Refreshing the page and playing around with the forms gives us the same behavior as before, so everything's peachy.

A few lines down we see another missing variable `history_id` lets `Ctrl+F` for `history_id=` on the page and see if we can locate where it might be defined.

```php
foreach ($ARRAY_history as $index => $array) {
    $u = $array['unit_id'];
    $k = $array['history_id'];
    $v = $array['date_'];
    echo "<option value='$file?park_code=$park_code&unit_id=$u&history_id=$k'>$v</option>";
}
```

Gotcha! `$history_id` is set via a form in this file, so it will be a request parameter. Let's define it:

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;
$history_id = isset($_REQUEST['history_id']) ? $_REQUEST['history_id'] : null;
```

Moving on to `$del`:

```php
if (!empty($history_id) and @$del == "delete") {

// Ctrl+F `del=`

echo "<tr><td><a href='burn_history.php?park_code=$park_code&history_id=$history_id&del=delete' onclick=\"return confirm('Are you sure you want this Burn History?')\">Delete</a></td></tr>";
```

Add it to the top:

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;
$history_id = isset($_REQUEST['history_id']) ? $_REQUEST['history_id'] : null;
$del = isset($_REQUEST['del']) ? $_REQUEST['del'] : null;
```

Instead of manually digging thru the file, let's `Ctrl+F` for `href=` and look for request params that we haven't explicitly defined yet.

```php
// Ctrl+F `href=`

// #1
href='burn_history.php?submit=Add&park_code=$park_code&unit_id=$unit_id'
// #2
href='/fire/$link'
// #3
href='$file'
// #4
href='$del_link'
// #5
href='participants.php?park_code=$park_code&unit_id=$unit_id&history_id=$history_id'
// #6
href='burn_history.php?park_code=$park_code&history_id=$history_id&del=delete' 
```

Let's tackle the easy ones first. In #1 we have `$submit`, #5 goes to a different page, so we don't care about it, and #6 doesn't use any variables we haven't explicitly defined. Let's define `$submit` with the rest of the parameters.

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;
$history_id = isset($_REQUEST['history_id']) ? $_REQUEST['history_id'] : null;
$del = isset($_REQUEST['del']) ? $_REQUEST['del'] : null;
$submit = isset($_REQUEST['submit']) ? $_REQUEST['submit'] : null;
```

And I'm going to update our list for clarity.

```php
// Ctrl+F `href=`

// #2
href='/fire/$link'
// #3
href='$file'
// #4
href='$del_link' 
```

Looking at #2

```php
foreach ($ARRAY as $index => $array) {
    foreach ($array as $fld => $value) {
		// ...
		@$link = $ARRAY_prescription[$value];
		echo "&nbsp;&nbsp;&nbsp;View Prescription: <a href='/fire/$link' target='_blank'>$name</a><input type='hidden' name='unit_history_prescription' value='$k'></td>";
		// ...
	}
}
```

We need to be careful here. Your first instinct might to be to look into how `$ARRAY_prescription` is defined. This is a bad idea, because it will send you down a rabbit hole and increase your scope. Our target now is looking for parameters that are reused across the page. This is only used in one place, and we know that because it's accessing an array value inside of a nested `foreach` loop. It also might link to a completely different page. We can ignore it for now.

#3 and #4 live a little further down

```php
foreach ($exp as $k => $v) {
	$exp1 = explode("*", $v);
	$file_id = $exp1[0];
	$file = $exp1[1];
	$del_link = "burn_history_upload.php?delete=$file_id";

	$exp3 = explode("*", $exp2[$k]);
	$original_file = $exp3[1];
	$input .= "<br />View Evaluation: <a href='$file' target='_blank'>file</a> ==> <b>$original_file</b>";
	if ($level > 3) {
		$input .= " ===> <a href='$del_link' onclick=\"return confirm('Are you sure you want to delete this File?')\">delete</a>";
	}
}
```

Again, let's not mess with `$file` since it's not guaranteed to link to the page we are working on. And looking at `$del_link` we see it points to a different file, so we don't care about defining these on this page.

Alright, so we've exhausted all our options for `href=`. Now let's look at another place request params can be set: `<form>`.  There are 3 forms on this page.

```php
// Ctrl+F `<form`

// form #1
echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\"><option selected=''>Select a Unit:</option>";
// form #2
echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\"><option selected=''>Select a History for a $pass_unit_name:</option>";
// form #3
echo "<form method='post' action='burn_history_upload.php' enctype='multipart/form-data'>";
```

Since form #3 is linking to a different page, we have no use to define those params on this one. Let's look for occurrences of `name=` that aren't in form #3

```php
// Ctrl+F `name=`

// name #1
echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\"><option selected=''>Select a Unit:</option>";

// name #2
echo "<td><form><select name='file' onChange=\"MM_jumpMenu('parent',this,0)\"><option selected=''>Select a History for a $pass_unit_name:</option>";

// the rest are inside form #3, so we can ignore them
```

There's that pesky 'file' param again. Let's stay out of JavaScript land and just define file at the top to be safe. We should also add a log for it since we aren't sure exactly what it is.

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;
$history_id = isset($_REQUEST['history_id']) ? $_REQUEST['history_id'] : null;
$del = isset($_REQUEST['del']) ? $_REQUEST['del'] : null;
$submit = isset($_REQUEST['submit']) ? $_REQUEST['submit'] : null;
$file = isset($_REQUEST['file']) ? $_REQUEST['file'] : null;

\Utils\Logging::consolejson(["file" => $file]);
```

Another target for missing variables is `$_SESSION`, this page doesn't use any, but `menu.php` does. The process for this is even easier. Just `Ctrl+F` for `$_SESSION` and move all the accesses to the top. Make sure you do so safely!

```php
// Session Info
$level = isset($_SESSION['fire']['level']) ? $_SESSION['fire']['level'] : null;
$tempID = isset($_SESSION['fire']['tempID']) ? $_SESSION['fire']['tempID'] : null;
$emid = isset($_SESSION['fire']['emid']) ? $_SESSION['fire']['emid'] : null;

```

---

You might be asking: "OK, cool. But why? What does all this do for us?"

First and foremost, it lets us look in a single place and see all of the parameters and session variables that a file uses.

Additionally, lots of files check for missing values and might even set them in the same line. Remember the output from our first time we logged to the console?

```json
[
	"menu"
    "", // park_code
    null
]
[
	"request"
    null, // park_code
    null
]
```

Well inside of `menu.php` there is this line:

```php
if(empty($park_code)){$park_code="";}
```

Another example is this:

```php
if (!empty($unit_id)) {
```

This pattern is really common across the codebase. If we move all of these undefined checks to the top of the file we can avoid having to check for undefined errors by sing *falsy* value like `null`, `""`, or `0`. I prefer to use `null` because it means that there is no value. By dedicating a space for these undefined checks at the top of the file, we can just write:

```php
if ($unit_id) {
```


---

One last place we can check for missing variables is any places in the codebase that navigate to our file. This one can be pretty tricky if the file you're working on is referenced by a lot of other files, but in this case `burn_history.php` only appears 11 times in the codebase, and all of the parameters added are ones we've defined already.

Now we can be fairly confident that we have MOST of the missing variables explicitly defined. We should still keep the `extract` statement just in case we missed anything.

Here's our final result:

```php
// Request Parameters
extract($_REQUEST);
$park_code = isset($_REQUEST['park_code']) ? $_REQUEST['park_code'] : null;
$unit_id = isset($_REQUEST['unit_id']) ? $_REQUEST['unit_id'] : null;
$history_id = isset($_REQUEST['history_id']) ? $_REQUEST['history_id'] : null;
$del = isset($_REQUEST['del']) ? $_REQUEST['del'] : null;
$submit = isset($_REQUEST['submit']) ? $_REQUEST['submit'] : null;
$file = isset($_REQUEST['file']) ? $_REQUEST['file'] : null;

// Session Info
$level = isset($_SESSION['fire']['level']) ? $_SESSION['fire']['level'] : null;
$tempID = isset($_SESSION['fire']['tempID']) ? $_SESSION['fire']['tempID'] : null;
$emid = isset($_SESSION['fire']['emid']) ? $_SESSION['fire']['emid'] : null;

\Utils\Logging::consolejson(["file" => $file]);
```

## 3 - Structuring our file

Humans are wired for spatial awareness. Placing things in a consistent spot makes them easier to find. We can leverage our biology to make our code easier to read and maintain by structuring it deliberately at a high level.

This is a structure that I have found to be quite helpful. It doesn't have to be followed to a T for all pages, but it is loose enough so that you can probably adapt it in a way that makes sense for your page.

- Dependencies
	- include statements
	- request parameters
	- session variables
	- anything else that happens outside of the file that affects it
- State
	- database connection
	- gateway classes
	- semantic variables
- Access
	- Use state to determine if someone has access to the page or if the rest of the page should render. Common checks include:
		- is our `$level` high enough?
		- do we have the correct request params?
		- we might need to make a specific query, like checking if we are a Budget Officer or a DISU. is the result OK?
- Functions
	- Pieces of business logic
	- Small SQL queries that run before the main query
- Actions/Handlers
	- Functions to handle different actions the page can perform (usually one for each possible value of `$submit`)
	- Control flow to execute the correct action & maybe `die()` afterwards if it's a POST request.
- Main Query
	- Function to compose a `WHERE` clause
	- Function to execute a `SELECT` statement
	- Control flow to actually run the main SQL query for the page to use
- UI Components
	- Functions that render HTML, especially conditionally or injecting "props" into them
- HTML
	- The main body of the page, commonly a big table

I'm going to add single-line comments that span >120 characters to clearly separate the page into sections.

```php
// =============================== Dependencies ============================

// =============================== State ===================================

// =============================== Access ==================================

// etc.
```

Remember this code from earlier? Where do you think it should go? What else can we do to it to clean it up?

```php
if (empty($park_code) and empty($unit_id)) {
    exit;
}
```

Here's how I refactored it:

```php
// =============================== Access ==================================

// Only show the page if we've selected a park and a burn unit.
if (!$park_code and !$unit_id) exit();

// ...
```

1. I moved it to the access section. 
2. I removed the now-redundant `empty()` checks.
3. I added a comment explaining WHY that code is there, not just WHAT it does.
4. I used a one-line `if` statement. This is just aesthetic preference. Sometimes they're good, sometimes they're bad.

Let's move on to the next chunk of code:

```php
$table_1 = "burn_history";
$table_2 = "units";

include("../../include/iConnect.inc");
include("../../include/get_parkcodes_dist.php");
mysqli_select_db($connection, 'fire');
```

Here's how I refactored it:

```php
// =============================== Dependencies =====================

include("menu.php");
include("../../include/get_parkcodes_dist.php");
$connection = \Utils\SQL::getConnection('fire');


// =============================== State ============================

$table_1 = "burn_history";
$table_2 = "units";
```

Here's the definition for `\Utils\SQL::getConnection($db)`. In case you've never seen it before it returns a database connection and prevents any included variables from messing with `iConnect.inc`. We'll get back to how I'm able to use it in this file without directly referencing it with `include` but for know just know that the `include` for `globalFunctions.php` handles that for us.

```php
public static function getConnection($db)
{
	$database = $db; // iConnect uses $database and $db
	include $_SERVER['DOCUMENT_ROOT'] . '/../include/iConnect.inc';
	return $connection;
}
```

Moving on to the next chunk:

```php
if (!empty($history_id) and @$del == "delete") {
    $sql = "Delete
	from $table_1
	where history_id='$history_id'
	";  //echo "$sql"; exit;
    $result = mysqli_query($connection, $sql) or die("Couldn't execute select query. $sql");
}
```

What does this do, and why? When will it do it?

- What: deletes a row
- Why: the user asked to do so
- When: `$history_id` is set and `$del` is `"delete"`

This would be a good thing to move into the actions section. We will clean it up more later but for now we will just copy & paste it as is, indent it some, and add a comment.

> **Note:** cleaning something like this up isn't entirely necessary. It works as is, is decently clear what it does, and is easily modifiable. Cleaning it up will mostly make it look nicer. Just make sure you move it to the right spot & add a comment to make it easier to locate.

```php
// =============================== Actions ==================================

// Handle deleting a record
if ($history_id and $del == 'delete') {
	$sql = "DELETE 
			FROM $table1 
			WHERE history_id='$history_id'";
	$result = $connection->query($sql) or die("Couldn't execute query. $sql");
}

```

I'm not going to do into gross detail about every chunk I refactor, but for some I will highlight what I'm doing if it's novel.

For this chunk:

```php
if (!empty($unit_id)) {
    $sql = "SELECT *
	from prescriptions
	where park_code='$park_code' and unit_id='$unit_id'
	order by id";
    $result = mysqli_query($connection, $sql) or die("Couldn't execute select query. $sql ");
    while ($row = mysqli_fetch_assoc($result)) {
        $ARRAY_prescription[$row['id']] = $row['unit_prescription'];
        $ARRAY_prescription_name[$row['id']] = $row['file_name'];
    }
    //	echo "<pre>"; print_r($ARRAY_prescription_name); echo "</pre>"; // exit;
}
```

We turn it into this:

```php
// =============================== State ============================

$prescriptions = null;

// =============================== Functions ========================

/**
 * Gets all prescriptions
 */
function getPrescriptions($connection, $park_code, $unit_id) {
	$sql = "SELECT *
			FROM prescriptions
			WHERE park_code='$park_code' AND unit_id='$unit_id'
			ORDER BY id";
	$result = $connection->query($sql);
	return \Utils\SQL::fetchAll($result);
}

// Fetch prescription info
if ($park_code && $unit_id) {
	$prescriptions = getPrescriptions($connection, $park_code, $unit_id);
}

```

1. I moved the state into the state section
2. I turned the query into function
3. I moved the control flow outside of the function

`\Utils\SQL::fetchAll($result)` takes a result from a query and iterates on it, fetching all the rows. It throws an error if it fails. Note that the check for our params is redundant but will 

