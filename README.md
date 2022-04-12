# SmartMAir
Reverse Engineer Mitsubishi Heavy Industries WF-RAC module


# Trying to reverse engineer the WF-RAC communication

Things done:
1. Disconnected Cloud services
2. Discover local IP of airco
3. Postscan on IP
4. Remote Wireshark on Unifi accesspoint to sniff data
5. Decode APK with APKTool and dex2jar

# Things found by sniffing network data and portscan
1. Airco is reacting on port 51443 via http communication
2. Reacts when posting json content data
3. Useragent 'okhttp/4.9.1' but also responds to other user agents
4. Main info via: http://<localip>:51443/beaver/command/getAirconStat
  by sending json: '{"apiVer":"1.0","command":"getAirconStat","deviceId":"1234567890ABCDEF","operatorId":"d2bc4571-1cea-4858-b0f2-34c18bef1901","timestamp":1649703587}'
  
  deviceId seems to be android ID of the used phone. Can be random generated
  operatorId seems to be random generated. Can be random generated
  timestamp is unix epoch
  
Sending this json to the airco responds with data:
  {
    "airconId":  "REMOVED",
    "airconStat":  "AACyiKT/AAAAAAASCgAAAAAAAf////8sv4AEEAAkigAAiAAAAgAAAAAAAAOAIIr/gBCN/5QQAQBufw==",
    "logStat":  0,
    "updatedBy":  "local",
    "expires":  1649704590,
    "ledStat":  1,
    "autoHeating":  0,
    "highTemp":  "AB",
    "lowTemp":  "66",
    "firmType":  "WF-RAC",
    "wireless":  {
                     "firmVer":  "010"
                 },
    "mcu":  {
                "firmVer":  "123"
            },
    "timezone":  "Europe/Amsterdam",
    "remoteList":  [
                       "",
                       "",
                       "",
                       ""
                   ],
    "numOfAccount":  1
}
  
airconStat changes when settings are changed on airco.

# Things found in dex2jar 
 1. MakeBeaerApi.class defines buildup of api call
    paramstring1: local IP
    paramstring2: command (getDeviceInfo, getAirconStat, setAirconstat, deleteAccountInfo, setMetworkInfo, updateAccountInfo, updateFirmware)
 2. discovery works with AirconDiscovery.class by multicast broadcast _beaver._tcp.<local>
 3. ResponseGetAirconStatEntity.class has AirconStat class
 4. ResponseAirconMApper.class and ResponseArconStartMapper.class contain mappings
 
  
