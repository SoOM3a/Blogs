---
title: JS Analysis
date: 2024-09-15 08:25:41 AM
categories: [Malware Analysis]
tags: [MalwareAnalysis101] 
render_with_liquid: false
media_subpath: /assets/img/MalwareAnalysis/MalwareAnalysis101
authors: [0]
---

## What is JS?
Javascript is a client side object oriented scripting language. Authors of JavaScript scripts use a variety of techniques to obfuscate the scripts. For example, they can use confusing syntax, unusual commands, variables with random names, and junk code, to make it hard for us to understand the script's logic.

## Samples informaion

| Sample Name         | Sh1 Hash         |
| :------------------ | :----------------------------------------- |
| loveyou.js          |  CE17790E9C0D1547F25AA3D30E38B5F17EB9FC8A  |

### Valditaing for Common Functions
First we need to check if sample contains susipous calls

#### Linux 

```shell
js-beautify loveyou.js | grep eval
```
![grep eval Linux](evalLinux.png)

#### Windows
```powershell
findstr "eval" samplename.js
```
### Analysis Methods
We have 3 main ways to analysis obfuscation javascript code, 
- Using [csript.exe](https://kb.webtrends.com/information/what-is-cscript-exe-and-what-is-it-used-for-1365447894619/) On Windows
- Using [Mozilla SpiderMonkey](https://github.com/mozilla-spidermonkey/spidermonkey.dev) On Linux
- Using [AMSI driver](https://learn.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-portal) On Windows

#### Cscript Deobfuscation For Windows

- you need to overwrite eval function, to print the eval output before running, you can prevent eval from runnuning by only output the string. 
Example: 
![overwrite eval for cscript windows](overwriteeval.png)

```powershell
csript.exe .\lovyou.js
```
![deobfuscation with csript windows](deobfuscation_csript.png)

if you wanna to run eval for any dynamic testing/wireshark traffic, you can update JS function as following 
```javascript
o_eval = eval;
eval = function(in_string) {
 WScript.Echo(in_string);
 o_eval(in_string);
 }
```

#### SpiderMonkey Deobfuscation For Linux
if we try to deobfuscation js directly, we will find that JS code depends on ActiveXObjects

```shell
js -f samle.js
```
![SpiderMonkey deobfuscation activeXObject issue](SpiderMonkey_deobfuscation_activeXissue.png)

> JS might depend on windows object such as ActiveXobjects yoyu mighjt download [object.js](https://docs.remnux.org/discover-the-tools/dynamically+reverse-engineer+code/scripts) which will help to fix this dependency. 
{: .prompt-danger }

- After fownload object.js, we can run spidermonkey with -f argument twoice and see the orignal JS code 

```shell 
js -f object.js -f sample.js
```
![SpiderMonkey deobfuscation activeXObject](SpiderMonkey_deobfuscation_activeX.PNG)

#### Windows AMSI Deobfuscation For Windows

We need to enable driver for logging, run the JS code in Windows native enviroment. Last step stop driver listening and save driver output 
- Enable AMSI driver listening 
```powershell
logman start mytrace -p Microsoft-Antimalware-Scan-Interface Event1 -o AMSITrace.etl -ets
```
- Run the suspicious script 
```powershell
cscript loveyou.js
```
- Stop AMSI driver monitoring
```powershell
 logman stop mytrace -ets
```
- Save the driver logs, for investigation 
```powershell
 AMSIScriptContentRetrieval > events.txt
```
![deobfuscation with AMSI windows Driver](deobfuscation_AMSI.png)

> If the malware using evasion techniques such as .callee.toString() to check that the orignal function eval/location did not be overwirtten. you can use js box for invistgation such as [box-js](https://github.com/CapacitorSet/box-js)
{: .prompt-tip }

<script src="https://giscus.app/client.js"
        data-repo="SoOM3a/Blogs"
        data-repo-id="R_kgDOLebVZA"
        data-category="General"
        data-category-id="DIC_kwDOLebVZM4Cd9IX"
        data-mapping="url"
        data-strict="1"
        data-reactions-enabled="1"
        data-emit-metadata="1"
        data-input-position="top"
        data-theme="preferred_color_scheme"
        data-lang="en"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>