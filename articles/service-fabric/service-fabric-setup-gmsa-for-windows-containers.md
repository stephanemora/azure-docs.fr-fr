---
title: Configurer des comptes de service administrés de groupe pour les services de conteneur Azure Service Fabric
description: Découvrez comment configurer des comptes de service administrés de groupe pour un conteneur s’exécutant dans Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639205"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurer un GSMA pour des conteneurs Windows s’exécutant sur Service Fabric

Pour configurer un gMSA (compte de service géré de groupe), un fichier de spécification des informations d’identification (`credspec`) est placé sur tous les nœuds du cluster. Le fichier peut être copié sur tous les nœuds à l’aide d’une extension de machine virtuelle.  Le fichier `credspec` doit contenir les informations de compte gMSA. Pour plus d’informations sur le fichier `credspec`, voir [Créer une spécification d’informations d’identification](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). La spécification d’informations d’identification et la balise `Hostname` sont spécifiées dans le manifeste d’application. La balise `Hostname` doit correspondre au nom de compte gMSA dans lequel le conteneur s’exécute.  La balise `Hostname` permet au conteneur de s’authentifier auprès d’autres services dans le domaine à l’aide de l’authentification Kerberos.  Un exemple de spécification des balises `Hostname` et `credspec` du manifeste d’application est indiqué dans l’extrait de code suivant :

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Comme prochaine étape, lisez les articles suivants :

* [Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-get-started-containers.md)
* [Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)
