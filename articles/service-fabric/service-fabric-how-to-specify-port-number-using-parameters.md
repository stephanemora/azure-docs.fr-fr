---
title: Spécifier le numéro de port d’un service à l’aide de paramètres
description: Vous montre comment utiliser des paramètres afin de spécifier le port pour une application dans Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609857"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Guide pratique pour spécifier le numéro de port d’un service à l’aide de paramètres dans Service Fabric

Cet article vous explique comment spécifier le numéro de port d’un service à l’aide de paramètres dans Service Fabric à l’aide de Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procédure permettant de spécifier le numéro de port d’un service à l’aide de paramètres

Dans cet exemple, vous définissez le numéro de port pour votre API web ASP.NET Core à l’aide d’un paramètre.

1. Ouvrez Visual Studio et créez une nouvelle application Service Fabric.
1. Choisissez le modèle ASP.NET Core sans état.
1. Choisissez l’API Web.
1. Ouvrez le fichier ServiceManifest.xml.
1. Notez le nom du point de terminaison spécifié pour votre service. La valeur par défaut est `ServiceEndpoint`.
1. Ouvrez le fichier ApplicationManifest.xml
1. Dans l’élément `ServiceManifestImport`, ajoutez un nouvel élément `RessourceOverrides` avec une référence au point de terminaison dans votre fichier ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Dans l’élément `Endpoint`, vous pouvez désormais remplacer n’importe quel attribut à l’aide d’un paramètre. Dans cet exemple, vous spécifiez `Port` et lui attribuer un nom de paramètre à l’aide de crochets, par exemple, `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Toujours dans le fichier ApplicationManifest.xml, spécifiez ensuite le paramètre dans l’élément `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Puis définissez un élément `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Ouvrez le dossier ApplicationParameters et le fichier `Cloud.xml`
1. Pour spécifier un autre port à utiliser lors de la publication sur un cluster distant, ajoutez le paramètre avec le numéro de port à ce fichier.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Lors de la publication de votre application depuis Visual Studio à l’aide du profil de publication Cloud.xml, votre service est configuré pour utiliser le port 80. Si vous déployez l’application sans spécifier le paramètre MyWebAPI_PortNumber, le service utilise le port 8080.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur certains des principaux concepts abordés dans cet article, consultez les articles [Gérer des applications pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
