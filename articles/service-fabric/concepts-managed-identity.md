---
title: Présentation des identités managées Service Fabric
description: Cet article présente Managed Identity et ses applications dans Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: dc7dafa59596537456accde66e878c06f9e5ca23
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528145"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Identité managée pour l’application Service Fabric (préversion)

La gestion des informations d’identification dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation des informations d’identification est une tâche importante, car elles n’apparaissent jamais sur les stations de travail de développeurs et ne sont pas archivées dans le contrôle de code source. La fonctionnalité Identité managée pour les ressources Azure dans Azure Active Directory (Azure AD) résout ce problème. Elle fournit aux services Azure une identité système administrée automatiquement dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

La fonctionnalité Identité managée pour les ressources Azure est gratuite avec Azure AD pour les abonnements Azure. Aucun coût supplémentaire n’est facturé.

> [!NOTE]
> Identité managée pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="terminology"></a>Terminologie

Les termes suivants sont utilisés dans l'ensemble de la documentation Identité managées pour les ressources Azure :

- **ID client** : identificateur unique généré par Azure AD lié à une application et à un principal de service lors de son approvisionnement initial (consultez également [ID d’application](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **ID du principal** : l’ID d’objet de l’objet du principal de service pour votre identité managée, utilisé pour octroyer un accès basé sur le rôle à une ressource Azure.

- **Principal du service** : un objet Azure Active Directory, qui représente la projection d’une application AAD dans un abonné donné (consultez également [principal du service](../active-directory/develop/developer-glossary.md#service-principal-object).)


## <a name="about-managed-identities-in-azure"></a>À propos des Identités managées dans Azure

- [Types d’identité managée (MI) dans Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Comment fonctionne l’Identité managée attribuée par le système dans Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Comment fonctionne l’Identité managée définie par l’utilisateur dans Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Scénarios pris en charge pour les applications Service Fabric

Les identités managées pour Service Fabric sont prises en charge uniquement dans les clusters Service Fabric déployés dans Azure et uniquement pour les applications déployées en tant que ressources Azure. Une application qui n’est pas déployée en tant que ressource Azure ne peut pas se voir attribuer une identité. En théorie, le support des identités managées dans un cluster Azure Service Fabric est constitué de deux phases :

1. Attribuez une ou plusieurs identités managées à la ressource d’application ; une application peut se voir attribuer une seule identité attribuée par le système et/ou des identités attribuées par des utilisateurs, jusqu’à 32, respectivement.

2. Dans la définition de l’application, mappez l’une des identités attribuées à l’application à un service individuel comprenant l’application.

L’identité attribuée par le système d’une application est propre à cette application ; une identité attribuée par l’utilisateur est une ressource autonome, qui peut être attribuée à plusieurs applications. Dans une application, une seule identité (qu’elle soit attribuée par le système ou par l’utilisateur) peut être attribuée à plusieurs services de l’application, mais chaque service individuel ne peut être attribué qu’à une seule identité. Enfin, une identité doit être attribuée explicitement à un service pour pouvoir accéder à cette fonctionnalité. En effet, le mappage des identités d’une application à ses services constitutifs permet une isolation dans l’application : un service peut uniquement utiliser l’identité qui lui est mappée (et aucune dans le cas contraire.)  

La liste des scénarios pris en charge pour la mise en production de la préversion est la suivante :

   - Déployer une nouvelle application avec un ou plusieurs services et une ou plusieurs identités attribuées

   - Attribuer une ou plusieurs identités managées à une application existante afin d’accéder aux ressources Azure ; l’application doit avoir été déployée en tant que ressource Azure elle-même


Les scénarios suivants ne sont pas pris en charge ni recommandés ; notez que ces actions peuvent ne pas être bloquées, mais peuvent entraîner des pannes dans vos applications :

   - Supprimez ou modifiez les identités attribuées à une application. Si vous devez apporter des modifications, envoyez des déploiements distincts pour ajouter d’abord une nouvelle attribution d’identité, puis en supprimer une précédemment attribuée. La suppression d’une identité d’une application existante peut avoir des effets indésirables, notamment la sortie de votre application dans un état qui ne peut pas être mis à niveau. Il est sans danger de supprimer entièrement l’application si la suppression d’une identité est nécessaire ; notez que cette opération supprimera l’identité attribuée par le système (si définie) associée à l’application et supprimera toutes les associations avec les identités attribuées par l’utilisateur et attribuées à l’application.

   - La prise en charge de SF pour les identités managées n’est pour l’heure pas intégrée à [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md) ; l’intégration sera effective d’ici la fin de la période de préversion de la fonctionnalité d’identité managée.

>
> [!NOTE]
>
> Cette fonctionnalité est en préversion ; en tant que telle, elle peut être soumise à des modifications fréquentes et peut ne pas convenir à des déploiements de production.

## <a name="next-steps"></a>Étapes suivantes
* [Déployer un nouveau cluster Azure Service Fabric avec un support d’identité managée](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Activer le support d’identités managées dans un cluster Azure Service Fabric existant](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par le système](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par l’utilisateur](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Tirer parti de l’identité managée d’une application Service Fabric à partir du code de service](./how-to-managed-identity-service-fabric-app-code.md)
* [Accorder à une application Azure Service Fabric l’accès à d’autres ressources Azure](./how-to-grant-access-other-resources.md)
* [Déclaration et utilisation de secrets d’application en tant que KeyVaultReferences](./service-fabric-keyvault-references.md) 
