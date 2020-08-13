# Common Tools Referenced

## sn.exe
Used for performing key operations for assemblies

### Usage
Creating a new public private key pair
```
sn -k myKeys.snk
```
Getting the public keys for an assembly
```
sn -Tp [assemblyLocation]
```

## gacutil
Used for performing operations with the Global Assembly Cache

### Usage
Listing assemblies currently in the GAC
```
gacutil -l
```
Installing an assembly in the GAC
```
gacuntil -i [assemblyLocation]
```
uninstalling an assembly from the GAC
```
gacutil -u [assembly]
```
