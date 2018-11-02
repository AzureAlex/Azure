## Settings
# Path
$ScriptPath = "C:\Temp\AzureScript\"
$TenantName = "tenant"
$UserName = "xxx@domain.com"

#Import csv file and get row 1 data
$information = import-csv "$ScripPath\automaatio.csv" -Header Rg, Tag, Group
$data = $information | Select-object -first 1 
 
#Save row 1 data to variables
$RgName = $data.Rg
$Tag = $data.Tag
$Group = $data.Group

# Delete used row
set-content "C:\Temp\AzureScript\automaatio.csv" (get-content "C:\Temp\AzureScript\automaatio.csv" | select-object -skip 1)

# Connect to Azure AD
# Check if the credential files has been created
if(Test-Path -Path "$ScriptPath\$TenantName.cred") {
    $SecureLogin = "1"
}
else {
    $Credentials = Get-Credential
    $Credentials.Password | ConvertFrom-SecureString | Out-File "$ScriptPath\$TenantName.cred" -Force
    $SecureLogin = "1"
}
if($SecureLogin -eq "1") {
    $PasswordSecureString = Get-Content "$ScriptPath\$TenantName.cred" | ConvertTo-SecureString 
    $Credential = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $PasswordSecureString
    # Connect to AzureAD
    Connect-AzureAD -Credential $Credential

    # Create Resource group with tags
    New-AzureRmResourceGroup –Name $RgName –Location "West Europe"  -Tags $Tag | out-null


}

#Get the object ID of the group using the Get-AzureADGroup command and Save the group object ID in a variable.
$GroupId = (Get-AzureADGroup -SearchString "$Group").ObjectId

#Get the ID of your subscription using the Get-AzureRmSubscription command and save the id in a variable.
$Subscriptionid = (Get-AzureRmSubscription).ID

#Assign the Reader role to the group at the subscription scope.
New-AzureRmRoleAssignment -ObjectId $GroupId -RoleDefinitionName "Reader" -Scope $Subscriptionid

#Assign the Contributor role to the group at the resource group scope.
New-AzureRmRoleAssignment -ObjectId $GroupId -RoleDefinitionName "Contributor"  -ResourceGroupName "$RgName"
