---
title: Définir la configuration du service dans StartupServices.xml pour une application Service Fabric
description: Découvrez comment utiliser StartupServices.xml pour séparer la configuration du niveau de service d’ApplicationManifest.xml.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 2b11e1dfdfec357d48ee95cabb35c87e71123bc8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482149"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>Présentation de StartupServices.xml dans l’application Service Fabric
Cette fonctionnalité introduit le fichier StartupServices.xml dans une conception d’application Service Fabric. Ce fichier héberge la section DefaultServices d’ApplicationManifest.xml. Avec cette implémentation, les paramètres relatifs à la définition de DefaultServices et de Service sont déplacés du fichier ApplicationManifest.xml existant vers ce nouveau fichier appelé StartupServices.xml. Ce fichier est utilisé dans chaque fonctionnalité (Compiler/Régénérer/F5/Ctrl+F5/Publier) dans Visual Studio.

## <a name="existing-service-fabric-application-design"></a>Conception d’application Service Fabric existante
Pour chaque application Service Fabric, ApplicationManifest.xml est la source de toutes les informations relatives au service pour l’application. ApplicationManifest.xml se compose de Parameters, de ServiceManifestImport et de DefaultServices. Les paramètres de configuration sont mentionnés dans les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters.

Lorsqu’un nouveau service est ajouté à une application, pour ces nouveaux paramètres de service, les sections ServiceManifestImport et DefaultServices sont ajoutées dans ApplicationManifest.xml. Les paramètres de configuration sont ajoutés dans les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters.

Quand l’utilisateur clique sur la fonction Compiler/Régénérer dans Visual Studio, la modification des sections ServiceManifestImport, Parameters et DefaultServices se produit dans ApplicationManifest.xml. Les paramètres de configuration sont modifiés dans les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters.

Quand l’utilisateur déclenche F5/CTRL+F5/Publier, l’application et les services sont déployés ou publiés en fonction des informations contenues dans ApplictionManifest.xml.  Les paramètres de configuration sont utilisés depuis les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters.

![Conception existante pour une application Service Fabric][exisiting-design-diagram]

Exemple de ApplicationManifest.xml 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>Nouvelle conception d’application Service Fabric avec StartupServices.xml
Dans cette conception, il existe une distinction claire entre les informations de niveau de service (par exemple, la définition de Service et les paramètres de Service) et les informations au niveau de l’application (ServiceManifestImport et ApplicationParameters). StartupServices.xml contient toutes les informations relatives au service alors qu’ApplicationManifest.xml contient toutes les informations relatives à l’application. Une autre modification introduite est l’ajout de Cloud.xml/Local1Node.xml/Local5Node.xml sous StartupServiceParameters, qui n’a que la configuration pour les paramètres de service. Le fichier Cloud.xml/Local1Node.xml/Local5Node.xml existant sous ApplicationParameters contient uniquement la configuration des paramètres relatifs à l’application.

Quand un nouveau service est ajouté dans l’application, les paramètres relatifs à l’application et ServiceManifestImport sont ajoutés dans ApplicationManifest.xml. Les paramètres de configuration pour application sont ajoutés dans les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters. Les informations de Service et les paramètres de Service sont ajoutés dans StartupServices.xml, et la configuration des paramètres de service est ajoutée dans Cloud.xml/Local1Node.xml/Local5Node.xml sous StartupServiceParameters.

Lors de compilation/régénération d’un projet, la modification de ServiceManifestImport, les paramètres d’application se produisent dans ApplicationManifest.xml. Les paramètres d’application de configuration sont modifiés dans les fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters. Les informations relatives au service sont modifiées dans StartupServices.xml et les paramètres de Service sont modifiés dans Cloud.xml/Local1Node.xml/Local5Node.xml sous StartupServiceParameters.

Quand F5/CTRL+F5/Publier est déclenché dans Visual Studio, l’application est déployée/publiée en fonction des informations d’ApplictionManifest.xml et des paramètres d’application de l’un des fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous ApplicationParameters. Chaque service est démarré individuellement avec les informations de service de StartupServices.xml et la configuration des paramètres de service à partir des fichiers Cloud.xml/Local1Node.xml/Local5Node.xml sous StartupServiceParameters.

![Nouvelle conception d’application Service Fabric avec StartupServices.xml][new-design-diagram]

Vous pouvez modifier ces paramètres de service et les paramètres d’application avant de publier une application (cliquez avec le bouton droit->Publier), comme indiqué par l’image.

![Publier une option dans une nouvelle conception][publish-application]

Exemple d’ApplicationManifest.xml dans une nouvelle conception
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

Exemple de fichier StartupServices.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

La fonctionnalité startupServices.xml est activée pour tous les nouveaux projets dans le kit de développement logiciel (SDK) version 5.0.516.9590 et versions ultérieures. Pour les services intervenant, cette option est activée dans Microsoft.ServiceFabric.Actors NuGet version 5.0.516 et versions ultérieures. Les projets créés avec une version antérieure du kit de développement logiciel sont entièrement compatibles avec le dernier Kit de développement logiciel (SDK). La migration des anciens projets vers la nouvelle conception n’est pas prise en charge. Si l’utilisateur souhaite créer une application Service Fabric sans StartupServices.xml dans la version la plus récente du kit de développement logiciel (SDK), l’utilisateur doit cliquer sur le lien « Aidez-moi à choisir un modèle de projet », comme montré dans l’image ci-dessous.

![Créer une option de nouvelle application dans une nouvelle conception][create-new-project]


## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [modèles d’applications Service Fabric](service-fabric-application-model.md).
- Découvrez les [Applications Service Fabric et Service Manifests](service-fabric-application-and-service-manifests.md).

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png

