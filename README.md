# snmp-fiberhome

This module communicates with Fiberhome OLTs using the SNMP protocol. The module is capable of managing the OLT, Slots, Cards, pon ports and ONUs by performing read and write functions directly in OLT.

## Summary
- Installation
- Tests
- Initial settings
- OLT functions
- Slot functions
- Card functions
- ONU functions
- License

## Installation

This is a [Node.js](https://nodejs.org/en/) module available through the [npm](https://www.npmjs.com/).

```bash
$ npm install --save snmp-fiberhome 
```

It is loaded using the require() function:

```js
const fh = require('snmp-fiberhome')
```

## Tests

| OLT       | Status     |
|-----------|------------|
| AN5516-01 | Tested     |
| AN5116    | Not Tested |

---

| ONU          | Tech | Status |
|--------------|------|--------|
| AN5506-01-A1 | GPON | Tested |
| AN5506-04-F1 | GPON | Tested |

## Initial settings

```js
const fh = require('snmp-fiberhome')

const options = {
    ip: '0.0.0.0',             // OLT IP
    community: 'default',
    port: 161,
    trapPort: 162,
    enableWarnings: true,
    enableLogs: true
}

fh.function(options, ...)      // Call of functions
```

**IMPORTANT!** For ONUs using GPON technology, the `macAddress` parameter in the following functions will receive the equipment SERIAL.

The following functions are assigned the **slot**, **pon** and **onuId** parameters to specify a particular ONU. If you work with onuIndex, use the `parseOnuIndex()` function to convert onuIndex to an object containing the values of **slot**, **pon** and **onuId**.

The `onuIndex` parameter in the following functions is calculated as follows:

    slot*(2^25) + pon*(2^19) + onuId*(2^8)

See the function `convertToOnuIndex()`

# OLT functions

## getOltInformation()

**Description:** Get relevant information from OLT.

**Function signature:**

```js
getOltInformation(options: <object>) => Promise <object>
```

Example:

```js
fh.getOltInformation(options).then(oltInfo => {
    console.log(oltInfo)
})
```

Output:

```js
{
    alias: 'AN5516-01',
    hardwareModel: 'An5516-01',
    hardwareVersion: 'WKE2.115.331R1A',
    ip: "1.2.3.4",        // OLT IP
    macAddress: '10-0b-d2-12-35-7d',
    oid: '1.3.6.1.4.1.5875.800.1001.11',
    slots: [11, 16, 19, 20],
    softwareVersion: 'RP0700',
    subrack: {
        subrackIndex: 1,
        subrackName: 'AN5516-06',
        subrackType: 'An5516-06',
        totalSlotNumber: 14
    },
    systemContact: 'DefaultsysContact',
    systemLocation: 'DefaultsysLocation',
    systemName: 'Test',
    systemRunningTime: 133001838,
    systemRunningTimeUnit: 'ms',
    temperature: 39,
    temperatureUnit: '°C'
}
```

## getOltModel()

**Description:** Returns the OLT model.

**Function signature:**

```js
getOltModel(options: <object>) => Promise <string>
```

Example:

```js
fh.getOltModel(options).then(onuModel => {
    console.log(onuModel)
})
```

Output:

```js
'An5516-01'
```

## getSubrackInformation()

**Description:** Returns information regarding the subrack.

**Function signature:**

```js
getSubrackInformation(options <object>) => Promise <object>
```

Example:

```js
fh.getSubrackInformation(options).then(info => {
    console.log(info)
})
```

Output:

```js
{
    subrackIndex: 1,
    subrackName: 'AN5516-06',
    subrackType: 'An5516-06',
    totalSlotNumber: 14
}
```

## getPonPortList()

**Description:** Returns an array with the relevant information from all PON ports in the OLT.

**Function signature:**

```js
getPonPortList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getPonPortList(options).then(ponPorts => {
    console.log(ponPorts)
})
```

Output:

```js
[
    {
        authorizedOnus: 2,
        portDescription: 'PON 11/1',
        portUplinkRate: 1250,
        portUplinkRateUnit: 'Mbit/s',
        portDownlinkRate: 2500,
        portDownlinkRateUnit: 'Mbit/s',
        portEnableStatus: 'enable',
        portEnableStatusValue: 1,
        portIndex: 369623040,
        portName: 'PON 11/1',
        portOnlineStatus: 'online',
        portOnlineStatusValue: 1,
        portType: 'PON',
        portTypeValue: 1
    },
    // { ... }
]
```

## getPonPort()

**Description:** Returns the relevant information for a given PON port in OLT.

**Function signature:**

```js
getPonPort(options: <object>, slot: <number>, ponPort: <number>) => Promise <object>
```

Example:

```js
fh.getPonPort(options, 11, 1).then(ponPort => {
    console.log(ponPort)
})
```

Output:

```js
{
    authorizedOnus: 2,
    portDescription: 'PON 11/1',
    portUplinkRate: 1250,
    portUplinkRateUnit: 'Mbit/s',
    portDownlinkRate: 2500,
    portDownlinkRateUnit: 'Mbit/s',
    portEnableStatus: 'enable',
    portEnableStatusValue: 1,
    portIndex: 369623040,
    portName: 'PON 11/1',
    portOnlineStatus: 'online',
    portOnlineStatusValue: 1,
    portType: 'PON',
    portTypeValue: 1
}
```

# Slot functions

## getSlots()

**Description:** Returns an array with the number of each slot.

**Function signature:**

```js
getSlots(options: <object>) => Promise <Array>
```

Example:

```js
fh.getSlots(options).then(slots => {
    console.log(slots)
})
```

Output:

```js
[11, 16, 19, 20]
```

## getSlotsInformationList()

**Description:** Retorna uma array com informações relevantes de todos os slots na OLT.

**Function signature:**

```js
getSlotsInformationList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getSlotsInformationList(options).then(slots => { 
    console.log(slots)
})
```

Output:

```js
[
    {
        slot: 11,
        actualCardType: 'GC8B',
        actualCardTypeValue: 527,
        authorizedCardType: 'GC8B'
        authorizedCardTypeValue: 527,
        cardPresentStatus: 'present',
        cardInformation: {
            availablePorts: 8,
            numberOfPorts: 8,
            cardStatus: 'normal',
            cardStatusValue: 1,
            cardType: 'GC8B',
            cardTypeValue: 527,
            cpu: 4.75,
            cpuUnit: '%',
            memory: 41.54,
            memoryUnit: '%',
            hardwareVersion: 'WKE2.200.012R1C',
            slot: '11',
            softwateVersion: 'RP0700'
        }
    },
    // { ... }
]
```

# Card functions

## getCardList()

**Description:** Returns an array with relevant information from all cards.

**Function signature:**

```js
getCardsInformationList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getCardsInformationList(options).then(cards => {
    console.log(cards)
})
```

Output:

```js
[
    {
        availablePorts: 8,
        numberOfPorts: 8,
        cardStatus: 'normal',
        cardStatusValue: 1,
        cardType: 'GC8B',
        cardTypeValue: 527,
        cpu: 4.75,
        cpuUnit: '%',
        memory: 41.54,
        memoryUnit: '%',
        hardwareVersion: 'WKE2.200.012R1C',
        slot: '11',
        softwateVersion: 'RP0700'
    },
    // { ... }
]
```

## getCard()

**Description:** Retorna informações relevantes de um determinado card. If not found, the return will be `false`.

**Function signature:**

```js
getCard(options: <object>, slot: <number>) => Promise <object>
```

Example:

```js
fh.getCard(options, 11).then(card => {
    console.log(card)
})
```

Output:

```js
{
    availablePorts: 8,
    numberOfPorts: 8,
    cardStatus: 'normal',
    cardStatusValue: 1,
    cardType: 'GC8B',
    cardTypeValue: 527,
    cpu: 4.75,
    cpuUnit: '%',
    memory: 41.54,
    memoryUnit: '%',
    hardwareVersion: 'WKE2.200.012R1C',
    slot: '11',
    softwateVersion: 'RP0700'
}
```

# ONU functions
For all the following functions, if the ONU, pon port or slot is not found, the return is **false**.

## addAllOnus()

**Description:** This function performs WAN and Vlans authorization and configuration for all unauthorized ONUs in a OLT. The input parameters `profilesWan` and `vlans` are not required. To learn more about the `profilesWan` input parameter see the `setWan()` function. To learn more about the `vlans` input parameter, see the `setLanPorts()` function. If the authorized ONU already contains any profiles configured for WAN or Vlan, the old settings will be replaced with the new ones. The return is an array that contains all authenticated ONUs.

**Function signature:**

```js
addAllOnus(options: <object>, profilesWan: <Array>, vlans: <Array>) => Promise <Array>
```

Example:

```js
fh.addAllOnus(options,
[
    { wanMode: 'internet', wanConnType: 'router', wanVlan: 2001, ipMode: 'pppoe', translationValue: 2000, svlan: 2000 },
    { wanMode: 'tr069', wanConnType: 'router', wanVlan: 2002, ipMode: 'dhcp', translationValue: 2000, svlan: 3000 }
],
[
    { lan: 1, vlans: [{ transparent: 2001 }, { tag: 3011, cos: 3 }] },
    { lan: 2, enable: false }
]
).then(authOnuList => {
    console.log(authOnuList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        index: 0,
        slot: 11,
        pon: 1,
        onuId: 1,
        macAddress: 'FHTT1231e796',
        onuType: {
            category: 'SFU',
            code: 765,
            mode: 'dual',
            model: 'AN5506-04-F1',
            type: 'GPON'
        }
    },
    // { ... }
]
```

## addOnu()

**Description:** Similar to the `addAllOnus()` function, but performs WAN and Vlans authorization and configuration for a particular ONU.

**Function signature:**

```js
addOnu(options: <object>, onu: <object>, profilesWan: <Array>, vlans: <Array>) => Promise <object>
```

`onu: <object>` parameter:

```js
onu: {
    slot: <number>,
    pon: <number>, 
    onuTypeCode: <number>, 
    macAddress: <string>
}
```

Example:

```js
fh.addOnu(options, { slot: 11, pon: 1, onuTypeCode: 765, macAddress: 'FHTT1231e796' }, 
[
    { wanMode: 'internet', wanConnType: 'router', wanVlan: 2001, ipMode: 'pppoe', translationValue: 2000, svlan: 2000 },
    { wanMode: 'tr069', wanConnType: 'router', wanVlan: 2002, ipMode: 'dhcp', translationValue: 2000, svlan: 3000 }
],
[
    { lan: 1, vlans: [{ transparent: 2001 }, { tag: 3011, cos: 3 }] },
    { lan: 2, enable: false }
]
).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    slot: 11,
    onuId: 1,
    pon: 1,
    macAddress: 'FHTT1231e796',
    onuType: {
            category: 'SFU',
            code: 765,
            mode: 'dual'
            model: 'AN5506-04-F1'
            type: 'GPON'
        }
}
```

## authenticateOnu()

**Description:** Authenticates a particular ONU. WAN and Vlan settings can be made using the `setWan()` and `setLanPorts()` functions, respectively. All input parameters for `authenticateOnu()` can be obtained by the `getUnauthorizedOnus()` function.

**Function signature:**

```js
authenticateOnu(options: <object>, slot: <number>, pon: <number>, onuTypeCode: <number>, macAddress: <string>) => Promise <object>
```

Example:

```js
fh.authenticateOnu(options, 11, 1, 765, 'FHTT1231e796').then(authOnu => {
    console.log(authOnu)
})
```

Output:

```js
{
    slot: 11,
    pon: 1,
    macAddress: 'FHTT1231e796',
    onuType: {
        category: 'SFU',
        code: 765,
        mode: 'dual'
        model: 'AN5506-04-F1'
        type: 'GPON'
    }
}
```

## delOnu() and delOnuByMacAddress()

**Description:** Deletes a particular ONU. If the ONU is successfully deleted, the function returns the mac address of the respective ONU deleted, otherwise false.

**Function signature:**

```js
delOnu(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <string>
```

**Function signature:**

```js
delOnuByMacAddress(options: <object>, macAddress: <string>) => Promise <string>
```

Example:

```js
fh.delOnu(options, 11, 1, 1).then(macAddress => {
    console.log(macAddress)
})

// or

fh.delOnuByMacAddress(options, 'FHTT1231e796').then(macAddress => {
    console.log(macAddress)
})
```

Output:

```js
'FHTT1231e796'
```

## enableLanPorts()

**Description:** Enables and disables LAN ports for a specific ONU. If successful, the function returns `onuIndex`, otherwise `false`.

**Function signature:**

```js
enableLanPorts(options: <objecy>, slot: <number>, pon: <number>, onuId: <number>, aLanPorts: <Array>) => Promise <number>
```

Example:

```js
fh.enableLanPorts(options, 11, 1, 1, 
[
    { lan: 2,  enable: false },
    { lan: 3,  enable: true }
]
).then(onuIndex => {
    console.log(onuIndex)
})
```

Output:

```js
369623296
```

## getAuthorizedOnus()

**Description:** Returns a list of all authorized ONUs in OLT.

**Function signature:**

```js
getAuthorizedOnus(options: <object>) => Promise <Array>
```

Example:

```js
fh.getAuthorizedOnus(options).then(onuList => {
    console.log(onuList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        slot: 11,
        pon: 1,
        onuId: 1,
        macAddress: 'FHTT1231e796'
    },
    // { ... }
]
```

## getBasicOnuInfo()

**Description:** Searches for a ONU in OLT based on mac address. The ONU must be authorized. NOTE: The input parameters `slot` and `pon` are not required, but their use will make the search faster. On success, returns an object containing basic ONU information, otherwise returns `null`.

**Function signature:**

```js
getBasicOnuInfo(options: <object>, macAddress: <string>, slot: <number>, pon: <number>) => Promise <object>
```

Example:

```js
fh.getBasicOnuInfo(options, 'FHTT1231e796').then(onu => {
    console.log(onu)
})

// or

fh.getBasicOnuInfo(options, 'FHTT1231e796', 11, 1).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    slot: 11,
    pon: 1,
    onuId: 1,
    macAddress: 'FHTT1231e796'
}
```

## getLanPorts()

**Description:** Returns the LAN ports settings of a given ONU.

**Function signature:**

```js
getLanPorts(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <Array>
```

Example:

```js
fh.getLanPorts(options, 11, 1, 1).then(lanPorts => {
    console.log(lanPorts)
})
```

Output:

```js
[
    {
        lan: 1,
        enable: true,
        vlans: [
            { transparent: 3000 },
            { tag: 3001, cos: 4 }
        ]
    },
    { 
        lan: 2,
        enable: true,
        vlans: [ ]
    },
    { 
        lan: 3,
        enable: false,
        vlans: [ ]
    },
    {
        lan: 4,
        enable: true,
        vlans: [
            { transparent: 2005 }
        ]
    }
]
```

## getMacAddressList()

**Description:** Returns a list containing the mac address of all authorized ONUs in a given OLT

**Function signature:**

```js
getMacAddressList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getMacAddressList(options).then(macAddressList => {
    console.log(macAddressList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        macAddress: 'FHTT1231e796'
    },
    // { ... }
]
```

## getOnu()

**Description:** Returns relevant information from a particular ONU, such as: opticalPowers (signals), distance, model, macAddress, enters others.

**NOTE:** The `opticalPower` parameter is available for GPON technology only. EPON technology will return the parameters with zero value.

**Function signature:**

```js
getOnu(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnu(options, 11, 1, 1).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    slot: 11,
    pon: 1,
    onuId: 1,
    macAddress: 'FHTT1231e796',
    onuLogicAuthId: '',
    onuLogicAuthIdPass: '',
    onuStatus: 'online',
    onuStatusValue: 1,
    softwareVersion: 'RP2522',
    systemName: '',
    firmwareVersion: '',
    hardwareVersion: 'GJ-2.134.285F4G',
    ip: '0.0.0.0',
    distance: {
        _onuIndex: 369623296,
        value: '1.282',
        unit: 'km'
    },
    lastOffTime: {
        _onuIndex: 369623296,
        date: '2019-01-21',
        time: '08:01:04'
    },
    opticalPower: {
        _onuIndex: 369623296,
        rxPower:{
            value: '-19.25',
            unit: 'dBm'
        },
        txPower: {
            value: '3.03',
            unit: 'dBm'
        },
        temperature: {
            value: '45.10',
            unit: '°C'
        },
        voltage: {
            value: '3.21'
            unit: 'V'
        },
        currTxBias: {
            value: '9.70'
            unit: 'mA'
        }
    },
    onuType: {
        category: 'SFU'
        code: 765
        mode: 'dual'
        model: 'AN5506-04-F1'
        type: 'GPON'
    }, 
    upLinkInterface: {
        downlinkRate: 2500
        downlinkRateUnit: 'Mbit/s'
        portDescription: 'PON 11/1/1'
        portName: 'PON 11/1/1'
        portStatus: 'enable'
        portStatusValue: 1
        portType: 1
        uplinkRate: 1250
        uplinkRateUnit: 'Mbit/s'
    }
}
```

## getOnuBandwidth()

**Description:** Returns the bandwidth of a given ONU.

**Function signature:**

```js
getOnuBandwidth(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnuBandwidth(options, 11, 1, 2).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623552,
    slot: 11,
    pon: 1,
    onuId: 2,
    upBw: 2048,
    downBw: 1024,
    bandwidthUnit: 'Kbit/s'
}
```

## getOnuDistance()

**Description:** Returns the distance traveled by the fiber to a particular ONU in the kilometer unit.

**Function signature:**

```js
getOnuDistance(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnuDistance(options, 11, 1, 1).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    value: '1.282',
    unit: 'km'
}
```

## getOnuIdList()

**Description:** Returns a list with the `onuId` of all authorized ONUs in OLT.

**Function signature:**

```js
getOnuIdList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getOnuIdList(options).then(onuList => {
    console.log(onuList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        onuId: 1
    },
    {
        _onuIndex: 369623552,
        onuId: 2
    },
    // { ... }
]
```

## getOnuIndexList()

**Description:** Returns a list with the index of all authorized ONUs in OLT.

**Function signature:**

```js
getOnuIndexList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getOnuIndexList(options).then(onuList => {
    console.log(onuList)
})
```

Output:

```js
[ 369623296, 371720448, 371720704 ]
```

## getOnuLastOffTime()

**Description:** Returns the date and time of the last disconnection of a given ONU. Date Format:  (year)-(month)-(day). Time format: (hour):(minute):(second).

**Function signature:**

```js
getOnuLastOffTime(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnuLastOffTime(options, 11, 1, 1).then(onu => {
    console.log(onu)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    date: '2019-01-21',
    time: '08:01:04'
}
```

## getOnuListByPon()

**Description:** Returns a list of all connected ONUs on a given PON port.

**Function signature:**

```js
getOnuListByPon(options: <object>, slot: <number>, pon: <number>) => Promise <Array>
```

Example:

```js
fh.getOnuListByPon(options, 11, 1).then(onuList => {
    console.log(onuList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        slot: 11,
        pon: 1,
        onuId: 1,
        macAddress: 'FHTT1231e796',
        ip: '0.0.0.0',
        opticalRxPower: '-10.30',
        opticalRxPowerUnit: 'dBm',
        onuStatus: 'online',
        onuStatusValue: 1,
        softwareVersion: 'RP2522',
        systemName: ''
        firmwareVersion: '',
        hardwareVersion: 'GJ-2.134.285F4G',
        onuLogicAuthId: '',
        onuLogicAuthIdPass: '',
        lastOffTime: {
            _onuIndex: 369623296,
            date: '2019-01-21',
            time: '08:01:04'
        },
        distance: {
            _onuIndex: 369623296,
            value: '1.282'
            unit: 'km',
        },
        onuType: {
            category: 'SFU',
            code: 765,
            mode: 'dual',
            model: 'AN5506-04-F1',
            type: 'GPON'
        }
    },
    // { ... }
]
```

## getOnuOpticalPower()

**Description:** Returns information related to the signals, voltage, temperature and bias current of a particular ONU. 

**NOTE:** This option is available for GPON technology only. EPON technology will return the parameters with zero value.

**Function signature:**

```js
getOnuOpticalPower(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnuOpticalPower(options, 11, 1, 1).then(onuOpticalPower => {
    console.log(onuOpticalPower)
})
```

Output:

```js
{
    _onuIndex: 369623296,
    rxPower:{
        value: '-19.25',
        unit: 'dBm'
    },
    txPower: {
        value: '3.03',
        unit: 'dBm'
    },
    temperature: {
        value: '45.10',
        unit: '°C'
    },
    voltage: {
        value: '3.21'
        unit: 'V'
    },
    currTxBias: {
        value: '9.70'
        unit: 'mA'
    }
}
```

## getOnuOpticalPowerList()

**Description:** Returns a signal list of all authorized ONUs in OLT.

**NOTE:** This option is available for GPON technology only. EPON technology will return the parameters with zero value.

**Function signature:**

```js
getOnuOpticalPowerList(options: <object>) => Promise <Array>
```

Example:

```js
fh.getOnuOpticalPowerList(options).then(onuList => {
    console.log(onuList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296
        slot: 11,
        pon: 1,
        onuId: 1,
        opticalPower: {
            _onuIndex: 369623296,
            rxPower:{
                value: '-19.25',
                unit: 'dBm'
            },
            txPower: {
                value: '3.03',
                unit: 'dBm'
            },
            temperature: {
                value: '45.10',
                unit: '°C'
            },
            voltage: {
                value: '3.21'
                unit: 'V'
            },
            currTxBias: {
                value: '9.70'
                unit: 'mA'
            }
        }
    },
    // { ... }
]
```

## getOnuUplinkInterface()

**Description:** Returns information regarding the uplink interface of a given ONU.

**Function signature:**

```js
getOnuUplinkInterface(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <object>
```

Example:

```js
fh.getOnuUplinkInterface(options, 11, 1, 1).then(upLink => {
    console.log(upLink)
})
```

Output:

```js
{
    downlinkRate: 2500,
    downlinkRateUnit: 'Mbit/s',
    portDescription: 'PON 11/1/1',
    portName: 'PON 11/1/1',
    portStatus: 'enable',
    portStatusValue: 1,
    portType: 1,
    uplinkRate: 1250,
    uplinkRateUnit: 'Mbit/s'
}
```

## getOnuWebAdmin()

**Description:** Returns an array of web access settings for a given ONU.

**Function signature:**

```js
getOnuWebAdmin(options: <object>, slot: <number>, pon: <number>, onuId: <number>) => Promise <Array>
```

Example:

```js
fh.getOnuWebAdmin(options, 11, 1, 2).then(profiles => { 
    console.log(profiles)
})
```

Output:

```js
[
    {
        group: 'common',
        groupValue: 1,
        webUsername: 'user1',
        webPassword: '1111'
    },
    {
        group: 'admin',
        groupValue: 2,
        webUsername: 'user2',
        webPassword: '2222'
    },
    // { ... }
]
```

## getRxPowerListByPon()

**Description:** Returns a list containing the Rx signal from all ONUs of a given PON port in OLT.

**Function signature:**

```js
getRxPowerListByPon(options: <object>, slot: <number>, pon: <number>) => Promise <Array>
```

Example:

```js
fh.getRxPowerListByPon(options, 11, 1).then(rxList => {
    console.log(rxList)
})
```

Output:

```js
[
    {
        _onuIndex: 369623296,
        slot: 11,
        pon: 1,
        onuId: 1,
        opticalRxPower: '-20.30',
        opticalRxPowerUnit: 'dBm',
    },
    // { ... }
]
```

## getUnauthorizedOnus()

**Description:** Returns an array containing all unauthorized ONUs in OLT.

**Function signature:**

```js
getUnauthorizedOnus(options: <object>) => Promise <Array>
```

Example:

```js
fh.getUnauthorizedOnus(options).then(unauthOnus => {
    console.log(unauthOnus)
})
```

Output:

```js
[
    {
        index: 0,
        slot: 11,
        pon: 1,
        macAddress: 'FHTT1231e796',
        onuType: {
            category: 'SFU',
            code: 765,
            mode: 'dual'
            model: 'AN5506-04-F1'
            type: 'GPON'
        }
    },
    // { ... }
]
```

**NOTE:** The `mode` parameter in `onuType` is the reference to the ONU mode of operation, which can be:
* **router**: operates as a router only
* **bridge**: operates only as bridge
* **dual**: operates as a router and/or bridge

Not all ONUs will have the `mode` parameter on `onuType`


## parseOnuIndex()

**Description:** Converts a `onuIndex` to `slot`, `pon`, and `onuId`.

**Function signature:**

```js
parseOnuIndex(onuIndex: <number>) => <object>
```

Example:

```js
var onu = fh.parseOnuIndex(369623296)
console.log(onu)
```

Output:

```js
{
    _onuIndex: 369623296,
    slot: 11,
    pon: 1,
    onuId: 1
}
```

## setLanPorts()

**Description:** Configures the lan ports of a particular ONU, allowing you to add vlans as well as enable or disable the ports. Values for `transparent` and `tag` parameters must be within the range 1 to 4085. The parameter `cos` must be within the range 0 to 7

**Function signature:**

```js
setLanPorts(options: <object>, slot: <number>, pon: <number>, onuId: <number>, aLanPorts: <Array>) => Promise <number>
```

`aLanPorts` parameter:

```js
aLanPorts = [
    {
        lan: <number>,
        anable: <boolean>,            // (optional)
        vlans: [
            {
                transparent or tag: <number>,
                cos: <number>         // (optional) Priority or COS
            },
            // { ... }
        ]
    },
    // { ... }
]
```

Example:

```js
fh.setLanPorts(options, 11, 1, 1, [
    {
        lan: 1,
        vlans: [
            { transparent: 3000 },
            { tag: 3001, cos: 4 }
        ]
    }, 
    { 
        lan: 3,
        enable: false
    }, 
    { 
        lan: 4,
        vlans: [
            { transparent: 2005 }
        ]
    }
]).then(onuIndex => {
    console.log(onuIndex)
})
```

**NOTE:** LAN port 2 will not be changed in the example above.

Output:

```js
369623296
```

## setOnuBandwidth()

**Description:** Sets the bandwidth of a given ONU. The input parameters `upBw` and `downBw` must be in the unit of Kbit/s and the values must be within the range 256 to 1000000.

**Function signature:**

```js
setOnuBandwidth(options: <object>, slot: <number>, pon: <number>, onuId: <number>, upBw: <number>, downBw: <number>) => Promise <number>
```

Example:

```js
fh.setOnuBandwidth(options, 11, 1, 2, 2048, 1024).then(onuIndex => {
    console.log(onuIndex)
})
```

Output:

```js
369623552
```

## setOnuWebAdmin()

**Description:** Set the ONU access settings via web. Returns `true` on success and `false` otherwise. Some ONUs only accept a single 'admin' group profile, such as AN5506-01-A1, mas caso mais de um perfil seja informado, o perfil do grupo 'admin' será configurado e os demais serão ignorados para esse tipo de ONU.

**Function signature:**

```js
setOnuWebAdmin(options: <object>, slot: <number>, pon: <number>, onuId: <number>, aWebConfig: <Array>) => Promise <boolean>
```

Example:

```js
fh.setOnuWebAdmin(options, 11, 1, 1, [
    {username: 'user1', password: '1111', group: 'common'},
    {username: 'user2', password: '2222', group: 'admin'},
    {username: 'user3', password: '3333', group: 'common'}
]).then(config => {
    console.log(config)
})
```

Output:

```js
true
```

## setWan()

**Description:** Performs the WAN settings. Values not entered in `profiles` parameter will be set to `false`, except for the `lans` and `ssids` parameters, which are all set to true by default.

**Function signature:**

```js
setWan(options: <object>, slot: <number>, pon: <number>, onuId: <number>, profiles: <Array>) => Promise <number>
```

`profiles: <Array>` parameter:

```js
var profile = {        // Values:
    wanMode:           // 'tr069', 'internet', 'tr069_internet', 'multcast', 'voip', 'voip_internet', 'radius', 'radius_internet' or 'other'
    wanConnType:       // 'router' or 'bridge'
    wanVlan:           // <number> or false
    wanCos:            // <number> or false
    wanNat:            // true or false
    ipMode:            // 'dhcp', 'static' or 'pppoe'
    wanIp:             // 'x.y.w.z' or false
    wanMask:           // 'x.y.w.z' or false
    wanGateway:        // 'x.y.w.z' or false
    wanMasterDNS:      // 'x.y.w.z' or false
    wanSlaveDNS:       // 'x.y.w.z' or false
    pppoeProxy:        // true or false
    pppoeUsername:     // <string> or false
    pppoePassword:     // <string> or false
    pppoeName:         // <string> or false
    pppoeMode:         // 'auto' or 'payload'
    wanQoS:            // true or false
    vlanMode:          // 'transparent' or 'tag'
    translationValue:  // <number> or false
    cos:               // <number> or false
    QinQ:              // true or false
    tpid: 33024,
    svlan:             // <number> or false
    svlanCos:          // <number> or false
    lans: { 
        lan1:          // true or false
        lan2:          // true or false
        lan3:          // true or false
        lan4:          // true or false
        },
    ssids: { 
        ssid1:         // true or false
        ssid2:         // true or false
        ssid3:         // true or false
        ssid4:         // true or false
    }
}
```

Example:

```js
fh.setWan(options, 11, 1, 1, [
    { wanMode: 'internet', wanConnType: 'router', wanVlan: 2001, ipMode: 'pppoe', translationValue: 2000, svlan: 2000 },
    { wanMode: 'tr069', wanConnType: 'router', wanVlan: 2002, ipMode: 'dhcp', translationValue: 2000, svlan: 3000 }
]
).then(onuIndex => {
    console.log(onuIndex)
})
```

Output:

```js
369623296
```

## License

  [MIT](LICENSE)