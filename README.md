⚠️ This environment is still in beta, crashes and future changes are to be expected ⚠️

⚠️ This is not a official repo, and has been made due to the discontinuation of the main repo  ⚠️
# Get started with FiveM's mono v2
Straight to the action? [set up your C# solution and resource](Setup.md) or [see examples](Examples/Overview.md)

## Major differences to v1
1. [Performance](#performance)
2. [Dividing CitizenFX.Core.dll into multiple libraries](#dividing-citizenfxcoredll-into-multiple-libraries)
3. [Native updates and compatibility](#dividing-citizenfxcoredll-into-multiple-libraries)
4. [Hello fast Coroutines, bye bye slow Tasks](#native-updates-and-compatibility)
5. [Tick-less, removing unnecessary per-frame overhead](#tick-less-removing-unnecessary-per-frame-overhead)
6. [Implicit parameter conversion for events, exports, and callbacks](Examples/MsgPack.md)

# Performance
| ![](https://user-images.githubusercontent.com/102315529/233750229-141384dd-d36b-4399-b423-fde740a1e542.png) |
|:--:|
| *Get on v2 and be the cyan guy* |
| *\*first mono runtime takes the biggest hit as well* |
1. **Interop**: all interop between C# and C++ is now done through thunks, a simple intermediate call
	1. Events and Exports are at least **84 times** faster
	2. String recoding is **43~47%** faster, precoding strings is also an option
	3. No unnecessary recoding on strings that aren't used
	4. Implicit parameter conversion on events, exports, and callbacks, decreasing invocation time by another **3 times**.
2. **Runtime enter/exit cost**: the default 0.04~0.06ms overhead cost in v1 has been eliminated, enjoy your 0.00ms.
3. **Custom ~~task~~ coroutine scheduler**: no longer uses the .NET scheduler, which brought quite some overhead.
4. **Natives**:
	1. Client can expect speed increases of **40~84%** and higher
	2. Servers on Windows have a **~10 times** speed increase (WSL **~160 times**)
	3. Custom native invocation can expect speed increases by **10-folds**

# Dividing CitizenFX.Core.dll into multiple libraries
In v1 everything was included in **CitizenFX.Core.dll**, v2 comes with separated libraries 
* **CitizenFX.Core.dll**: Core functionality for client, server, and shared libraries
* **CitizenFX.Server.dll**: Server related types and natives
* **CitizenFX.FiveM.dll**: FiveM (GTA V) related types and functionality
* **CitizenFX.FiveM.Native.dll**: All natives for FiveM (GTA V), make a copy of this one
* **CitizenFX.RedM.dll** RedM (RDR 2) related types and functionality
* **CitizenFX.RedM.Native.dll**: All natives for RedM (RDR 2), make a copy of this one

[*Make sure to copy the latest **CitizenFX.FiveM.Native.dll** or **CitizenFX.RedM.dll** and supply it with your resource*](#native-updates-and-compatibility)

# Native updates and compatibility
v2 will allow C# programmers to finally get back on track with the latest natives, allowing them to use the most up-to-date types that are currently known. At some point we'll even be able to add and remove parameters to natives!

This does come with some extra work for you as you'll need to copy the latest **CitizenFX.FiveM.Native.dll** for FiveM or **CitizenFX.RedM.Native.dll** for RedM and supply it with your resource. Doing this will add backwards compatibility to your C# resource, allowing them to always find the native methods they were compiled against. Failing to do so will result in `MissingMethodException`s in the future, rendering your resource broken.

# Hello fast Coroutines, bye bye slow Tasks
Task/Task\<T\> are used for the default .NET scheduler and its performance is well... really bad. It's now replaced with a custom scheduler that you can use by using Coroutine/Coroutine\<T\>, it's as simple as replacing Task with Coroutine and you are making use of it!

\* As a side note; although its scheduler is no longer restricted, we do not offer any support for using Task in v2 and may be prone to deactivation if required.

# Tick-less, removing unnecessary per-frame overhead
v2 got updated to run on the core scheduler, removing any runtime switching cost (that were left) when there's no `Tick` methods scheduled in your resource.

&nbsp;

Now go and [set up your C# solution and resource!](Setup.md)
