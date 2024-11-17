- Q2.1: 
```powershell
Start-Process powershell -ArgumentList "-NoProfile -ExecutionPolicy Bypass -File `"C:\Scripts\AddLocalUsers.ps1`"" -Verb RunAs
```
- Q2.2: Les 2 premières lignes du CSV sont ignorées (cf ligne 44). Il suffit de passer à 1 ligne et le premier utilisateur sera pris en compte:
```powershell
$Users = Import-Csv -Path $CsvFile -Delimiter ";" -Header "prenom","nom","societe","fonction","service","description","mail","mobile","scriptPath","telephoneNumber" -Encoding UTF8  | Select-Object -Skip 1
```
- Q2.3:  Il est écrit $**u**ser au lieu de $**U**ser (cf ligne 55). Modification:
```powershell
$Description = "$($User.description) - $($User.fonction)"
```
-  Q2.4: Il suffit de retirer la partie -Header (ligne 44):
```powershell
$Users = Import-Csv -Path $CsvFile -Delimiter ";" -Encoding UTF8  | Select-Object -Skip 1
```
- Q2.5: Ajout de l'affichage du mot de passe:
```powershell
Write-Host "L'utilisateur $Prenom.$Nom a été crée. Le mot de passe est $Pass ."
```
- Q2.6: Ajout d'appels à la fonction log:
```powershell

        Log -FilePath $LogFile -Content "Création du compte utilisateur : $Name"

        New-LocalUser @UserInfo
        Add-LocalGroupMember -Group "Utilisateur" -Member "$Prenom.$Nom"
        Write-Host "L'utilisateur $Prenom.$Nom a été crée. Le mot de passe est $Pass ."

        Log -FilePath $LogFile -Content "Le compte utilisateur $Prenom.$Nom a été créé avec succès."
```
- Q2.7: Ajout de la vérification de l'existence de l'utilisateur et modification du log:

```powershell

foreach ($User in $Users)
{
    $Prenom = ManageAccentsAndCapitalLetters -String $User.prenom
    $Nom = ManageAccentsAndCapitalLetters -String $User.Nom
    $Name = "$Prenom.$Nom"
    
    try
    {
        # Vérifie si le compte d'utilisateur existe déjà
        if (-not (Get-LocalUser -Name $Name -ErrorAction SilentlyContinue))
        {
            $Pass = Random-Password
            $Password = (ConvertTo-secureString $Pass -AsPlainText -Force)
            $Description = "$($User.description) - $($User.fonction)"
            $UserInfo = @{
                Name                 = "$Prenom.$Nom"
                FullName             = "$Prenom.$Nom"
                Password             = $Password
                AccountNeverExpires  = $true
                PasswordNeverExpires = $false
            }

            Log -FilePath $LogFile -Content "Création du compte utilisateur : $Prenom.$Nom"

            New-LocalUser @UserInfo
            Add-LocalGroupMember -Group "Utilisateur" -Member $Prenom.$Nom
            Write-Host "L'utilisateur $Prenom.$Nom a été crée. Le mot de passe est $Pass."

            Log -FilePath $LogFile -Content "Le compte utilisateur $Prenom.$Nom a été créé avec succès."
        }
        else
        {
            Log -FilePath $LogFile -Content "Le compte utilisateur $Prenom.$Nom existe déjà."
            Write-Host "L'utilisateur $Prenom.$Nom existe déjà."
        }
    }
    catch

    {

        Log -FilePath $LogFile -Content "Une erreur s'est produite lors de la création de l'utilisateur $Prenom.$Nom."

        Write-Host "Une erreur s'est produite lors de la création de l'utilisateur $Prenom.$Nom."

        Write-Host $_.Exception.Message

    }
}
```
- Q2.8: Correction de la ligne d'ajout dans le groupe, un S est manquant:
```powershell
Add-LocalGroupMember -Group "Utilisateurs" -Member $Prenom.$Nom
```
- Q2.9: Utilisation de la variable $Name:
```powershell
foreach ($User in $Users)
{
    $Prenom = ManageAccentsAndCapitalLetters -String $User.prenom
    $Nom = ManageAccentsAndCapitalLetters -String $User.Nom
    $Name = "$Prenom.$Nom"
    
    try
    {
        # Vérifie si le compte d'utilisateur existe déjà
        if (-not (Get-LocalUser -Name $Name -ErrorAction SilentlyContinue))
        {
            $Pass = Random-Password
            $Password = (ConvertTo-secureString $Pass -AsPlainText -Force)
            $Description = "$($User.description) - $($User.fonction)"
            $UserInfo = @{
                Name                 = $Name
                FullName             = $Name
                Password             = $Password
                AccountNeverExpires  = $true
                PasswordNeverExpires = $false
            }

            Log -FilePath $LogFile -Content "Création du compte utilisateur : $Name"

            New-LocalUser @UserInfo
            Add-LocalGroupMember -Group "Utilisateurs" -Member $Name
            Write-Host "L'utilisateur $Name a été crée. Le mot de passe est $Pass."

            # Journalise l'activité du script
            Log -FilePath $LogFile -Content "Le compte utilisateur $Name a été créé avec succès."
        }
        else
        {
            Log -FilePath $LogFile -Content "Le compte utilisateur $Name existe déjà."
            Write-Host "L'utilisateur $Name existe déjà."
        }
    }
}

```
- Q2.10: Modification pour que le mot de passe n'expire pas:
```powershell
PasswordNeverExpires = $true
```
- Q2.11: Modification du nombre de caractères du mot de passe:
```powershell
Function Random-Password ($length = 10)
```

### Script final

```powershell
function Log
{
    param([string]$FilePath,[string]$Content)

    # Vérifie si le fichier existe, sinon le crée
    If (-not (Test-Path -Path $FilePath))
    {
        New-Item -ItemType File -Path $FilePath | Out-Null
    }

    # Construit la ligne de journal
    $Date = Get-Date -Format "dd/MM/yyyy-HH:mm:ss"
    $User = [System.Security.Principal.WindowsIdentity]::GetCurrent().Name
    $logLine = "$Date;$User;$Content"

    # Ajoute la ligne de journal au fichier
    Add-Content -Path $FilePath -Value $logLine
}

Function Random-Password ($length = 10)
{
    $punc = 46..46
    $digits = 48..57
    $letters = 65..90 + 97..122
    $password = get-random -count $length -input ($punc + $digits + $letters) |`
        ForEach -begin { $aa = $null } -process {$aa += [char]$_} -end {$aa}
    Return $password.ToString()
}

Function ManageAccentsAndCapitalLetters
{
    param ([String]$String)
    
    $StringWithoutAccent = $String -replace '[éèêë]', 'e' -replace '[àâä]', 'a' -replace '[îï]', 'i' -replace '[ôö]', 'o' -replace '[ùûü]', 'u'
    $StringWithoutAccentAndCapitalLetters = $StringWithoutAccent.ToLower()
    $StringWithoutAccentAndCapitalLetters
}

$Path = "C:\Scripts"
$CsvFile = "$Path\Users.csv"
$LogFile = "$Path\Log.log"

$Users = Import-Csv -Path $CsvFile -Delimiter ";" -Encoding UTF8  | Select-Object -Skip 1

foreach ($User in $Users)
{
    $Prenom = ManageAccentsAndCapitalLetters -String $User.prenom
    $Nom = ManageAccentsAndCapitalLetters -String $User.Nom
    $Name = "$Prenom.$Nom"
    
    try
    {
        # Vérifie si le compte d'utilisateur existe déjà
        if (-not (Get-LocalUser -Name $Name -ErrorAction SilentlyContinue))
        {
            $Pass = Random-Password
            $Password = (ConvertTo-secureString $Pass -AsPlainText -Force)
            $Description = "$($User.description) - $($User.fonction)"
            $UserInfo = @{
                Name                 = $Name
                FullName             = $Name
                Password             = $Password
                AccountNeverExpires  = $true
                PasswordNeverExpires = $true
            }


            Log -FilePath $LogFile -Content "Création du compte utilisateur : $Name"

            New-LocalUser @UserInfo
            Add-LocalGroupMember -Group "Utilisateurs" -Member $Name
            Write-Host "L'utilisateur $Name a été crée. Le mot de passe est $Pass."


            Log -FilePath $LogFile -Content "Le compte utilisateur $Name a été créé avec succès."
        }
        else
        {

            Log -FilePath $LogFile -Content "Le compte utilisateur $Name existe déjà."
            Write-Host "L'utilisateur $Name existe déjà."
        }
    }
    catch
    {
        Log -FilePath $LogFile -Content "Une erreur s'est produite lors de la création de l'utilisateur $Name."
        Write-Host "Une erreur s'est produite lors de la création de l'utilisateur $Name."
        Write-Host $_.Exception.Message
    }
}

```
