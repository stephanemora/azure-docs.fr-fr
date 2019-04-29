---
title: Guide pratique pour spécifier des variables d’environnement pour des services dans Azure Service Fabric | Microsoft Docs
description: Vous montre comment utiliser des variables d’environnement pour des applications dans Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720227"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Guide pratique pour spécifier des variables d’environnement pour des services dans Service Fabric

Cet article vous explique comment spécifier des variables d’environnement pour un service ou un conteneur dans Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procédure permettant de spécifier des variables d’environnement pour des services

Dans cet exemple, vous définissez une variable d’environnement pour un conteneur. Cet article suppose que vous disposez déjà d’un manifeste d’application et de service.

1. Ouvrez le fichier ServiceManifest.xml.
1. Dans l’élément `CodePackage`, ajoutez un nouveau élément `EnvironmentVariables` et un élément `EnvironmentVariable` pour chaque variable d’environnement.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Les variables d’environnement peuvent être remplacées dans le manifeste de l’application.

1. Pour remplacer les variables d’environnement dans le manifeste de l’application, utilisez l’élément `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur certains des principaux concepts abordés dans cet article, consultez les articles [Gérer des applications pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).