# Out-of-process C# COM server (CSExeCOMServer)
## Requires
- Visual Studio 2008
## License
- MS-LPL
## Technologies
- COM
- Windows SDK
## Topics
- Interop
- out-of-process COM Server
## Updated
- 03/04/2012
## Description

<h1>WINDOWS APPLICATION (CSExeCOMServer)</h1>
<h2>Introduction</h2>
<p class="MsoNormal">CSExeCOMServer demonstrates an out-of-process COM server in the form of local server (EXE), which is implemented entirely in Visual C#.
</p>
<p class="MsoNormal">CSExeCOMServer exposes the following item: </p>
<p class="MsoNormal">1. SimpleObject </p>
<p class="MsoNormal"><span style="">&nbsp; </span>(Please generate new GUIDs when you are writing your own COM server)
</p>
<p class="MsoNormal"><span style="">&nbsp; </span>Program ID: CSExeCOMServer.SimpleObject
</p>
<p class="MsoNormal"><span style="">&nbsp; </span>CLSID_SimpleObject: DB9935C1-19C5-4ed2-ADD2-9A57E19F53A3
</p>
<p class="MsoNormal"><span style="">&nbsp; </span>IID_ISimpleObject: 941D219B-7601-4375-B68A-61E23A4C8425
</p>
<p class="MsoNormal"><span style="">&nbsp; </span>DIID_ISimpleObjectEvents: 014C067E-660D-4d20-9952-CD973CE50436
</p>
<p class="MsoNormal"><span style="">&nbsp; </span>Properties: </p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
// With both get and set accessor methods
float FloatProperty

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal"><span style="">&nbsp; </span>Methods: </p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
// HelloWorld returns a string &quot;HelloWorld&quot;
string HelloWorld();
// GetProcessThreadID outputs the running process ID and thread ID
void GetProcessThreadID(out uint processId, out uint threadId);

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal"><span style="">&nbsp; </span>Events: </p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
// FloatPropertyChanging is fired before new value is set to the 
// FloatProperty property. The Cancel parameter allows the client to 
// cancel the change of FloatProperty.
void FloatPropertyChanging(float NewValue, ref bool Cancel);

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<h2>Using the Code</h2>
<h3><span style="">A. Creating the project </span></h3>
<p class="MsoNormal">Step1. Create a Visual C# / Windows / Console Application project named CSExeCOMServer in Visual Studio 2008.
</p>
<p class="MsoNormal">Step2. Open the project's Properties page, and change the output type to &quot;Windows Application&quot; in the Application tab. This avoids the console window when the executable file is started.
</p>
<h3><span style="">B. Adding the COMHelper class </span></h3>
<p class="MsoNormal">COMHelper provides the helper functions to register/unregister COM servers and encapsulates the native COM APIs to be used in .NET.
</p>
<h3><span style="">C. Adding the ExeCOMServer class </span></h3>
<p class="MsoNormal">ExeCOMServer encapsulates the skeleton of an out-of-process COM server in C#. The class implements the singleton design pattern and it's thread-safe. To start the server, call CSExeCOMServer.Instance.Run(). If the server is running, the
 function returns directly. Inside the Run method, it registers the class factories for the COM classes to be exposed from the COM server, and starts the message loop to wait for the drop of lock count to zero. When lock count equals zero, it revokes the registrations
 and quits the server.The lock count of the server is incremented when a COM object is created, and it's decremented when the object is released (GC-ed). In order that the COM objects can be GC-ed in time, ExeCOMServer triggers GC every 5 seconds by running
 a Timer after the server is started. </p>
<h3><span style="">D. Adding the COM-visible class SimpleObject </span></h3>
<p class="MsoNormal">Step1. Define a &quot;public&quot; COM-visible interface ISimpleObject to describe the COM interface of the coclass. Specify its GUID, aka IID, using GuidAttribute:
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
[Guid(&quot;941D219B-7601-4375-B68A-61E23A4C8425&quot;), ComVisible(true)]

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">In this way, IID of the COM object is a fixed value. By default, the interfaces used by a .NET Class are transformed to dual interfaces [InterfaceType(ComInterfaceType.InterfaceIsDual)] in the IDL. This allows the<span style="">
</span>client to get the best of both early binding and late binding. Other options<span style="">&nbsp;
</span>are [InterfaceType(ComInterfaceType.InterfaceIsIUnknown)] and [InterfaceType(ComInterfaceType.InterfaceIsIDispatch)].
</p>
<p class="MsoNormal">Step2. Inside the interface ISimpleObject, define the prototypes of the properties and methods to be exported.
</p>
<p class="MsoNormal">Step3. Define a &quot;public&quot; COM-visible interface ISimpleObjectEvents to describe the events the coclass can sink. Specify its GUID, aka the Events Id, using GuidAttribute:
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
[Guid(&quot;014C067E-660D-4d20-9952-CD973CE50436&quot;), ComVisible(true)]

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">Decorate the interface as an IDispatch interface: </p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
[InterfaceType(ComInterfaceType.InterfaceIsIDispatch)]

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">Step4. Inside the interface ISimpleObjectEvents, define the prototype of<span style="">
</span>the events to be exported. </p>
<p class="MsoNormal">Step5. Declare the class ReferenceCountedObject. The class is responsible for incrementing the lock count of the COM server in the constructor, and decrementing the lock count in the Finalize.
</p>
<p class="MsoNormal">Step6. Define a &quot;public&quot; COM-visible class SimpleObject that implements the interface ISimpleObject, and inherits from ReferenceCountedObject. Attach the attribute [ClassInterface(ClassInterfaceType.None)] to it, which tells
 the type-library generation tools that we do not require a Class Interface. This ensures that the ISimpleObject interface is the default interface. In addition, specify the GUID of the class, aka CLSID, using the Guid attribute:
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
[Guid(&quot;DB9935C1-19C5-4ed2-ADD2-9A57E19F53A3&quot;), ComVisible(true)]

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">In this way, CLSID of the COM object is a fixed value. Last, decorate the class with a ComSourceInterface attribute:
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
[ComSourceInterfaces(typeof(ISimpleObjectEvents))]

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">ComSourceInterfaces identifies a list of interfaces that are exposed as<span style="">
</span>COM event sources for the attributed class. </p>
<p class="MsoNormal">Step7. Make sure that the constructor of the class SimpleObject is not private (we can either add a public constructor or use the default one), so that the COM object is creatable from the COM aware clients.
</p>
<p class="MsoNormal">Step8. Inside SimpleObject, implement the interface ISimpleObject by writing the body of the property FloatProperty and the methods HelloWorld,<span style="">&nbsp;
</span>GetProcessThreadID. </p>
<h3><span style="">E. Registering SimpleObject in the registry </span></h3>
<p class="MsoNormal">Additional registry keys and values are required for the COM server. The default COM registration routine in Regasm.exe only works for InprocServer in the form of DLL. In order to register the LocalServer, we need to customize the registration
 routine to change InprocServer32 to LocalServer appropriately. </p>
<p class="MsoNormal">Step1. Inside SimpleObject, add the functions Register and Unregister, and decorate them with ComRegisterFunctionAttribute and ComUnregisterFunctionAttribute. The custom routine gets called after Regasm finishes the default behaviors.
 The Register and Unregister functions call the helper methods in COMHelper. </p>
<h3><span style="">F. Registering ClassFactory of SimpleObject </span></h3>
<p class="MsoNormal">Step1. Create a ClassFactory class that realizes the IClassFactory interface for SimpleObject.
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
/// &lt;summary&gt;
    /// Class factory for the class SimpleObject.
    /// &lt;/summary&gt;
    internal class SimpleObjectClassFactory : IClassFactory
    {
        public int CreateInstance(IntPtr pUnkOuter, ref Guid riid, 
            out IntPtr ppvObject)
        {
            ppvObject = IntPtr.Zero;


            if (pUnkOuter != IntPtr.Zero)
                Marshal.ThrowExceptionForHR(COMNative.CLASS_E_NOAGGREGATION);


            if (riid == new Guid(SimpleObject.ClassId) || 
                riid == new Guid(COMNative.GuidIUnknown))
                // Create the instance of the .NET object
                ppvObject = Marshal.GetComInterfaceForObject(
                    new SimpleObject(), typeof(ISimpleObject));
            else
                // The object that ppvObject points to does not support the 
                // interface identified by riid.
                Marshal.ThrowExceptionForHR(COMNative.E_NOINTERFACE);
            return 0;   // S_OK
        }


        public int LockServer(bool fLock)
        {
            return 0;   // S_OK
        }
    }

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">Step2. Register the class factory of SimpleObject using the standard<span style="">&nbsp;
</span>CoRegisterClassObject API when the server starts (In the PreMessageLoop method of ExeCOMServer). Please note that PInvoking CoRegisterClassObject to register COM objects is a technique which is not supported.
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
// Register the SimpleObject class object on start
int hResult = COMNative.CoRegisterClassObject(
    ref clsidSimpleObj,                 // CLSID to be registered
    new SimpleObjectClassFactory(),   // Class factory
    CLSCTX.LOCAL_SERVER,                // Context to run
    REGCLS.MULTIPLEUSE, 
    out _cookie);

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<p class="MsoNormal">Step3. Revoke the registration of SimpleObject using the CoRevokeClassObject API when the server stops (In the PostMessageLopp method of ExeCOMServer).
</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>C#</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span>
</div>
<span class="hidden">csharp</span>

<pre id="codePreview" class="csharp">
COMNative.CoRevokeClassObject(_cookie);

</pre>
</div>
</div>
<div class="endscriptcode">&nbsp;</div>
<h3><span style="">G. Configuring and building the project as a COM local server </span>
</h3>
<p class="MsoNormal">Step1. Open the Properties page of the project and turn to Build Events.
</p>
<p class="MsoNormal">Step2. In Post-build event command line, enter the commands:
</p>
<p class="MsoNormal"><span style="">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span>Regasm.exe &quot;$(<span class="SpellE">TargetPath</span>)&quot; </p>
<p class="MsoNormal">The commands register the COM-visible types (e.g. <span class="SpellE">
SimpleObject</span>) in the registry. </p>
<h2>More Information<span style=""> </span></h2>
<p class="MsoListParagraph" style=""><span style="font-family:Symbol"><span style="">��<span style="font:7.0pt &quot;Times New Roman&quot;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span></span><a href="http://www.codeproject.com/KB/COM/BuildCOMServersInDotNet.aspx">Building COM Servers in .NET</a>
</p>
<hr>
<div><a href="http://go.microsoft.com/?linkid=9759640" style="margin-top:3px"><img alt="" src="http://bit.ly/onecodelogo">
</a></div>
