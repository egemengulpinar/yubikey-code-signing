# yubikey-code-signing

This repo aiming to help that quite complicated digital code signing process.
## Requirements
This repo used Yubikey 5C FIPS device, please be sure that your own device support features below.

- <a href="https://www.yubico.com/support/download/smart-card-drivers-tools" target="_blank">YubiKey Smart Card Minidriver</a>
- <a href="https://www.yubico.com/support/download/smart-card-drivers-tools" target="_blank">YubiKey Manager</a>
- <a href="https://www.yubico.com/support/download/smart-card-drivers-tools" target="_blank">Yubico PIV Tool</a>  
- Windows SDK(Windows Kits/signtool.exe)

## Windows SDK - signtool.exe
To get that tool, please refer to <a href="https://developer.microsoft.com/en-us/windows/downloads/windows-sdk" target="_blank">Windows SDK</a> site, install relevant windows SDK version
Then you can find your signtool like below ```C:\Program Files (x86)\Windows Kits\10\bin\10.0.22621.0\x64```
For configuring your signtool.exe, please open 
- ⊞ Win + R
- enter ```sysdm.cpl```
- Click ```advanced``` tab and ```environment variables```
- Under ```system variables``` open ```Path``` then paste your signtool folder
- then finish and close all tabs open a new editor as administrator


*You must be sure, you need to generate ```.csr``` files before and send it to them to code signing provider. They will generate ```.crt``` files, download them and follow these lines below. If you don't know how to generate .csr files , follow providers documentation.*.


## Installation 

**Root Certificate :** my own certificate started like 1705XXXXXX.crt(includes your company information and timestamp )

**Intermediate Certificate :** my own certificate started like CodeSigning_CABundle.crt(includes code signing provider information)

After install all required programs, please follow the lines below.
1. Open **Yubikey Manager** and select tab(Authentication or Digital Signature that tab used for generate .csr files before)
Enter **Import** button and select your Root certificate.
If you not created ```Management Key``` select ```use default``` checkbox, or enter your own Management Key.
![yubikey](https://github.com/egemengulpinar/yubikey-code-signing/assets/71253469/e4aec946-0ff3-414e-887c-b9cf8b77575b)


2. Be sure that you get your certificates(.crt) from your digital code sign provider (ex: signmycode)
You must get 3 more certificates for importing to Yubikey. These called(maybe different for you)
- **AAACertificateServices.crt (ROOT)**
- **SectigoPublicCodeSigningCAEVE36.crt (INTERMEDIATE 1)**
- **SectigoPublicCodeSigningRootE46_AAA.crt (INTERMEDIATE 2)**

Open powershell as administrator, then

```cd "C:\Program Files\Yubico\YubiKey Manager"```

Enter the lines below **(give your own .crt paths)**
```
.\ykman.exe piv certificates import 82 "C:\Users\ege\AAACertificateServices.crt"  
.\ykman.exe piv certificates import 83 "C:\Users\ege\SectigoPublicCodeSigningCAEVE36.crt"
.\ykman.exe piv certificates import 84 "C:\Users\ege\SectigoPublicCodeSigningRootE46_AAA.crt"     
```

3. Get your ROOT_THUMBPRINT, double click your Root Certificate then click ```details``` tab, you can see below as ```Thumbprint``` 
Copy the Thumbprint value now that is your ROOT_THUMBPRINT. Now, follow the last step.

![thumbprint](https://github.com/egemengulpinar/yubikey-code-signing/assets/71253469/08e7dbaa-71a2-49a4-ada8-a73315f29560)

4. For sign your .exe applications, follow the lines
```
signtool sign /sha1 ROOT_THUMBPRINT /fd SHA256 /t PROVIDER_TIMESTAMP "YOUR .EXE FILE"
```
That's look like below:
```

signtool sign /sha1 XXXXXXXXXXXXXXXXXXXXXXX /fd SHA256 /t http://timestamp.sectigo.com "C:\Users\ege\MyTestApp.exe"
```
![code_signing](https://github.com/egemengulpinar/yubikey-code-signing/assets/71253469/5cc615f9-9e9c-48d4-bf64-eb3a81d418a4)
