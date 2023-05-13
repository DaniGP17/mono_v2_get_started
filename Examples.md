⚠️ This environment is still in beta, crashes and future changes are to be expected ⚠️
# Examples

## Natives
As before, but now they are in the **Natives** class, found in the *CitizenFX.FiveM.Native*, *CitizenFX.Server.Native*, or *CitizenFX.Shared.Native* namespace
```csharp
using CitizenFX.FiveM.Native; // client
using CitizenFX.Server.Native; // server
using CitizenFX.Shared.Native; // shared (for shared libraries)
...
int health = Natives.GetEntityHealth(ped);
int health = Natives.Call<int>(Hash.GET_ENTITY_HEALTH, ped);
```

## Coroutines replace Tasks
Where you used `Task`/`Task<T>` before, you now use `Coroutine`/`Coroutine<T>`, it's as simple as replacing Task with Coroutine. Below you can see that scheduling them as a repeating coroutine is still the same, if you want to schedule a coroutine/action as a non-repeating action then use `Scheduler.Schedule(Action [, int])`
```csharp
public Constructor()
{
	Tick += AsyncFunction; // creates and activates a repeating coroutine
}

async Coroutine AsyncFunction()
{
	// do some stuff here
	await WaitUntilNextFrame();

	// do some more stuff here
	await Yield(); // same as WaitUntilNextFrame

	// let's do stuff, then 1 second sleepy sleepy
	await Wait(1000);

	// done, though it'll run again next frame as it's registered as a repeating tick
}
```
\* *As a side note; although its scheduler is no longer restricted, we do not offer any support for using `Task` in v2 and may be prone to deactivation if required.*

## Events callable from anywhere
Events callable from anywhere
```csharp
Events.TriggerEvent("EventName", ...)
Events.TriggerServerEvent("EventName", ...)
Events.TriggerClientEvent("EventName", Player, ...)
```

## Export invocation
Export's calling syntax has changed, v1 used dynamic intermediate code that caused seconds of JIT lag, with the following syntax we circumvent that:
```csharp
Exports["ResourceName", "ExportName"](...); // in BaseScript inherited classes
Exports.Local["ResourceName", "ExportName"](...); // call from anywhere
```

## Register Events and restrict their accessibility
EventHandlers can now use the `Binding` overload to determine who can call this handler/export. The options are:
1. **Local** (default): client only accepts client events, server only server events
2. **Remote**: client only accepts server events, server only client events
3. **All**: accept events from both client and server
```csharp
[EventHandler("EventName", Binding.Local)]
private string EventFunction()
{
	return "Remote can't touch this, ...";
}
```

### Extra ways to (un)register:  
Events no longer pass `List<object>` objects, they are now pure arrays `object[]`.
```csharp
Constructor()
{
	// Add
	EventHandlers["Function"] += Func.Create<int, object[]>(EventFunction1);
	EventHandlers["Function"].Add(Func.Create<float>(EventFunction2));
	EventHandlers["Function"] += Func.Create<float, int>(EventFunction3WithReturn);
	
	// Remove
	EventHandlers["Function"] -= Func.Create<int, object[]>(EventFunction1);
	EventHandlers["Function"].Remove(Func.Create<float>(EventFunction2));
	EventHandlers["Function"] -= Func.Create<float, int>(EventFunction3WithReturn);
}

void EventFunction1(int i, object[] values) { }
void EventFunction2(float f) { }
int EventFunction3WithReturn(float f) { }
```

## ExportAttribute
Previously non-existent, can be used as follows:
```csharp
[Export("ExportName")]
private string ExportE2()
{
	return "yeehaa";
}
```

## CString
A UTF8 encoded string dedicated for interop. Do not use this as a replacement to C#'s `string`, if at all, better use it as a storage type for repeated interop usage.
```csharp
CString value = "This string is converted to a UTF8 string and doesn't need reconversion on interop!";
```