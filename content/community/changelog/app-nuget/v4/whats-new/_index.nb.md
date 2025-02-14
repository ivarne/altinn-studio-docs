---
title: Hva er nytt?
description: Oversikt over endringer som ble introdusert i versjon 4.
toc: true
tags: [translate-to-norwegian]
---

## 4.14.1 (2021-09-22) - 500 error when retrieving non existing instance fixed

There was a bug causing a 500 response when an request is made towards Get/Instances for a
non-existing instance. This has now been fixed and the response returned is 403.
Swagger for the endpoint is updated to reflect possible response codes.

## 4.14.0 (2021-09-13) - Partial support for namespace XML
The code that deserializes XML has been updated to support namespace declaration in the root element.

Example:
```xml
<Skjema xmlns="urn:no:altinn:skjema:v1">
   <Navn>Altinn</Navn>
</Skjema>
```
Deserialization occurs when an external system uses the app API to submit a new form, when they overwrite an existing form, and when an app retrieves a form from blob storage.

The change is not automatically used by all apps that update to this version. For the change to take properly effect the C# class that represents the model must be updated. The class needs to be decorated with an XmlRootAttribute with the Namespace property set to the correct namespace. 

Example:
```cs
[XmlRoot(ElementName = "Skjema", Namespace = "urn:no:altinn:skjema:v1")]
public class Skjema {
    [MaxLength(100)]
    [XmlElement("Navn")]
    public string Navn { get; set; }
}
```
This change must be done manually for all old and new models. The model editor in altinn.studio has not be updated to do this automatically.

## 4.13.0 (2021-09-03) - Event for changed substatus on instance
Changing the substatus of an instance triggers an event `app.instance.substatus.changed` which can be subscribed to in the event component.

This solves issue [#6691](https://github.com/Altinn/altinn-studio/issues/6691)

## 4.12.0 (2021-08-27) - Identity data is included in the request telemetry for all requests
In Application Insights we now register the properties listed below enabling linking of an entity to a specific request received by the application.

- partyId
- authentication level
- userId 
- organisationNumber

This solves issue [#5983](https://github.com/Altinn/altinn-studio/issues/5983)


## 4.11.1 (2021-08-26) - No longer possible to cache response from stateless apps
Caching of the stateless data responses is no longer possible.

This solves issue [#6532](https://github.com/Altinn/altinn-studio/issues/6532)

## 4.11.0 (2021-08-03) - Support for disabling reportee selection in Altinn Portal
Apps now support adding query parameter `DontChooseReportee=true` to disable the reportee selection when an unauthorized user accesses an app. 
The result being that the user will represent themselves and be routed directly to the application after login.

This release solves issue [#6573](https://github.com/Altinn/altinn-studio/issues/6573).

## 4.10.2 (2021-07-15) - Text resources are loaded locally
- The app will now load texts from the locally stored text resource files (config/texts/*) instead of retrieving them from Storage. Texts are still uploaded to Storage during deploy. The change is to remove unnecessary calls to Storage and to avoid an issue with caching that prevented new texts from being used immediately. [#6466](https://github.com/Altinn/altinn-studio/issues/6466), [#6415](https://github.com/Altinn/altinn-studio/issues/6415)
- Fixed a bug where a filename with space in it could lead to a crash. [#6421](https://github.com/Altinn/altinn-studio/issues/6421)
- New apps created after the v2021.29 release will provide security headers like X-Frame-Options, X-XSS-Protection, X-Content-Type-Options, and Referer-Policy. To activate this in existing apps follow these steps:
   - Open the `App/Startup.cs` file.
   - At the top of the file add the namespace reference: `using Altinn.App.Api.Middleware;`
   - Find the `Configure` method and add the statement: `app.UseDefaultSecurityHeaders();` Add it right before existing `app.Use*` statements. E.g. before `app.UseRouting();`


## 4.9.2 (2021-07-08) - Fixed messages from multipart request validation
Validation messages from multipart request validation was misleading. This release solved issue [#6418](https://github.com/Altinn/altinn-studio/issues/6418). 


## 4.9.1 (2021-07-02) - Bugfix for errors in multipart validation
Fixed a bug that caused validation messages to show C# type of DataType rather than DataTypeId.
Issue [#6418](https://github.com/Altinn/altinn-studio/issues/6418)


## 4.9.0 (2021-06-29) - Support for marking a single field validation error as fixed
It is now possible to mark a previous validation error as fixed by using the prefix `*FIXED*` in front of the original error. 
[documentation on how to implement the functionality](https://altinn.github.io/docs/altinn-studio/app-creation/logic/validation/#spesifisere-at-valideringsfeil-er-fikset) (in Norwegian )


## 4.8.0 (2021-06-22) - Application version number available in AppSettings
During app deployment an environment variable with the app version number/name is added to the app runtime environment. This version information can now be retrieved in any controller or service through the AppSettings configuration object. Just add a dependency on `AppSettings` into the class and access the new property called `AppVersion`.

## 4.7.1 (2021-06-15) - Adjustments to response headers
Some of the controllers exposed by the applications have been modified to not allow caching and/or storage of their responces in the client.

## 4.7.0 (2021-06-08)

Altinn Apps now authorize access for statless apps.

Altinn Apps now have two new application events where application developers can add data processing logic. calculation, population, and more.

In this update the RunCalculate application event is made obsolete/deprecated. It's recommended that Apps are updated to use RunProcessDataWrite and RunProcessDataRead instead. Calls to the RunCalculate method will be removed in a future update.

The process to update is

1. Add the DataProcessing folder and DataProcessingHandler class from our [app template](https://github.com/Altinn/altinn-studio/tree/master/src/Altinn.Apps/AppTemplates/AspNet/App/logic) to your app.
2. Update App.cs. Add a class field for DataProcessingHandler and copy new methods ( RunProcessDataRead and RunProcessDataWrite) from [App.cs](https://github.com/Altinn/altinn-studio/blob/master/src/Altinn.Apps/AppTemplates/AspNet/App/logic/App.cs)
3. Move logic from calculation handler to DataProcessinghandler
4. Remove RunCalculation method from App.cs
5. Remove CalculationHandler when code has been moved to DataProcessingHandler.
6. Compile and test your app. 

See details about data processing [here](https://altinn.github.io/docs/altinn-studio/app-creation/logic/dataprocessing/)

## 4.6.2 (2021-06-01) - Duplicate keys in options causing crash

This release has a fix for a crash related to PDF rendering when an app have [options](https://altinn.github.io/docs/altinn-studio/app-creation/data/options/) with duplicate entries. [#5887](https://github.com/Altinn/altinn-studio/issues/5887)

## 4.6.1. (2021-05-21) Changed alternative subject

Altinn Apps now uses org instead of organization as subject when publishing events.

## 4.6.0 (2021-05-11) - Apps now support data fields
Altinn Apps now support data fields.
Data fields allows for adding data values, from either form fields or a custom source, to the instance object.
Form data can be added by configuring data fields in `applicationmetadata.json` while custom sources require coding.
Documentation on how to add data values to an instance can be found [here](https://altinn.github.io/docs/altinn-studio/app-creation/configuration/datafields/).


## 4.5.2 (2021-05-04) - Endpoints for stateless data elements exposed through app. Bug stopping local testing fixed

Altinn Apps now expose endpoints for creating, prefilling and running calculations on stateless data elements.
A stateless data element entails there is no link to an instance or instance owner, and the data is simply presented to the end user, but not persisted in any database.

In addition, a bug breaking apps running with localtest intoduced in 4.4.1 has been fixed.

Information on the new endpoints can be found in the swagger exposed by each application https://{org}.apps.altinn.no/{org}{app}/swagger

## 4.4.1 (2021-04-30) - Ask user to upgrade security level 

An app would show the "unknown error" message if a user were trying to access an instance with a security level that was too low for the instance. This has been fixed. The user is now sent to authentication with the option to pick an authentication method that provides a higher security level. The fix targets the GET instance endpoint specifically.

## 4.4.0(2021-04-27) - Performance fix
Improved performance.

## 4.3.0 (2021-04-28) - Apps now support presentation fields

Altinn Apps now support presentation fields. 
By specifying presentation fields in `applicationmetadata.json`, speficied data values from the form data
will be stored on the instance in order to show them along with the app title in the Altinn messagebox. 
Further documentation on how to configure presentation fields is found [here](https://altinn.github.io/docs/altinn-studio/app-creation/configuration/presentationfields/).

This change is related to [this epic](https://app.zenhub.com/workspace/o/altinn/altinn-studio/issues/594).

## 4.2.0 (2021-04-19) - Possible to integrate an app with eFormidling

Altinn Apps now support integration with eFormidling. 
Documentation on how to set up an application to use eFormidling will be published
once an integration point for eFormidling is set up in Altinn Platform. 

## 4.1.0 (2021-04-07) - Add new property with updated data to response for PUT to DataController

During PUT of data to DataController (`{org}/{app}/instances/{instanceOwnerPartyId:int}/{instanceGuid:guid}/data`), any calculations that are 
defined by the apps are run, and data is potentially updated before being saved.
Previously, the response returned only the metadata for the updated data element, and a GET to fetch the updated data was necessary.
In this version, a dictionary of all the fields that have updated data from calculations is returned as a new parameter
in the API response (in addition to the data element metadata), so that clients do not need to perform the additional GET request in order
to get the updated data.

This change is related to [this issue](https://github.com/Altinn/altinn-studio/issues/5754).

## 4.0.3 (2021-03-23) - Fixed a bug reading filename from Content-Disposition 

- The specification for Content-Disposition specify that `filename` should be in quotes. This was not supported by the app backend API, causing requests following the specification to fail. This has been fixed.
- Added support for `filename*` (FilenameStar). If Content-Disposition contain both `filename` and `filename*`, the value defined by `filename*` will be used.

## 4.0.1 (2021-03-15) - Upgraded application to .Net 5 and grouped references of Altinn App and Altinn Platform services in Startup.cs

Altinn.App.* librarires target .Net 5 now, which requires that the application does the same.
In addition we have created two methods for referencing all app and platform sevices in Startup.cs 

See [breaking changes](../breaking-changes) for how to update you app to be compatible with this version.
