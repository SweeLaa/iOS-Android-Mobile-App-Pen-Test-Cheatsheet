# iOS-Android-Mobile-App-Pen-Test-Cheatsheet

# Android

1. Unpack a backup file 
`java -jar abe.jar unpack backup.ab backup.tar`

2. Untar a tar file
`tar xf backup.tar -C android-backup/`

3. `file` command to identify file type

4. `strings` command to extract plaintext strings from binary files

5. `unzip -d myapp myApp.apk` unzips myApp.apk for analysis

6. `strings -eb app.dll` to extract big-endian unicode values (for apps developed in .NET framework)

7. `strings -el app.dll` to extract little-endian unicode strings

8. Generate signing keys for modified apk
```
mkdir keys
keytool -genkey -v -keystore keys/isItDown.keystore -alias isItDown -keyalg RSA -validity 10000
```

9. Once keystore generated, sign the APK file using jarsigner
```
jarsigner -verbose -keystore keys/isItDown.keystore isItDown_modified.apk isItDown

```

10. Once apk is jarsigned, we need to zipalign
```
zipalign -v 4 isItDown_modified.apk isItDown_aligned.apk
```

 
## Android App Static Analysis

1. `$ jadx-gui` - Examine APK files

2. Inspect `AndroidManifest.xml` found in `Resources` folder
	- Look out for what is exported `android:exported="true"`
	- Look for exposed activity through intent filters

> Remember: For app link to work, there needs to be an assetlinks.json file at https://<app-link>/.well-known/assetlinks.json

### Deobfuscating Using Simplify.jar
```
java -jar simplify.jar -it 'org/sec575/securenotepad/d;->' SecureNotePad.apk
```
- `org/sec575/securenotepad/` is the package folder 
- `d` is the class to deobfuscate


### Monodevelop
Can decompile DLL files

# Dynamic Static Analysis

## Frida Android (Using Objection)

```
# Use bytecodeviewer to examing apk
> bytecodeviewer
```

1. `objection -g org.sec575.securenotes explore` 

2. `android root disable` 

3. `android hooking search classes vault`

4. Write injection script in Javascript
```
# Example: disable_root.js

var vault = Java.use("org.sec575.securenotes.Vault");
vault.getPassword.implementation = function()
{
    var password = this.getPassword();
    console.log("Password: " + password);
    return password;
}

```
5. `frida -U -l disable_root -f org.sec575.securenotes` to inject the script via frida 

