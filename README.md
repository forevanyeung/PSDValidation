# PSDValidation
Write validation for PowerShell data files or hashtables.

Powershell data files are just hashtables and when used with `Import-PowershellDataFile` (available since PS 5+) cannot allow outside code to run. These are natively supported in Powershell and support commenting, so no external libraries are needed. Which make them perfect to store configuration settings used in PowerShell scripts.



## Getting Started
In this example, we are validating a deployment template, `appconfig.psd1`. Here's something quick and dirty.
```PowerShell
@{
    Name = "Create Your Own Pirate Adventure"
    Author = "Perry Parrot"
    Build = @{
        BuildFolder = ".\Build"
        ReleaseFolder = ".\Release"
        Tags = "high-seas"
    }
}
```

First, create a new file called `Config.Validation.ps1` to contain all the validation rules. You can name this file whatever you feel like.

In this file, let's register the validations for each key in the hashtable. Note, this is one command and the backticks are for easier reading
```PowerShell
# Validate Authors, that it exists and it is either a string or an array of strings
Register-PSDValidation Authors `
    -Required `
    -TypeOf Array, String

# Same as
# Register-PSDValidation Authors -Required -TypeOf Array, String
```

you can also validate nested hashtables like this
```PowerShell
# Validate Build.ReleaseFolder is only a string
Register-PSDValidation  Build.ReleaseFolder`
    -TypeOf String
```

Now we're finally ready to run the validation tests on PowerShell Data File. Do so easily with the Invoke-PSDValidate command and the validation will return the number of passed, failed, skipped, and not validated keys. Passed and failed are self-explanatory. A validation is skipped when they key is not required and not present. NotValidated is when a key is present but there is no corresponding validation to match.
```PowerShell
Invoke-PSDValidate -InputObject appconfig.psd1 -Path Config.Validation.ps1

Passed: [2] Failed: [0] Skipped: [0] NotValidated: [3]
```

If we ran `Invoke-PSDValidate` with the `-Strict` parameter, then the three NotValidated keys would failed. 

There are many more ways to scale up and down the configuration validation. See the Advanced examples for validating parameter sets and arrays of hashtables. 

## Syntax
```PowerShell
Register-PSDValidation
    -Key <String[]>
    [-Required <Switch>]
    [-RequiredIf <String[]>]
    [-TypeOf <String[]>]
    [-OneOf <String[]>]
    [-Match <String>]
```

```PowerShell
-TypeOf
# Has to be one of these following types. Can specify multiple types. 
```

```PowerShell
Invoke-PSDValidate
    -Datafile
    -Validation
    -Quiet
```