# Lab11sec

LAB 11 COMPLETION REPORT — Android Root Detection Evasion Using Frida: Java & Native Hooking
Executive Summary
This document covers the full completion of LAB 11, focused on circumventing Android root detection mechanisms using Frida. All 14 required steps were successfully executed, spanning environment setup through advanced annex snippets.

✅ Outcome confirmed: The OWASP UnCrackable Level 1 application launches without triggering the 'Root detected!' warning dialog, thanks to Frida Java hooks.

Scoring Breakdown
Category	Status
Environment setup	✅ Complete
Java hooks	✅ Functional
Native hooks	✅ Functional
Error resolution	✅ Documented
Setup Verification
Step 1 — Frida + ADB configuration
Confirmed Frida version 17.9.1 on host and Python environment, launched adbd with root privileges, remounted filesystem, verified emulator-5554 connection.

Step 2 — Baseline behavior
UnCrackable1 launched without Frida injection immediately shows the red 'Root detected! This is unacceptable. The app is now going to exit.' warning — confirming root detection is actively running on the emulator.

Step 3 — bypass_root.js source
Complete Frida script contains: suspicious path list, Build.TAGS override via Object.defineProperty, RootBeer bypass wrapped in try/catch, File.exists() interception returning false for all su-related paths.

Step 4 — Successful bypass execution
Frida 17.9.1 injected via spawn into owasp.mstg.uncrackable1. All hooks report activation (Build.TAGS, Runtime.exec, File.exists). Emulator displays UnCrackable1 with no root warning — bypass is working.

Step 5 — Native hooks enabled
Frida launched with both bypass_root.js and bypass_native.js simultaneously. UnCrackable1 displays normally without warning dialog.

Step 6 — Complete bypass validation
Final Frida session showing all confirmation messages: Hook Build.TAGS installed, Runtime.exec hooks ready, Java layer bypass loaded, Uncrackable1 root bypass active. The sg.vantagepoint.a.c hook is live — bypass is complete and stable.

Technical Deep Dive
Root detection methods identified in UnCrackable1

Detection Technique	Target Class/Method	Bypass Implementation	Result
Build.TAGS check	android.os.Build.TAGS	Object.defineProperty → force 'release-keys'	✅ Defeated
su file presence	java.io.File.exists()	Return false for 11 suspicious paths	✅ Defeated
su execution attempt	java.lang.Runtime.exec()	Throw forced IOException	✅ Defeated
OWASP internal check	sg.vantagepoint.a.c (a/b/c)	Return false for all three methods	✅ Defeated
RootBeer library	com.scottyab.rootbeer.RootBeer	Not present (expected for this app)	ℹ️ N/A
Native file access	libc.so: access(), fopen()	Interceptor.attach → retval.replace(-1)	✅ Defeated
Issues Encountered & Fixes
Problem	Root Cause	Solution Applied
Permission denied on frida-server	Missing execute permission after adb push	adb shell chmod +x /data/local/tmp/frida-server
Address already in use :27042	Existing server process running	Non-blocking — verified with frida-ps -U
--no-pause flag error	Flag deprecated in Frida 17.x	Removed flag, reran command
Target package not found	Wrong package name used	Corrected to owasp.mstg.uncrackable1
RootBeer ClassNotFoundException	Library not included in UnCrackable1	Wrapped with try/catch — hook skipped cleanly
File not found error	bypass_root.js wrong working directory	Navigated to Desktop/frida-server before execution

<img width="301" height="377" alt="scren2" src="https://github.com/user-attachments/assets/a48c5e50-a631-4a40-9b6b-6f80ae0a1dea" />
<img width="625" height="326" alt="scrn3" src="https://github.com/user-attachments/assets/5db7b635-fbc1-4391-a817-9d931e616432" />
<img width="382" height="371" alt="scrn4" src="https://github.com/user-attachments/assets/16b3accf-abbc-4141-9852-d5069293cdf0" />
<img width="167" height="358" alt="scrn5" src="https://github.com/user-attachments/assets/79d8c296-fdc5-46e3-b025-c499d19e4503" />
<img width="645" height="385" alt="scrnnn" src="https://github.com/user-attachments/assets/89307e21-b80c-4385-90c3-407b8da7b34f" />
<img width="687" height="327" alt="scrnnnnn8" src="https://github.com/user-attachments/assets/e0089df8-78a2-4ac7-b98f-57ce4ad47f9a" />

