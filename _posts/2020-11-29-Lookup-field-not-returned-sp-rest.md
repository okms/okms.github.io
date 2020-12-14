---
layout: single
title:  "Lookup field not returned in SharePoint REST"
date:   2020-11-29 12:00:00 +0100
excerpt: "Valid fields causes REST voes in Microsoft 365 and SharePoint"
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/img/2020-11-29/jdr7v5pmsps7ngziy06x.png
  show_overlay_excerpt: true
categories: SharePoint, Microsoft365

---
Recently I reviewed a strange issue where a SharePoint document library had a Person/User field "Originator" which was impossible to access through REST. 

## TLDR

if you add a person/user/lookup field named `Originator`, you there will be no name collision when creating the field since there are no other field with the same internal name, but *accessing this field through REST cannot be done* since SP REST _will_ have a name collision with an internal field due to the `<internal name>Id` convention used by the API. 

## Details

Here is my simple test case, a list with only two manually configured user (person) fields `Orignator` as well as `AnotherUserField`. The same user has been added both places

![Alt Text](/assets/img/2020-11-29/rzj44yilkfke4251vara.png)

First a bit of background. I assume the original field was created through the UI, with the title `Originator`. This means the internal name aka static name for this field would be the same, i.e. `Originator`. This does not conflict with any OOB fields, so creating it is straight forward.

As this was a user field, its internally handled like any other lookup field. What this means is that the field is returned as `OriginatorId` when you do a rest query. Well, that's the theory anyway. Lets have a look

![Alt Text](/assets/img/2020-11-29/lj9fe20gt0wiq1fmzmz3.png)

I can find my item title as well as `AnotherUserField` which here is returned as `AnotherUserFieldId` as expected. But no `OriginatorId`. On a whim we pulled out the SchemaXml for the list, like so (my test list is aptly called 'test')

```PowerShell
(Get-PnPList -Identity test -Includes SchemaXml).SchemaXml | clip
```

If we search through that text (preferably pretty printed) we find our `Originator` field

```xml
    <Field Type="User" DisplayName="Originator" List="UserInfo" Required="FALSE" EnforceUniqueValues="FALSE" ShowField="ImnName" UserSelectionMode="PeopleOnly" UserSelectionScope="0" ID="{ef9f5a10-56dd-41e8-9e22-bfd095095ae0}" SourceID="{4fb24039-a619-44e7-a2cf-2be13e015f4b}" StaticName="Originator" Name="Originator" ColName="int4" RowOrdinal="0"/>
```

But searching further for the word `Originator` we found this internal hidden field which is added automatically to any list and required by some internal functionality in SPO, thus impossible to delete 

```xml
<Field ID="{14ee99cd-bed9-474a-bf99-8f753fbad6b4}" Name="OriginatorId" DisplaceOnUpgrade="TRUE" Hidden="TRUE" ReadOnly="TRUE" ShowInFileDlg="FALSE" Type="Lookup" DisplayName="Originator Id" List="Docs" FieldRef="ID" ShowField="OriginatorId" JoinColName="DoclibRowId" JoinRowOrdinal="0" JoinType="INNER" SchemaVersion="16.0.77.0" RecreateIfMissing="TRUE" SourceID="http://schemas.microsoft.com/sharepoint/v3" StaticName="OriginatorId" FromBaseType="TRUE"/>
```

This means, if you add a person/user/lookup field named `Originator`, you there will be no name collision when creating the field since there are no other field with the same internal name, but *accessing this field through REST cannot be done* since SP REST _will_ have a name collision with an internal field due to the `<internal name>Id` convention used by the API. 

There's a bunch more of these names that are valid, but should be avoided, here are some which have names that do not start with underscore:

* Prog
* Scope
* Unique
* ComplianceAsset
* SyncClient

## Conclusion

The simplest way to avoid this issue is to prefix internal names of your field. E.g. could the `Originator` field have been created like this

```xml
    <Field Type="User" DisplayName="Originator" List="UserInfo" Required="FALSE" EnforceUniqueValues="FALSE" ShowField="ImnName" UserSelectionMode="PeopleOnly" UserSelectionScope="0" ID="{ef9f5a10-56dd-41e8-9e22-bfd095095ae0}" SourceID="{4fb24039-a619-44e7-a2cf-2be13e015f4b}" StaticName="OKMSOriginator" Name="OKMSOriginator" />
```

This is, however, something you only would do if you created this field programmatically. Using the UI it is possible to create a field using the internal name you would like it to have, and then rename it, but this is not something regular users would ever do. 
