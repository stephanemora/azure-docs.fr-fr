---
title: Configurer un GMSA pour les services de conteneur Azure Service Fabric | Documents Microsoft
description: Découvrez comment configurer un GMSA pour un conteneur en cours d’exécution dans Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: ae8c5c8ec1e16669b3cbdde8b3eaa3d5dbb7c4de
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668549"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurer un GSMA pour des conteneurs Windows s’exécutant sur Service Fabric

Pour configurer un gMSA (compte de service géré de groupe), un fichier de spécification des informations d’identification (`credspec`) est placé sur tous les nœuds du cluster. Le fichier peut être copié sur tous les nœuds à l’aide d’une extension de machine virtuelle.  Le fichier `credspec` doit contenir les informations de compte gMSA. Pour plus d’informations sur la `credspec` de fichiers, consultez [créer une spécification d’informations d’identification](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). La spécification d’informations d’identification et la balise `Hostname` sont spécifiées dans le manifeste d’application. La balise `Hostname` doit correspondre au nom de compte gMSA dans lequel le conteneur s’exécute.  La balise `Hostname` permet au conteneur de s’authentifier auprès d’autres services dans le domaine à l’aide de l’authentification Kerberos.  Un exemple de spécification des balises `Hostname` et `credspec` du manifeste d’application est indiqué dans l’extrait de code suivant :

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
