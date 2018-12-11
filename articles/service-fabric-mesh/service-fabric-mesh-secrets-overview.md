---
title: Stocker et utiliser des secrets d’application Azure Service Fabric Mesh | Microsoft Docs
description: Stockez et utilisez des secrets d’application Service Fabric Mesh.
services: service-fabric-mesh
keywords: secrets
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891880"
---
# <a name="service-fabric-mesh-application-secrets"></a>Secrets d’application Service Fabric Mesh
Service Fabric Mesh prend en charge les secrets comme des ressources Azure. Un secret Service Fabric Mesh peut correspondre à n’importe quel type de texte sensible, comme des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs devant être stockées et transmises de manière sécurisée.

![Présentation des secrets Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ressources Secrets Mesh
Un secret d’application Mesh comprend :
* Une ressource **Secrets**, qui est un conteneur où sont stockés les secrets texte. Les secrets contenus dans la ressource **Secrets** sont stockés et transmis de manière sécurisée.
* Une ou plusieurs ressources **Secrets/Valeurs** qui sont stockées dans le conteneur de ressources **Secrets**. Chaque ressource **Secrets/Valeurs** se distingue par son numéro de version.

## <a name="inline-or-stored-in-azure-key-vault"></a>Incluses ou stockées dans Azure Key Vault
- Les applications et les ressources de service Mesh comprennent Managed Service Identity (MSI) avec Azure Active Directory (AAD) afin de pouvoir accéder aux secrets contenus dans Azure Key Vault.
- Les secrets et les certificats peuvent être automatiquement renouvelés à l’aide de stratégies.

## <a name="next-steps"></a>Étapes suivantes 
Pour plus d’informations sur les secrets Service Fabric Mesh, consultez :
- [Gérer les secrets d’application Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Présentation du modèle de ressource Azure Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
