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
ms.date: 05/20/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeeb7b2704474e030e00eda03e73fd523434a207
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976116"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Que sont les identités managées pour les ressources Azure ?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

En tant que développeur cloud, vous recherchez probablement le moyen le plus simple et le plus sûr d’accéder aux ressources Azure dans votre code. 

Les identités managées pour les ressources Azure peuvent vous y aider, car elles :

- **Éliminent** le besoin d’informations d’identification dans votre code.
- **Effectuent la rotation** automatiquement des informations d’identification.
- **Réduisent** au minimum votre implication dans la gestion des identités.


## <a name="how-it-works"></a>Fonctionnement 

Toutes les ressources Azure qui prennent en charge les identités managées peuvent obtenir des jetons permettant d’échanger des données sans présenter d’informations d’identification dans le code. Le processus se déroule de la façon suivante :

 
1.  **Activation** : créer l’identité managée pour la ressource.
2.  **Octroi de l’accès** : autoriser l’accès aux ressources avec Azure RBAC.
3.  **Accès** : effectuer les actions autorisées.
4.  **Désactiver** : supprimer l’identité managée. 

## <a name="managed-identity-types"></a>Types d’identités managées

Il existe deux types d’identités administrées :

- Identité managée affectée par le système

- Identité managée affectée par l’utilisateur


Pour les ressources Azure autonomes, vous pouvez activer **les identités managées** affectées par le système. Les identités managées affectées par le système offrent une prise en charge extrêmement pratique du point de vue de la gestion des identités. En un seul clic, vous pouvez activer la gestion automatisée du cycle de vie d’une identité pour votre ressource.   

 ![Identité managée affectée par le système](./media/overview/system-assigned.png)  

Alors que les identités managées affectées par le système offrent une solution des plus pratiques pour les ressources autonomes, les choses semblent différentes s’il est question de gérer un groupe de ressources Azure pour la même tâche. Dans ce scénario, il est préférable de créer une identité manuellement, puis d’affecter cette identité principale à toutes les ressources Azure que vous devez regrouper. Cette affectation est appelée identité managée **affectée par l’utilisateur**. 
  

## <a name="supported-services"></a>Services pris en charge

Vous pouvez utiliser les identités managées pour les ressources Azure dans l’authentification auprès des services prenant en charge l’authentification Azure AD. Pour obtenir la liste des services Azure qui prennent en charge la fonctionnalité d’identités managées pour les ressources Azure, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](services-support-msi.md).


## <a name="next-steps"></a>Étapes suivantes

Bien démarrer avec la fonctionnalité des identités managées pour les ressources Azure grâce aux guides de démarrage rapide suivants :

* [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
