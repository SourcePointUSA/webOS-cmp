# WebOS Demo App

WebOS Demo Web App that demonstrates ability to display integrated CMP

## Requirements

1. WebOS SDK

## Import project

If you already have WebOS IDE installed:

1. Clone the repo to your workspace directory
2. In WebOS IDE click `File > Import` menu item
3. As source choose `Git`, then `Projects from Git > Existing local repository`
4. Choose the repo from step 1

## Running on the WebOS Emulator

To run the application on the emulator:

1. Turn on an Emulator machine
2. In the IDE Targe manager menu connect to the emulator machine
3. In the Project Explorer view of IDE, right-click the project and select `Run As / Debug As > webOS Application`

# Useful links

https://webostv.developer.lge.com/sdk/installation/
https://webostv.developer.lge.com/sdk/tools/emulator/introduction-emulator/
https://webostv.developer.lge.com/api/web-api/supported-standard-web-api/

## Implementation notes

To make the consent manager work you will need to include a bundle of scripts into your `index.html` file.

- Stub file(s)
- Client configuration script
- URL to messaging library

### Stub file(s)

The first part implementation script(s) contains the IAB stub functions. The stub functions set up the IAB privacy string object `__uspapi` (U.S. Privacy) and/or `__tcfapi` (GDPR TCF). This makes it available on queue to be called and released when needed. It is important to have these script tags in the first position to avoid errors and failure of service.

```javascript
// GDPR TCF stub file. Example only. Please use stub file generated in Sourcepoint portal as it may have changed.
<script type="text/javascript">
    function _typeof(t){return(_typeof="function"==typeof Symbol&&"symbol"==typeof Symbol.iterator?function(t){return typeof t}:function(t){return t&&"function"==typeof Symbol&&t.constructor===Symbol&&t!==Symbol.prototype?"symbol":typeof t})(t)}!function(){for(var t,e,o=[],n=window,r=n;r;){try{if(r.frames.__tcfapiLocator){t=r;break}}catch(t){}if(r===n.top)break;r=n.parent}t||(function t(){var e=n.document,o=!!n.frames.__tcfapiLocator;if(!o)if(e.body){var r=e.createElement("iframe");r.style.cssText="display:none",r.name="__tcfapiLocator",e.body.appendChild(r)}else setTimeout(t,5);return!o}(),n.__tcfapi=function(){for(var t=arguments.length,n=new Array(t),r=0;r<t;r++)n[r]=arguments[r];if(!n.length)return o;"setGdprApplies"===n[0]?n.length>3&&2===parseInt(n[1],10)&&"boolean"==typeof n[3]&&(e=n[3],"function"==typeof n[2]&&n[2]("set",!0)):"ping"===n[0]?"function"==typeof n[2]&&n[2]({gdprApplies:e,cmpLoaded:!1,cmpStatus:"stub"}):o.push(n)},n.addEventListener("message",(function(t){var e="string"==typeof t.data,o={};if(e)try{o=JSON.parse(t.data)}catch(t){}else o=t.data;var n="object"===_typeof(o)?o.__tcfapiCall:null;n&&window.__tcfapi(n.command,n.version,(function(o,r){var a={__tcfapiReturn:{returnValue:o,success:r,callId:n.callId}};t&&t.source&&t.source.postMessage&&t.source.postMessage(e?JSON.stringify(a):a,"*")}),n.parameter)}),!1))}();
</script>
```

```javascript
// US Privacy stub file. Example only. Please use stub file generated in Sourcepoint portal as it may have changed.
<script>
    (function () { var e = false; var c = window; var t = document; function r() { if (!c.frames["__uspapiLocator"]) { if (t.body) { var a = t.body; var e = t.createElement("iframe"); e.style.cssText = "display:none"; e.name = "__uspapiLocator"; a.appendChild(e) } else { setTimeout(r, 5) } } } r(); function p() { var a = arguments; __uspapi.a = __uspapi.a || []; if (!a.length) { return __uspapi.a } else if (a[0] === "ping") { a[2]({ gdprAppliesGlobally: e, cmpLoaded: false }, true) } else { __uspapi.a.push([].slice.apply(a)) } } function l(t) { var r = typeof t.data === "string"; try { var a = r ? JSON.parse(t.data) : t.data; if (a.__cmpCall) { var n = a.__cmpCall; c.__uspapi(n.command, n.parameter, function (a, e) { var c = { __cmpReturn: { returnValue: a, success: e, callId: n.callId } }; t.source.postMessage(r ? JSON.stringify(c) : c, "*") }) } } catch (a) { } } if (typeof __uspapi !== "function") { c.__uspapi = p; __uspapi.msgHandler = l; c.addEventListener("message", l, false) } })();
</script>
```

### Client configuration script

The client configuration script contains your organization's specific account configuration parameters. This configuration includes the necessary and optional parameters for your property to communicate with the Sourcepoint messaging platform and consent service libraries.

> You will need to include the `gdpr` and/or `ccpa` object depending on which campaign you have enabled on the property.

```javascript
<script type="text/javascript">
  window._sp_queue = [];
  window._sp_ = {
      config: {
          accountId: 22,
          baseEndpoint: 'https://cdn.privacy-mgmt.com',
          propertyHref: 'http://webosapp',
          gdpr: { },
          ccpa: { }
      }
}
</script>
```

### URL to messaging library

The final script is a URL that points to Sourcepoint's messaging libraries. This script only needs to be included once regardless of how many different types of messaging campaigns you run on the property.

```javascript
<script src="https://cdn.privacy-mgmt.com/unified/wrapperMessagingWithoutDetection.js"></script>
```

> If your organization has edited the `baseEndpoint` with a CNAME DNS record you will also need to edit the URL. Please follow the following format if necessary: `https://cname.subdomain/unified/wrapperMessagingWithoutDetection.js`

## Remote

To get possibility navigate on the Privacy Manager include:

```javascript
<script type="text/javascript">
function Navigation(e){e=e||{},this.viewportModal=e.viewport||"[id^='sp_message_iframe_']",this.tvKey={KEY_BACK_KEYBOARD:8,KEY_ENTER:13,KEY_BACK:461,KEY_0:48},this.onLoad()}Navigation.prototype={onLoad:function(){try{webOS.fetchAppInfo(function(e){console.log("Widget version: ",e.version)},webOS.fetchAppRootPath()+"appinfo.json"),webOS.deviceInfo(function(e){console.log("Model name:",e.modelName),console.log("Platform:",e.sdkVersion)})}catch(e){console.log("Something goes wrong on getting an application info")}this.bindEvents()},getViewportWindow:function(e){void 0===e&&(e=0);var t=this,n=document.querySelector(this.viewportModal);return(e+=1)<=3&&setTimeout(function(){null===n?t.getViewportWindow(e):n=n.contentWindow},1e3),n},getActiveElement:function(e){void 0===e&&(e=document.activeElement);const t=e.shadowRoot,n=e.contentDocument;return t&&t.activeElement?this.getActiveElement(t.activeElement):n&&n.activeElement?this.getActiveElement(n.activeElement):e},getBackButton:function(){var e=tileNavigation.getViewportWindow(),t=e.contentDocument.body.querySelectorAll("div.message-component.message-button"),n=e.contentDocument.body.querySelectorAll("div.back-button.focusable");return t.length?t[0]:n.length?n[0]:null},triggerClick:function(e){void 0===e&&(e=this.getActiveElement());var t=new Event("keydown",{bubbles:!0,cancelable:!0});t.keyCode=this.tvKey.KEY_ENTER,e.parentElement.className.split(" ").indexOf("categories")>=0&&(t=new KeyboardEvent("keypress",{key:"Enter",bubbles:!0,charCode:0,keyCode:this.tvKey.KEY_ENTER})),"BUTTON"===e.tagName&&e.className.split(" ").indexOf("ott-switch")>=0||e.dispatchEvent(t)},onKeyDown:function(e){if(e.keyCode===tileNavigation.tvKey.KEY_BACK||e.keyCode===tileNavigation.tvKey.KEY_BACK_KEYBOARD){var t=tileNavigation.getBackButton();t?(t.focus(),tileNavigation.triggerClick(t)):webOS.platformBack()}},onClick:function(e){tileNavigation.triggerClick()},bindEvents:function(){var e=this;window.setTimeout(function(){var t=tileNavigation.getViewportWindow();t&&(t.contentDocument.body.addEventListener("keydown",e.onKeyDown),t.contentDocument.body.addEventListener("click",e.onClick))},3e3)}};
window.onload = function () { window.tileNavigation = new Navigation();};
</script>
```

## Resurface OTT Message

Load the OTT message on demand by [retrieving the OTT message ID](https://docs.sourcepoint.com/hc/en-us/articles/20806618675603-Resurface-OTT-message) from the Sourcepoint portal and pass it to the `loadNativeOtt` function.

```javascript
//GDPR
window._sp_.gdpr.loadNativeOtt(GDPR_OTT_ID);

//U.S. Privacy Legacy
window._sp_.ccpa.loadNativeOtt(USP_LEGACY_OTT_ID);
```

Attach the `loadNativeOtt` function to an event handler on your project. Most organizations who implement this function will attach it to `onclick` event of an element.

```javascript
//GDPR
<button onclick="window._sp_.gdpr.loadNativeOtt(123456)">OTT GDPR</button>

//U.S. Privacy Legacy
<button onclick="window._sp_.ccpa.loadNativeOtt(987654)">OTT USP Legacy</button>
```

## Global Privacy Platform (GPP) Multi-State Privacy (MSPS) Support

To enable the MSPS signal on your web property that is currently utilizing the U.S. Privacy String, your organization will need to add the GPP stub file in addition to the U.S. Privacy - [CCPA stub](https://docs.sourcepoint.com/hc/en-us/articles/4405412395667#h_01FDD1S943DFHN9NMATFMRVFNH) stub file to `index.html`:

```
<script>
window.__gpp_addFrame=function(e){if(!window.frames[e])if(document.body){var t=document.createElement("iframe");t.style.cssText="display:none",t.name=e,document.body.appendChild(t)}else window.setTimeout(window.__gpp_addFrame,10,e)},window.__gpp_stub=function(){var e=arguments;if(__gpp.queue=__gpp.queue||[],__gpp.events=__gpp.events||[],!e.length||1==e.length&&"queue"==e[0])return __gpp.queue;if(1==e.length&&"events"==e[0])return __gpp.events;var t=e[0],p=e.length>1?e[1]:null,s=e.length>2?e[2]:null;if("ping"===t)p({gppVersion:"1.1",cmpStatus:"stub",cmpDisplayStatus:"hidden",signalStatus:"not ready",supportedAPIs:["2:tcfeuv2","5:tcfcav1","6:uspv1","7:usnatv1","8:uscav1","9:usvav1","10:uscov1","11:usutv1","12:usctv1"],cmpId:0,sectionList:[],applicableSections:[],gppString:"",parsedSections:{}},!0);else if("addEventListener"===t){"lastId"in __gpp||(__gpp.lastId=0),__gpp.lastId++;var n=__gpp.lastId;__gpp.events.push({id:n,callback:p,parameter:s}),p({eventName:"listenerRegistered",listenerId:n,data:!0,pingData:{gppVersion:"1.1",cmpStatus:"stub",cmpDisplayStatus:"hidden",signalStatus:"not ready",supportedAPIs:["2:tcfeuv2","5:tcfcav1","6:uspv1","7:usnatv1","8:uscav1","9:usvav1","10:uscov1","11:usutv1","12:usctv1"],cmpId:0,sectionList:[],applicableSections:[],gppString:"",parsedSections:{}}},!0)}else if("removeEventListener"===t){for(var a=!1,i=0;i<__gpp.events.length;i++)if(__gpp.events[i].id==s){__gpp.events.splice(i,1),a=!0;break}p({eventName:"listenerRemoved",listenerId:s,data:a,pingData:{gppVersion:"1.1",cmpStatus:"stub",cmpDisplayStatus:"hidden",signalStatus:"not ready",supportedAPIs:["2:tcfeuv2","5:tcfcav1","6:uspv1","7:usnatv1","8:uscav1","9:usvav1","10:uscov1","11:usutv1","12:usctv1"],cmpId:0,sectionList:[],applicableSections:[],gppString:"",parsedSections:{}}},!0)}else"hasSection"===t?p(!1,!0):"getSection"===t||"getField"===t?p(null,!0):__gpp.queue.push([].slice.apply(e))},window.__gpp_msghandler=function(e){var t="string"==typeof e.data;try{var p=t?JSON.parse(e.data):e.data}catch(e){p=null}if("object"==typeof p&&null!==p&&"__gppCall"in p){var s=p.__gppCall;window.__gpp(s.command,(function(p,n){var a={__gppReturn:{returnValue:p,success:n,callId:s.callId}};e.source.postMessage(t?JSON.stringify(a):a,"*")}),"parameter"in s?s.parameter:null,"version"in s?s.version:"1.1")}},"__gpp"in window&&"function"==typeof window.__gpp||(window.__gpp=window.__gpp_stub,window.addEventListener("message",window.__gpp_msghandler,!1),window.__gpp_addFrame("__gppLocator"));
</script>
```

Additionally, your organization will need to add the `includeGppApi` parameter to the `ccpa` object in your client configuration and set one of the following flag(s) depending on your organization's use case. [Click here](<https://github.com/SourcePointUSA/sp-roku-sdk/wiki/Global-Privacy-Platform-(GPP)-Multi%E2%80%90State-Privacy-(MSPS)>) for more information on each attribute, possible values, and examples for signatories and non-signatories of the MSPA.

If `includeGppApi` is set to `true`, the following MSPA arguments will be set accordingly:

- `MspaCoveredTransaction`: `"no"`
- `MspaOptOutOptionMode`: `"na"`
- `MspaServiceProviderMode`: `"na"`

Example:

```
window._sp_queue = [];
window._sp_ = {
    config: {
        accountId: 1584,
        baseEndpoint: 'https://cdn.privacy-mgmt.com',
        ccpa: {
            includeGppApi: true
        },
        propertyHref: 'https://www.testdemo.com',

```

Optionally, your organization can customize support for the MSPS by configuring the MSPA attributes as part of the GPP config.

```
window._sp_queue = [];
window._sp_ = {
    config: {
        accountId: 1584,
        baseEndpoint: 'https://cdn.privacy-mgmt.com',
        ccpa: {
            includeGppApi: {
                "MspaCoveredTransaction": "yes",
                "MspaOptOutOptionMode": "yes",
                "MspaServiceProviderMode": "no"
            }
        },
        propertyHref: 'https://www.testdemo.com',

```
