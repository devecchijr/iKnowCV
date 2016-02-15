# iKnowCV

Demo that shows iKnow within HealthShare Clinical Viewer, giving Fast Insights based on a bunch of non structure clinical documents data, offering an interesting user experience and proving some fancy frameworks (ex: highcharts, bootstrap, angularjs, REST services hosted by Cache, etc)


Part of these steps and content of Marla Gonzalez's SDA was gotten from Tom Spencer's Demo available in http://twiki.iscinternal.com/twiki/bin/view/ISC/HealthShareUKClinicalNotesIKnowDemo. Thank you Tom!

Step 1 - Setup HealthShare and Load Marla's documents 
The basis for the demo is a standard HealthShare install. I usually use a BusDemo install. Basically get the clinician viewer working correctly. You can use this basic demo with any and all patients. After that you can load Marla Gonzales SDA (SDAMARLA.xml) to HS by copying to HSDirectory>\Data\HSEDGE1\SDAIn

Step 2 
On studio, Import iKnowCV.XML (namespace HSACCESS)

Step 3 - Configure Web Applications (See WEBAPP.jpeg). HSACCESS was already created but you can enable DeepSee and iKnow. iKnowCV is for Rest Service hosting.

Step 4 -  Like Spencer's demo, the data showed is based on a Cube. In a more real cenario, is possible to think in extend analytics (HS Insight) Document Cube and place the Rest Services there. So far, to load Marla's data to Cube you have to load her data into Clinical Viewer Page (Trakcare tables cache) by search page and run a method for Cube's table feeding. It also can be done in other patients. 

> Do ##Class(Custom.BRSC.iknow.ClinicalNotes).InsertCache()

> Do ##class(%DeepSee.Utils).%BuildCube("Clinical Notes")


So far you can test iknow page (http://localhost:port/csp/healthshare/hsaccess/iknow/iknow.CSP) without HS and Clinical Viewer. With no URL parameter it will be shown all the Cube's Data.

Obs: This page authenticates on Rest Service using the basic _system/sys user password. To use another user/pwd is needed to change Authorization settings in iknow.js) in seetings area.

Step 5 - Configure the Viewer

So all we need now is to configure the actual viewer so it displays a custom Page as a tab in the clinician viewer. This is done in two steps.

The first is to load in (or create) a utility class into the HSACCESS namespace. We are going to tell HealthShare that this class will provide the URL for our custom tab. I've done this in  ViewerUtil class. I have a few URLs for some other custom tabs, but the one that matters is the DocAnalysisUrl method. You will note that I just return the same URL regardless of the patient, but you could vary this based on the patient quite easily. Also my URL returned is based on the Clinical Notes being setup in HSACCESS, if you have placed this in another namespace you need to vary this URL accordingly.

The second is to configure the viewer (change port if needed):

    Log in to Trak (http://localhost:57772/csp/healthshare/HSAccess/web/Default.htm, adjusting host/port)
    Log in to Trak as hs/demo (if asked log into HealthShare/Caché as _system or similar)
    Click on Tools (Hammer)
    Click on Clinical Setup > Charts > New
    Call the chart 'Document Explorer' (or something else if you prefer) and you can give it a short name as desired
    Enter in a Custom Chart Expression: 
    	 ##class(Custom.BRSC.HS.UI.ViewerUtil).DocAnalysisUrl($Get(%request.Data("PatientID",1)),57772) 
    
    Hit apply and save this chart.
    Click on Clinical Setup > Chart Book Definition > Find
    Select HS-Doctor (to add it to the main clinician view, i.e. users with 'clinician' ticked)
    Hit the magnifying glass next to 'Select Chart'
    Find 'Document Explorer' (or whatever you named the chart as)
    It will now be in the 'Main Chart List' and you can move it up/down relative to other tabs
    Hit apply and save the chart book definition 

That should then be it and your ready to go. 


PS: This Demo may works on HS Version 2015.2.0 and above.

