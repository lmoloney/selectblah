3.	Connect-AzAccount -ServicePrincipal -Credential -Tenant $TenantId 
4.	    Set-AzContext -SubscriptionId  $SubscriptionId
5.	
6.	    #Select-AzureSubscription -SubscriptionId $SubscriptionId 
7.	
8.	    #Get all SQL Datawarehouses in the subscription
9.	    $dws = Get-AzResource | Where-Object ResourceType -EQ "Microsoft.Sql/servers/databases" | Where-Object Kind -ILike "*datawarehouse*"
10.	
11.	   #Loop through each SQLDW
12.	   foreach($dw in $dws)
13.	   {
14.	       $rg = $dw.ResourceGroupName
15.	       $dwc = $dw.Name.split("/")
16.	       $sn = $dwc[0]
17.	       $db = $dwc[1]
18.	      # $status = Get-AzureSqlDatabase -ServerName $sn -DatabaseName $db -ConnectionContext $ctx  | Select Status
19.	    $status = Get-AzSqlDatabase -ResourceGroupName $rg -ServerName $sn -DatabaseName $db  | Select Status
20.	       
21.	       #$rg
22.	       #$sn
23.	       #$db
24.	       $status.Status
25.	       #Check the status
26.	        if($status.Status -ne "Paused" -and $sn -eq $SQLDWServer)
27.	       {   
28.	           #If the status is not equal to "Paused", pause the SQLDW
29.	           Suspend-AzSqlDatabase -ResourceGroupName $rg -ServerName $sn -DatabaseName $db
30.	      }    
31.	    }
