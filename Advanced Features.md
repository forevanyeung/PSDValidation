## Advanced Features
Let's say if our `appconfig.psd1` looked something more like this
```PowerShell
@{
    Name = "Create Your Own Pirate Adventure"
    Author = "Perry Parrot"
    Build = @{
        BuildFolder = ".\Build"
        ReleaseFolder = ".\Release"
        Tags = "high-seas"
    }
    Dependencies = @(
        @{
            name = "ships"
            version = '2.0.1'
        },
        @{
            name = "cannons"
            version = '0.5.1'
        }
    )
}
```
and we wanted to make sure the Dependencies all follow the same hashtable structure. Here's how we can validate that

```PowerShell
Register-PSDValidation Dependencies `
    -TypeOf Array, Hashtable

Register-PSDValidationHashtable Dependencies -Strict {
    Register-PSDValidation Dependencies.name `
        -TypeOf String
    Register-PSDValidation Dependencies.version `
        -TypeOf Version
}
```

Let's break it down. First, we say Dependencies can only be an array of hashtables. Then we define the hashtable with `Register-PSDValidationHashtable` and we use the `-Strict` parameter here to tell it we want nothing else in the hashtable, it has to match this structure. Then inside 
