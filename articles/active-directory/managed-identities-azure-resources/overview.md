---
title: Identités gérées pour les ressources Azure
description: Une vue d’ensemble des identités managées pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/23/2018
ms.author: daveba
ms.openlocfilehash: e025d9041358fbb9dee9b64519e012c4c1988024
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987258"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Que sont les identités gérées pour les ressources Azure ?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

La gestion des informations d’identification dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement et ne sont jamais archivées dans le contrôle de code source. Azure Key Vault permet de stocker en toute sécurité des informations d’identification, secrets, et autres clés, mais votre code doit s’authentifier sur Key Vault pour les récupérer. 

La fonctionnalité des identités managées pour les ressources Azure dans Azure Active Directory (Azure AD) résout ce problème. Elle fournit aux services Azure une identité système administrée automatiquement dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

La fonctionnalité des identités managées pour les ressources Azure est gratuite avec Azure AD pour les abonnements Azure. Aucun coût supplémentaire n’est facturé.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## Principe de la fonctionnalité<a name="how-does-it-work"></a>

Il existe deux types d’identités administrées :

- Une **identité managée attribué par le système** est activée directement sur une instance de service Azure. Lorsque l’identité est activée, Azure crée une identité pour l’instance dans le locataire Azure AD approuvé par l’abonnement de l’instance. Une fois l’identité créée, ses informations d’identification sont provisionnées sur l’instance. Le cycle de vie d’une identité attribuée par le système est directement lié à l’instance de service Azure sur laquelle elle est activée. Si l’instance est supprimée, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.
- Une **identité managée attribuée par l’utilisateur** est créée en tant que ressource Azure autonome. Via un processus de création, Azure crée une identité dans le locataire Azure AD approuvé par l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure. Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des instances de service Azure auxquelles elle est affectée.

Votre code peut utiliser une identité managée pour faire une demande de jetons d’accès pour les services qui prennent en charge l’authentification Azure AD. Azure prend en charge la restauration des informations d’identification utilisées par l’instance de service.

Le diagramme suivant illustre le fonctionnement des identités de service administré avec les machines virtuelles (VM) Azure :

![Identités de service administré et machines virtuelles Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Fonctionnement d’une identité managée attribuée par le système avec une machine virtuelle Azure

1. Azure Resource Manager reçoit une requête pour activer l’identité managée attribuée par le système sur une machine virtuelle.
2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité de la machine virtuelle. Le principal de service est créé dans le locataire Azure AD approuvé par cet abonnement.
3. Azure Resource Manager configure l’identité sur la machine virtuelle :
    1. Met à jour le point de terminaison d’identité Azure Instance Metadata Service avec l’ID client et le certificat du Principal de service.
    1. Provisionne l’extension de machine virtuelle (dont l’abandon est prévu en janvier 2019), et ajoute l’ID client et le certificat du principal de service. (Cette étape sera bientôt abandonnée).
4. Maintenant que la machine virtuelle possède une identité, utilisez les informations du Principal de service pour accorder aux ressources Azure l’accès à la machine virtuelle. Pour appeler Azure Resource Manager, utilisez le contrôle d’accès en fonction du rôle (RBAC) dans Azure AD pour attribuer le rôle approprié au principal de service de la machine virtuelle. Pour appeler Key Vault, accordez à votre code un accès au secret spécifique ou à la clé dans Key Vault.
5. Votre code en cours d’exécution sur la machine virtuelle peut demander un jeton à partir de deux points de terminaison qui sont uniquement accessibles à partir de la machine virtuelle :

    - Point de terminaison d’identité Azure Instance Metadata Service (recommandé) : `http://169.254.169.254/metadata/identity/oauth2/token`
        - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.
        - Le paramètre de version d’API spécifie la version IMDS, utilisez api-version=2018-02-01 ou version ultérieure.
    - Point de terminaison d’extension de machine virtuelle (dont l’abandon est prévu en janvier 2019) : `http://localhost:50342/oauth2/token` 
        - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.

6. Un appel est passé à Azure AD pour demander un jeton d’accès (comme indiqué à l’étape 5), à l’aide de l’ID client et du certificat configurés à l’étape 3. Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Fonctionnement d’une identité managée attribuée par l’utilisateur avec une machine virtuelle Azure

1. Azure Resource Manager reçoit une requête pour créer une identité managée attribuée par l’utilisateur.
2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité managée attribuée par l’utilisateur. Le principal de service est créé dans le locataire Azure AD approuvé par cet abonnement.
3. Azure Resource Manager reçoit une requête pour configurer l’identité managée attribuée par l’utilisateur sur une machine virtuelle :
    1. Met à jour le point de terminaison d’identité Azure Instance Metadata Service avec l’ID client et le certificat du Principal de service de l’identité managée attribuée par l’utilisateur.
    1. Provisionne l’extension de machine virtuelle et ajoute l’ID client et le certificat du Principal de service de l’identité managée attribuée par l’utilisateur. (Cette étape sera bientôt abandonnée).
4. Maintenant que l’identité managée attribuée par l’utilisateur a été créée, utilisez les informations du Principal de service pour lui accorder l’accès aux ressources Azure. Pour appeler Azure Resource Manager, utilisez RBAC dans Azure AD pour attribuer le rôle approprié au principal de service de l’Identité attribuée par l’utilisateur. Pour appeler Key Vault, accordez à votre code un accès au secret spécifique ou à la clé dans Key Vault.

   > [!Note]
   > Vous pouvez également effectuer cette étape avant l’étape 3.

5. Votre code en cours d’exécution sur la machine virtuelle peut demander un jeton à partir de deux points de terminaison qui sont uniquement accessibles à partir de la machine virtuelle :

    - Point de terminaison d’identité Azure Instance Metadata Service (recommandé) : `http://169.254.169.254/metadata/identity/oauth2/token`
        - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.
        - Le paramètre ID client spécifie l’identité pour laquelle le jeton est demandé. Cela est nécessaire pour lever l’ambiguïté lorsque plusieurs identités attribuées par l’utilisateur se trouvent sur une même machine virtuelle.
        - Le paramètre de version d’API spécifie la version d’Azure Instance Metadata Service. Utilisez la version `api-version=2018-02-01` ou ultérieure.

    - Point de terminaison d’extension de machine virtuelle (dont l’abandon est prévu en janvier 2019) : `http://localhost:50342/oauth2/token`
        - Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Pour vous authentifier à Azure Resource Manager, utilisez `resource=https://management.azure.com/`.
        - Le paramètre ID client spécifie l’identité pour laquelle le jeton est demandé. Cela est nécessaire pour lever l’ambiguïté lorsque plusieurs identités attribuées par l’utilisateur se trouvent sur une même machine virtuelle.
6. Un appel est passé à Azure AD pour demander un jeton d’accès (comme indiqué à l’étape 5), à l’aide de l’ID client et du certificat configurés à l’étape 3. Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Comment puis-je utiliser des identités managées pour les ressources Azure ?

Pour découvrir comment utiliser les identités managées pour accéder aux différentes ressources Azure, essayez ces didacticiels.

Découvrez comment utiliser une identité managée avec une machine virtuelle Windows :

* [Accéder à Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Accéder à SQL Azure](tutorial-windows-vm-access-sql.md)
* [Accéder au stockage Azure à l’aide d’une clé d’accès](tutorial-windows-vm-access-storage.md)
* [Accéder au stockage Azure à l’aide d’une signature d’accès partagé](tutorial-windows-vm-access-storage-sas.md)
* [Accéder à une ressource non Azure AD avec Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Découvrez comment utiliser une identité managée avec une machine virtuelle Linux :

* [Accéder à Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Accéder au stockage Azure à l’aide d’une clé d’accès](tutorial-linux-vm-access-storage.md)
* [Accéder au stockage Azure à l’aide d’une signature d’accès partagé](tutorial-linux-vm-access-storage-sas.md)
* [Accéder à une ressource non Azure AD avec Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Découvrez comment utiliser une identité managée avec d’autres services Azure :

* [Azure App Service](/azure/app-service/app-service-managed-service-identity)
* [Azure Functions](/azure/app-service/app-service-managed-service-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Gestion des API Azure](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)

## Quels sont les services Azure qui prennent en charge la fonctionnalité ?<a name="which-azure-services-support-managed-identity"></a>

Les identités managées pour les ressources Azure peuvent servir à l’authentification auprès des services prenant en charge l’authentification Azure AD. Pour obtenir la liste des services Azure qui prennent en charge la fonctionnalité d’identités managées pour les ressources Azure, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](services-support-msi.md).

## <a name="next-steps"></a>Étapes suivantes

Bien démarrer avec la fonctionnalité des identités managées pour les ressources Azure grâce aux guides de démarrage rapide suivants :

* [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
