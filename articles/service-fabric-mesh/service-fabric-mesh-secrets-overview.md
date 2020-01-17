---
title: Stocker et utiliser des secrets d’application Azure Service Fabric Mesh
description: Service Fabric Mesh prend en charge les secrets comme des ressources Azure. Voici comment stocker et gérer les secrets avec des applications Service Fabric Mesh.
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: d45b9e98c1f325e5dbd656c85655a4ac72e4c3e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459112"
---
# <a name="service-fabric-mesh-application-secrets"></a>Secrets d’application Service Fabric Mesh
Service Fabric Mesh prend en charge les secrets comme des ressources Azure. Un secret Service Fabric Mesh peut correspondre à n’importe quel type de texte sensible, comme des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs devant être stockées et transmises de manière sécurisée.

![Présentation des secrets Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ressources Secrets Mesh
Un secret d’application Mesh comprend :
* Une ressource **Secrets**, qui est un conteneur où sont stockés les secrets texte. Les secrets contenus dans la ressource **Secrets** sont stockés et transmis de manière sécurisée.
* Une ou plusieurs ressources **Secrets/Valeurs** qui sont stockées dans le conteneur de ressources **Secrets**. Chaque ressource **Secrets/Valeurs** se distingue par son numéro de version.

## <a name="next-steps"></a>Étapes suivantes 
Pour plus d’informations sur les secrets Service Fabric Mesh, consultez :
- [Gérer les secrets d’application Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Présentation du modèle de ressource Azure Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
