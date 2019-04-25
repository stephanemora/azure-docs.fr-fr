---
title: Paramétrer les fichiers de configuration dans Azure Service Fabric | Microsoft Docs
description: Découvrir comment paramétrer les fichiers de configuration dans Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 3d03ca5cec2cef67862c2678b3b0a8f17b413787
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482435"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Guide pratique pour paramétrer les fichiers de configuration dans Service Fabric

Cet article vous explique comment paramétrer un fichier de configuration dans Service Fabric.  Si vous n’êtes pas déjà familiarisé avec les concepts fondamentaux de la gestion des applications pour plusieurs environnements, consultez [Gérer des applications pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procédure de paramétrage des fichiers de configuration

Dans cet exemple, vous remplacez une valeur de configuration à l’aide de paramètres dans le déploiement de votre application.

1. Ouvrez le  *\<MyService > \PackageRoot\Config\Settings.xml* fichier dans votre projet de service.
1. Définissez un nom de paramètre de configuration et une valeur, par exemple la taille du cache égale à 25, en ajoutant le code XML suivant :

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Enregistrez et fermez le fichier.
1. Ouvrez le  *\<MyApplication > \ApplicationPackageRoot\ApplicationManifest.xml* fichier.
1. Dans le fichier ApplicationManifest.xml, déclarez une valeur de paramètre et une valeur par défaut dans l’élément `Parameters`.  Il est recommandé que le nom du paramètre contienne le nom du service (par exemple, « MyService »).

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Dans la section `ServiceManifestImport` du fichier ApplicationManifest.xml, ajoutez un élément `ConfigOverride`, qui référence le package de configuration, la section et le paramètre.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Si vous ajoutez un élément ConfigOverride, Service Fabric choisit toujours les paramètres d’application ou la valeur par défaut spécifiée dans le manifeste d’application.
>
>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les autres fonctionnalités de gestion d’application disponibles dans Visual Studio, consultez la section [Gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).