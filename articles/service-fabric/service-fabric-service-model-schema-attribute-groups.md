---
title: Grupy atrybutów schematu XML modelu usługi
description: Opisuje grupy atrybutów w schemacie XML modelu usługi Service Fabric.
ms.topic: reference
ms.date: 12/10/2018
ms.openlocfilehash: 95e19dbdeafdd03af56acaeeb06901a36f2e0333
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609387"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-attribute-groups"></a>Grupy atrybutów schematu XML modelu usługi

## <a name="accountcredentialsgroup-attributegroup"></a>AccountCredentialsGroup atrybutu

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|AccountCredentialsGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountCredentialsGroup">
        <xs:attribute name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        <xs:attribute name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    </xs:attributeGroup>
    
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="accountname"></a>AccountName
Nazwa użytkownika lub nazwa konta usługi (na przykład MyMachine\JohnDoe lub John.Doe@department.contoso.com).

|Atrybut|Wartość|
|---|---|
|name|AccountName|
|type|XS: ciąg|
|używanie|opcjonalnie|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        
```

#### <a name="password"></a>Hasło
Hasło konta użytkownika.

|Atrybut|Wartość|
|---|---|
|name|Hasło|
|type|XS: ciąg|
|używanie|opcjonalnie|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    
```

## <a name="applicationinstanceattrgroup-attributegroup"></a>ApplicationInstanceAttrGroup atrybutu
Grupa atrybutów dla wystąpienia aplikacji.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|ApplicationInstanceAttrGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationInstanceAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application instance.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:attribute name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="nameuri"></a>NameUri
W pełni kwalifikowana nazwa aplikacji.

|Atrybut|Wartość|
|---|---|
|name|NameUri|
|type|FabricUri|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

#### <a name="applicationid"></a>ApplicationId
Identyfikator tej aplikacji.

|Atrybut|Wartość|
|---|---|
|name|ApplicationId|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="applicationmanifestattrgroup-attributegroup"></a>ApplicationManifestAttrGroup atrybutu
Grupa atrybutów dla manifestu aplikacji.

|Atrybut|Wartość|
|---|---|
|content|3 atrybuty|
|name|ApplicationManifestAttrGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationManifestAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:anyAttribute processContents="skip"/> <!-- Allow unknown attributes to be used. -->
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="applicationtypename"></a>ApplicationTypeName
Identyfikator typu dla tej aplikacji.

|Atrybut|Wartość|
|---|---|
|name|ApplicationTypeName|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="applicationtypeversion"></a>ApplicationTypeVersion
Wersja tego typu aplikacji, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|ApplicationTypeVersion|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="manifestid"></a>ManifestId
Identyfikator tego manifestu aplikacji, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|ManifestId|
|używanie|opcjonalnie|
|default||
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

## <a name="configoverridesidentifier-attributegroup"></a>ConfigOverridesIdentifier atrybutu
Identyfikuje zastąpienia konfiguracji dla pakietu usługi.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|ConfigOverridesIdentifier|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConfigOverridesIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies configuration overrides for a service package.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServicePackageName" type="xs:string" use="required"/>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="servicepackagename"></a>ServicePackageName

|Atrybut|Wartość|
|---|---|
|name|ServicePackageName|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServicePackageName" type="xs:string" use="required"/>
    
```

#### <a name="rolloutversion"></a>RolloutVersion
Identyfikator wdrożenia, w ramach którego wprowadzono zmiany w elemencie Overrides.

|Atrybut|Wartość|
|---|---|
|name|RolloutVersion|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="connectionstring-attributegroup"></a>Element atrybutu ConnectionString

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|Przekształcon|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString">
                <xs:attribute name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="connectionstring"></a>Przekształcon
Parametry połączenia z kontem usługi Azure Storage. Format: DefaultEndpointsProtocol = https; AccountName = []; AccountKey = []

|Atrybut|Wartość|
|---|---|
|name|Przekształcon|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="containername-attributegroup"></a>ContainerName — atrybut

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|NazwaKontenera|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName">
    <xs:attribute name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="containername"></a>NazwaKontenera
Nazwa kontenera w usłudze Azure Blob Storage, do którego są przekazywane dane.

|Atrybut|Wartość|
|---|---|
|name|NazwaKontenera|
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="datadeletionageindays-attributegroup"></a>DataDeletionAgeInDays atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|DataDeletionAgeInDays|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays">
    <xs:attribute name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="datadeletionageindays"></a>DataDeletionAgeInDays
Liczba dni, po upływie których stare dane zostaną usunięte z tej lokalizacji.

|Atrybut|Wartość|
|---|---|
|name|DataDeletionAgeInDays|
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="isenabled-attributegroup"></a>IsEnabled atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|IsEnabled|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled">
                <xs:attribute name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="isenabled"></a>IsEnabled
Czy jest włączony transfer danych do tego miejsca docelowego. Domyślnie nie jest włączona.

|Atrybut|Wartość|
|---|---|
|name|IsEnabled|
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="levelfilter-attributegroup"></a>LevelFilter atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|LevelFilter|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter">
    <xs:attribute name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="levelfilter"></a>LevelFilter
Poziom, na którym mają być filtrowane zdarzenia ETW. Wszystkie zdarzenia na tym samym lub niższym poziomie niż określony poziom są uwzględniane.

|Atrybut|Wartość|
|---|---|
|name|LevelFilter|
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="namevaluepair-attributegroup"></a>NameValuePair atrybutu
Nazwa i wartość zdefiniowana jako atrybut.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|NameValuePair|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameValuePair">
    <xs:annotation>
      <xs:documentation>Name and Value defined as an attribute.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="name"></a>Nazwa
Nazwa ustawienia, które ma zostać przesłonięte.

|Atrybut|Wartość|
|---|---|
|name|Nazwa|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="value"></a>Wartość
Nowa wartość ustawienia.

|Atrybut|Wartość|
|---|---|
|name|Wartość|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="path-attributegroup"></a>Ścieżka atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|Ścieżka|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path">
                <xs:attribute name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="path"></a>Ścieżka
Ścieżka do udziału plików. Format: plik: []

|Atrybut|Wartość|
|---|---|
|name|Ścieżka|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="relativefolderpath-attributegroup"></a>RelativeFolderPath atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|RelativeFolderPath|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath">
                <xs:attribute name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="relativefolderpath"></a>RelativeFolderPath
Ścieżka do folderu względem katalogu dziennika aplikacji.

|Atrybut|Wartość|
|---|---|
|name|RelativeFolderPath|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="servicemanifestidentifier-attributegroup"></a>ServiceManifestIdentifier atrybutu
Identyfikuje manifest usługi.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|ServiceManifestIdentifier|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies a service manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="servicemanifestname"></a>ServiceManifestName
Nazwa manifestu usługi, do którego odwołuje się odwołanie. Nazwa musi być zgodna z nazwą zadeklarowaną w elemencie servicemanifest manifestu usługi.

|Atrybut|Wartość|
|---|---|
|name|ServiceManifestName|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="servicemanifestversion"></a>ServiceManifestVersion
Wersja manifestu usługi, do którego istnieje odwołanie. Wersja musi być zgodna z wersją zadeklarowaną w manifeście usługi.

|Atrybut|Wartość|
|---|---|
|name|ServiceManifestVersion|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

## <a name="uploadintervalinminutes-attributegroup"></a>UploadIntervalInMinutes atrybutu

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|UploadIntervalInMinutes|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes">
    <xs:attribute name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="uploadintervalinminutes"></a>UploadIntervalInMinutes
Interwał w minutach, w którym dane są przekazywane do tego miejsca docelowego.

|Atrybut|Wartość|
|---|---|
|name|UploadIntervalInMinutes|
|type|XS: ciąg|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="versioneditemattrgroup-attributegroup"></a>VersionedItemAttrGroup atrybutu
Grupa atrybutów dla sekcji dotyczącej wersji w dokumentach ApplicationInstance i servicepackage.

|Atrybut|Wartość|
|---|---|
|content|1 atrybut (y)|
|name|VersionedItemAttrGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedItemAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for versioning sections in ApplicationInstance and ServicePackage documents.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required"/>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="rolloutversion"></a>RolloutVersion

|Atrybut|Wartość|
|---|---|
|name|RolloutVersion|
|type|XS: ciąg|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required"/>
  
```

## <a name="versionedname-attributegroup"></a>VersionedName atrybutu
Grupa atrybutów, która zawiera nazwę i wersję.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|VersionedName|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedName">
    <xs:annotation>
      <xs:documentation>Attribute group that includes a Name and a Version.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="name"></a>Nazwa
Nazwa elementu z wersją.

|Atrybut|Wartość|
|---|---|
|name|Nazwa|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="version"></a>Wersja
Wersja elementu z wersjami, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|Wersja|
|używanie|{1&gt;wymagane&lt;1}|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

