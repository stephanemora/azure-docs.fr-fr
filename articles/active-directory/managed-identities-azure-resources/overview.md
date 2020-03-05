---
title: Identités gérées pour les ressources Azure
description: Une vue d’ensemble des identités managées pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a721d1e486010e22927512985611cccdd99f4505
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78248317"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Que sont les identités managées pour les ressources Azure ?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

La gestion des informations d’identification dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement et ne sont jamais archivées dans le contrôle de code source. Azure Key Vault permet de stocker en toute sécurité des informations d’identification, secrets, et autres clés, mais votre code doit s’authentifier sur Key Vault pour les récupérer.

La fonctionnalité des identités managées pour les ressources Azure dans Azure Active Directory (Azure AD) résout ce problème. Elle fournit aux services Azure une identité système administrée automatiquement dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

La fonctionnalité des identités managées pour les ressources Azure est gratuite avec Azure AD pour les abonnements Azure. Aucun coût supplémentaire n’est facturé.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="terminology"></a>Terminologie

Les termes suivants sont utilisés dans l'ensemble de la documentation sur les identités gérées pour les ressources Azure :

- **ID client** : identificateur unique généré par Azure AD qui est lié à une application et à un principal de service lors de son provisionnement initial.
- **ID du principal** : ID d’objet du principal de service pour votre identité managée ; il est utilisé pour accorder un accès basé sur les rôles à une ressource Azure.
- **Instance Metadata Service (IMDS) Azure** : point de terminaison REST accessible à toutes les machines virtuelles IaaS créées par le biais d’Azure Resource Manager. Le point de terminaison est disponible sur une adresse IP non routable bien connue (169.254.169.254) accessible uniquement à partir de la machine virtuelle.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Comment fonctionnent les identités managées pour les ressources Azure ?

Il existe deux types d’identités administrées :

- Une **identité managée attribué par le système** est activée directement sur une instance de service Azure. Lorsque l’identité est activée, Azure crée une identité pour l’instance dans le locataire Azure AD approuvé par l’abonnement de l’instance. Une fois l’identité créée, ses informations d’identification sont provisionnées sur l’instance. Le cycle de vie d’une identité attribuée par le système est directement lié à l’instance de service Azure sur laquelle elle est activée. Si l’instance est supprimée, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.
- Une **identité managée attribuée par l’utilisateur** est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure. Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée.

En interne, des identités managées sont des principaux de service d’un type spécial, qui sont verrouillés pour être utilisés uniquement avec les ressources Azure. Lorsqu’une identité managée est supprimée, le principal de service correspondant est automatiquement supprimé.

Votre code peut utiliser une identité managée pour faire une demande de jetons d’accès pour les services qui prennent en charge l’authentification Azure AD. Azure prend en charge la restauration des informations d’identification utilisées par l’instance de service.

Le diagramme suivant illustre le fonctionnement des identités de service administré avec les machines virtuelles (VM) Azure :

![Identités de service administré et machines virtuelles Azure](media/overview/msi-vm-vmextension-imds-example.png)

|  Propriété    | Identité managée affectée par le système | Identité managée affectée par l’utilisateur |
|------|----------------------------------|--------------------------------|
| Création |  Créé dans le cadre d’une ressource Azure (par exemple, une machine virtuelle Azure ou Azure App Service) | Créé en tant que ressource Azure autonome |
| Cycle de vie | Cycle de vie partagé entre la ressource Azure et l’identité managée avec laquelle elle est créée. <br/> Lorsque la ressource parente est supprimée, l’identité managée l’est également. | Cycle de vie indépendant. <br/> Doit être explicitement supprimé. |
| Partage entre ressources Azure | Ne peut pas être partagé. <br/> Ne peut être associé qu’à une seule ressource Azure. | Peut être partagé <br/> Une même identité managée affectée par l’utilisateur peut être associée à plusieurs ressources Azure. |
| Cas d’utilisation courants | Charges de travail contenues dans une même ressource Azure <br/> Charges de travail pour lesquelles vous avez besoin d’identités indépendantes. <br/> Par exemple, une application qui s’exécute sur une seule machine virtuelle | Charges de travail qui s’exécutent sur plusieurs ressources et qui peuvent partager une même identité. <br/> Charges de travail qui ont besoin d’une autorisation préalable pour accéder à une ressource sécurisée dans le cadre d’un flux de provisionnement. <br/> Charges de travail dont les ressources sont recyclées fréquemment, mais pour lesquelles les autorisations doivent rester les mêmes. <br/> Par exemple, une charge de travail où plusieurs machines virtuelles doivent accéder à la même ressource |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Fonctionnement d’une identité managée attribuée par le système avec une machine virtuelle Azure

1. Azure Resource Manager reçoit une requête pour activer l’identité managée attribuée par le système sur une machine virtuelle.

2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité de la machine virtuelle. Le principal de service est créé dans le locataire Azure AD approuvé par cet abonnement.

3. Azure Resource Manager configure l’identité sur la machine virtuelle en mettant à jour le point de terminaison d’identité Azure Instance Metadata Service avec l’ID client et le certificat du principal de service.

4. Maintenant que la machine virtuelle possède une identité, utilisez les informations du Principal de service pour accorder aux ressources Azure l’accès à la machine virtuelle. Pour appeler Azure Resource Manager, utilisez le contrôle d’accès en fonction du rôle (RBAC) dans Azure AD pour attribuer le rôle approprié au principal de service de la machine virtuelle. Pour appeler Key Vault, accordez à votre code un accès au secret spécifique ou à la clé dans Key Vault.

5. Votre code en cours d’exécution sur la machine virtuelle peut demander un jeton à partir du point de terminaison d’Azure Instance Metadata Service, accessible uniquement à partir de la machine virtuelle : `http://169.254.169.254/metadata/identity/oauth2/token`
    - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.
    - Le paramètre de version d’API spécifie la version IMDS, utilisez api-version=2018-02-01 ou version ultérieure.

6. Un appel est passé à Azure AD pour demander un jeton d’accès (comme indiqué à l’étape 5), à l’aide de l’ID client et du certificat configurés à l’étape 3. Azure AD renvoie un jeton d’accès JSON Web Token (JWT).

7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Fonctionnement d’une identité managée attribuée par l’utilisateur avec une machine virtuelle Azure

1. Azure Resource Manager reçoit une requête pour créer une identité managée attribuée par l’utilisateur.

2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité managée attribuée par l’utilisateur. Le principal de service est créé dans le locataire Azure AD approuvé par cet abonnement.

3. Azure Resource Manager reçoit une requête pour configurer l’identité managée affectée par l’utilisateur sur une machine virtuelle, et mettre à jour le point de terminaison d’identité Azure Instance Metadata Service avec l’ID client et le certificat du principal de service de l’identité managée affectée par l’utilisateur.

4. Maintenant que l’identité managée attribuée par l’utilisateur a été créée, utilisez les informations du Principal de service pour lui accorder l’accès aux ressources Azure. Pour appeler Azure Resource Manager, utilisez RBAC dans Azure AD pour attribuer le rôle approprié au principal de service de l’Identité attribuée par l’utilisateur. Pour appeler Key Vault, accordez à votre code un accès au secret spécifique ou à la clé dans Key Vault.

   > [!Note]
   > Vous pouvez également effectuer cette étape avant l’étape 3.

5. Votre code en cours d’exécution sur la machine virtuelle peut demander un jeton à partir du point de terminaison d’identité d’Azure Instance Metadata Service, accessible uniquement à partir de la machine virtuelle : `http://169.254.169.254/metadata/identity/oauth2/token`
    - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.
    - Le paramètre ID client spécifie l’identité pour laquelle le jeton est demandé. Cela est nécessaire pour lever l’ambiguïté lorsque plusieurs identités attribuées par l’utilisateur se trouvent sur une même machine virtuelle.
    - Le paramètre de version d’API spécifie la version d’Azure Instance Metadata Service. Utilisez la version `api-version=2018-02-01` ou ultérieure.

6. Un appel est passé à Azure AD pour demander un jeton d’accès (comme indiqué à l’étape 5), à l’aide de l’ID client et du certificat configurés à l’étape 3. Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Comment puis-je utiliser des identités managées pour les ressources Azure ?

Pour découvrir comment utiliser les identités managées pour accéder aux différentes ressources Azure, essayez ces didacticiels.

> [!NOTE]
> Consultez le cours [Implementing Managed Identities for Microsoft Azure Resources](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) pour plus d’informations sur les identités managées, y compris des vidéos de procédures détaillées relatives à plusieurs scénarios pris en charge.

Découvrez comment utiliser une identité managée avec une machine virtuelle Windows :

* [Accéder à Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Accéder à SQL Azure](tutorial-windows-vm-access-sql.md)
* [Accéder au stockage Azure à l’aide d’une clé d’accès](tutorial-vm-windows-access-storage.md)
* [Accéder au stockage Azure à l’aide d’une signature d’accès partagé](tutorial-windows-vm-access-storage-sas.md)
* [Accéder à une ressource non Azure AD avec Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Découvrez comment utiliser une identité managée avec une machine virtuelle Linux :

* [Accéder à Azure Container Registry](../../container-registry/container-registry-authentication-managed-identity.md)
* [Accéder à Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Accéder au stockage Azure à l’aide d’une clé d’accès](tutorial-linux-vm-access-storage.md)
* [Accéder au stockage Azure à l’aide d’une signature d’accès partagé](tutorial-linux-vm-access-storage-sas.md)
* [Accéder à une ressource non Azure AD avec Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Découvrez comment utiliser une identité managée avec d’autres services Azure :

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Gestion des API Azure](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry Tasks](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Data Factory](../../data-factory/data-factory-service-identity.md).


## Quels sont les services Azure qui prennent en charge la fonctionnalité ?<a name="which-azure-services-support-managed-identity"></a>

Les identités managées pour les ressources Azure peuvent servir à l’authentification auprès des services prenant en charge l’authentification Azure AD. Pour obtenir la liste des services Azure qui prennent en charge la fonctionnalité d’identités managées pour les ressources Azure, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](services-support-msi.md).

## <a name="next-steps"></a>Étapes suivantes

Bien démarrer avec la fonctionnalité des identités managées pour les ressources Azure grâce aux guides de démarrage rapide suivants :

* [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
