
Plug it in with a `data` transfer `USB cable` (not a charging one) `example : Flipper USB`


```bash
idevicepair pair
```

Then hit the `Trust` button on the `Unlocked iPhone` :

```bash
>  idevicepair pair  
SUCCESS: Paired with device 0000000-111111111
```

Then, gather basic data with `ideviceinfo` :

```bash
ideviceinfo
```

And more detailed with `pymobiledevice3 lockdown info` :

```bash
>  pymobiledevice3 lockdown info  
{  
   "ActivationState": "Activated",  
   "ActivationStateAcknowledged": true,  
   "BasebandActivationTicketVersion": "V2",  
   "BasebandCertId": 16******,  
   "BasebandChipID": 28*****,  
   "BasebandKeyHashInformation": {  
       "AKeyStatus": 2,  
       "SKeyHash": "<0000000000000000000000000000000000000000000000000000000000000000>",  
       "SKeyStatus": 0  
   },  
   "BasebandMasterKeyHash": "1B416076****************************CD57B4C466055803E1EF81C870",  
   "BasebandRegionSKU": "<00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000>",  
   "BasebandSerialNumber": "<7***a91>",  
   "BasebandStatus": "BBInfoAvailable",  
   "BasebandVersion": "2.40.01",  
   "BluetoothAddress": "e8:*********:90",  
   "BoardId": 10,  
   "BootSessionID": "496BA66*************D87",  
   "BrickState": false,  
   "BuildVersion": "23D127",  
   "CPUArchitecture": "arm64e",  
   "CarrierBundleInfoArray": [  
       {  
           "CFBundleIdentifier": "com.apple.Unknown",  
           "CFBundleVersion": "68.0",  
           "GID1": "ffffffff",  
           "GID2": "ffffffff",  
           "IntegratedCircuitCardIdentity": "8933380************",  
           "InternationalMobileSubscriberIdentity": "2083800*******",  
           "MCC": "208",  
           "MNC": "38",  
           "SIMGID1": "<ffffffff>",  
           "SIMGID2": "<ffffffff>",  
           "Slot": "kOne",  
           "kCTPostponementInfoAvailable": "SIMCarrierInfo"  
       }  
   ],  
   "CertID": 165********,  
   "ChipID": 33****,  
   "ChipSerialNo": "<7fb73a91>",  
   "DeviceClass": "iPhone",  
   "DeviceColor": "1",  
   "DeviceName": "XCOM-K",  
   "DieID": 2825**********,  
   "EthernetAddress": "e8:ff:f4:df:36:3e",  
   "FirmwareVersion": "iBoot-13822.82.4",  
   "FusingStatus": 3,  
   "GID1": "ffffffff",  
   "GID2": "ffffffff",  
   "HardwareModel": "D48AP",  
   "HardwarePlatform": "t8140",  
   "HasSiDP": true,  
   "HostAttached": true,  
   "HumanReadableProductVersionString": "26.3",  
   "IntegratedCircuitCardIdentity": "8933**********351",  
   "InternationalMobileEquipmentIdentity": "35153********59",  
   "InternationalMobileEquipmentIdentity2": "351********21",  
   "InternationalMobileSubscriberIdentity": "20*********9135",  
   "InternationalMobileSubscriberIdentityOverride": false,  
   "MLBSerialNumber": "HT3HM9002KD0000HBX",  
   "MobileSubscriberCountryCode": "208",  
   "MobileSubscriberNetworkCode": "38",  
   "ModelNumber": "MXWN3",  
   "NonVolatileRAM": {  
       "StartupMute": "<31>",  
       "SystemAudioVolumeExtension": "<2207>",  
       "alpm-24h-mode": "<31>",  
       "alpm-dst-offset": "<2*******030>",  
       "alpm-language-direction": "<31>",  
       "alpm-next-dst": "<31373*********0303030>",  
       "alpm-number-system": "<6c******6e>",  
       "auto-boot": "<74727565>",  
       "backlight-level": "<343536>",  
       "backlight-nits": "<30783030353030303030>",  
       "bootdelay": "<30>",  
       "fm-account-masked": "<72e280******************a2e280a2e280a22e636f6d>",  
       "fm-activation-locked": "<594553>",  
       "fm-spkeys": "<62706c****************************************************************************************************************** 
08287898*********00000000000001010000000000000015000000000000000000000000000000ba>",  
       "fm-spstatus": "<5***53>",  
       "obliteration": "<4541435320**************************9636557697065205b67463a20307830303030303030  
3030303030303030305d>",  
       "panicmedic-telemetry": "<1101000000000000d400fa3***********************000000000000000000000000000000000000000000000000000000000000  
00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000>",  
       "panicmedic-timestamps": "<303**********66335313463>",  
       "usbc,version,rid0": "<0**********d00>",  
       "utc-offset": "<373****30>"  
   },  
   "PRIVersion_Major": 0,  
   "PRIVersion_Minor": 1,  
   "PRIVersion_ReleaseNo": 163,  
   "PairRecordProtectionClass": 4,  
   "PartitionType": "GUID_partition_scheme",  
   "PasswordProtected": false,  
   "PhoneNumber": "07 ** ** ** 45",  
   "PkHash": "<627ac******************************************************************5>",  
   "ProductName": "iPhone OS",  
   "ProductType": "iPhone17,4",  
   "ProductVersion": "26.3",  
   "ProductionSOC": true,  
   "ProtocolVersion": "2",  
   "RegionInfo": "ZD/A",  
   "SIM1IsEmbedded": false,  
   "SIMGID1": "<ffffffff>",  
   "SIMGID2": "<ffffffff>",  
   "SIMStatus": "kCTSIMSupportSIMStatusReady",  
   "SIMTrayStatus": "kCTSIMSupportSIMTrayInsertedWithSIM",  
   "SerialNumber": "K******P7P",  
   "SoftwareBehavior": "<05000000000000000000000000000000>",  
   "SoftwareBundleVersion": "",  
   "SupportedDeviceFamilies": [  
       1  
   ],  
   "TelephonyCapability": true,  
   "TimeIntervalSince1970": 1781267207.8724,  
   "TimeZone": "Europe/Paris",  
   "TimeZoneOffsetFromUTC": 7200.0,  
   "TrustedHostAttached": true,  
   "UniqueChipID": 2825**********,  
   "UniqueDeviceID": "00008*************13801C",  
   "UntrustedHostBUID": "2402**************31DDF49931",  
   "UseRaptorCerts": true,  
   "Uses24HourClock": false,  
   "WiFiAddress": "e8**********40:2b",  
   "WirelessBoardSerialNumber": "33*********D",  
   "kCTPostponementInfoPRIVersion": "0.1.163",  
   "kCTPostponementInfoServiceProvisioningState": true,  
   "kCTPostponementStatus": "kCTPostponementStatusActivated"
```

To list the apps in the phone in a .json :

```bash
pymobiledevice3 apps list > apps_list.json
jq length apps_list.json
```

Once the .json is in, a clean view of the apps can be grepped with `jq` :

```bash
jq -r '.[].CFBundleIdentifier' apps_list.json | sort
	
jq -r '.[].CFBundleIdentifier' apps_list.json | grep -iE 'password|auth|bank|finance|crypto|wallet|messag|chat|mail|note|drive|dropbox|google|microsoft|corp|work|mdm'

jq -r '.[].CFBundleIdentifier' apps_list.json | grep -iE 'signal|whatsapp|telegram|1password|bitwarden|lastpass|authy|bank|revolut|crypto|slack|outlook|notion|obsidian'
```
