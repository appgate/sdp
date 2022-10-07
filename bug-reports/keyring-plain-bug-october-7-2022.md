# tgpski AppGate SDP Client Bug Report Oct 7 2022

I could not find a public facing portal to report on bugs, so I am using this MR to draw attention to a problem.

## Core problem

The linux keyring client is using a `plain` encryption algorithm to store secrets in the system keyring when setting up a profile. The current algorithm for keyring communication should be `dh-ietf1024-sha256-aes128-cbc-pkcs7`.

## Stack Trace

```
[2022-10-07T22:18:13.727Z] Error : Error while handling empower requests. $'System.AggregateException: One or more errors occurred. (One or more errors occurred. (org.freedesktop.DBus.Error.InvalidArgs: Algorithm plain is not supported. (only dh-ietf1024-sha256-aes128-cbc-pkcs7 is supported)))
 ---> System.AggregateException: One or more errors occurred. (org.freedesktop.DBus.Error.InvalidArgs: Algorithm plain is not supported. (only dh-ietf1024-sha256-aes128-cbc-pkcs7 is supported))
 ---> Tmds.DBus.DBusException: org.freedesktop.DBus.Error.InvalidArgs: Algorithm plain is not supported. (only dh-ietf1024-sha256-aes128-cbc-pkcs7 is supported)
   at Tmds.DBus.DBusConnection.CallMethodAsync(Message msg, Boolean checkConnected, Boolean checkReplyType)
   at Tmds.DBus.Connection.CallMethodAsync(Message message)
   at Tmds.DBus.CodeGen.DBusObjectProxy.SendMethodReturnReaderAsync(String iface, String member, Nullable`1 inSignature, MessageWriter writer)
   at Tmds.DBus.CodeGen.DBusObjectProxy.CallNonVoidMethodAsync[T](String iface, String member, Nullable`1 inSignature, MessageWriter writer, ReadMethodDelegate`1 readValue)
   --- End of inner exception stack trace ---
   at System.Threading.Tasks.Task.ThrowIfExceptional(Boolean )
   at System.Threading.Tasks.Task`1.GetResultCore(Boolean )
   at System.Threading.Tasks.Task`1.get_Result()
   at Stratus.LinuxClient.CredentialStorage.LibSecretKeyring.StoreItemAsync(String id, String secretToStore, String type, String schema) in /home/appgate/cabf95aa4bca1f9d/src/client/Linux/CredentialStorage/LibSecretKeyring.cs:line 322
   --- End of inner exception stack trace ---
   at System.Threading.Tasks.Task.ThrowIfExceptional(Boolean )
   at System.Threading.Tasks.Task.Wait(Int32 , CancellationToken )
   at System.Threading.Tasks.Task.Wait()
   at Stratus.LinuxClient.CredentialStorage.LibSecretKeyring.StoreProfile(String id, String profile) in /home/appgate/cabf95aa4bca1f9d/src/client/Linux/CredentialStorage/LibSecretKeyring.cs:line 198
   at Stratus.LinuxClient.Shared.SecureStorage.StoreProfile(String id, String data) in /home/appgate/cabf95aa4bca1f9d/src/client/Linux/Shared/SecureStorage.cs:line 218
   at Shared.Client.Logic.Components.Setup.Setup.SafeStoreProfile(Profile newProfile)
   at Shared.Client.Logic.Components.Setup.Setup.StoreProfile(String name, ControllerUrl controllerUrl, IdentityProvider identityProvider, String newCertificate)
   at Shared.Client.Logic.Components.Setup.SetupLogic.<>c__DisplayClass32_0.<CreateProfile>b__0()
   at System.Threading.Tasks.Task`1.InnerInvoke()
   at System.Threading.Tasks.Task.<>c.<.cctor>b__272_0(Object )
   at System.Threading.ExecutionContext.RunInternal(ExecutionContext , ContextCallback , Object )
--- End of stack trace from previous location ---
   at System.Threading.ExecutionContext.RunInternal(ExecutionContext , ContextCallback , Object )
   at System.Threading.Tasks.Task.ExecuteWithThreadLocal(Task& , Thread )
--- End of stack trace from previous location ---
   at Shared.Client.App.Connection.LiveApps.Empower.Incoming.ContractLogic.SetMimeUrlContractLogic.Process(UrlContract contract, IViewAction viewAction, StatusProvider statusProvider, IActionCenterSession actionCenter)
   at Shared.Client.App.Connection.LiveApps.Empower.Incoming.EmpowerContractLogic`1.Process(IViewAction viewAction, StatusProvider statusProvider)
   at Shared.Client.App.Connection.LiveApps.Empower.Incoming.EmpowerIncomingContractLogic.RunContractResponse[TContract](EmpowerContractLogic`1 contractLogic)'
```

## System Information

* AppGate-SDP full client version: Version: 6.0.2-30054-release
* Linux Kernel: 5.18.19-3-MANJARO 64-bit
* KDE Plasma: 5.25.5
* KDE Frameworks: 5.98.0
* QT: 5.15.6

I have been using AppGate for about 7 months on this system with no problems. An underlying keyring update to my system disabled plain as an option for accessing secure secrets via keyring, and now I am locked out of my corporate account.