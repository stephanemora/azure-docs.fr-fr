---
title: Stocker et utiliser des secrets d’application Azure Service Fabric Mesh
description: Service Fabric Mesh prend en charge les secrets comme des ressources Azure. Voici comment stocker et gérer les secrets avec des applications Service Fabric Mesh.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625530"
---
# <a name="service-fabric-mesh-application-secrets"></a>Secrets d’application Service Fabric Mesh

> [!IMPORTANT]
> La préversion d’Azure Service Fabric Mesh a été mise hors service. Les nouveaux déploiements par le biais de l’API Service Fabric Mesh ne seront plus autorisés. La prise en charge des déploiements existants se poursuivra jusqu’au 28 avril 2021.
> 
> Pour plus d’informations, consultez [Mise hors service de la préversion d’Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

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
