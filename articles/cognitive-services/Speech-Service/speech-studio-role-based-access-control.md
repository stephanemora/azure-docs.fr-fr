---
title: Contrôle d’accès en fonction du rôle dans Speech Studio - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment affecter des rôles d’accès au service Speech via Speech Studio.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: pafarley
ms.openlocfilehash: 72d2acd25381af654fb098d81a7cdc4ec0bbce22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700700"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Contrôle d’accès en fonction du rôle Azure dans Speech Studio 

Speech Studio prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC), système d’autorisation pour la gestion des accès individuels aux ressources Azure. En utilisant Azure RBAC, vous pouvez affecter des niveaux d’autorisations différents pour vos opérations Speech Studio à différents membres de l’équipe. Pour plus d’informations sur Azure RBAC, consultez la [documentation Azure RBAC](/azure/role-based-access-control/overview).

## <a name="prerequisites"></a>Prérequis

* Vous devez être connecté à Speech Studio avec votre compte Azure et votre ressource Speech. Consultez [Vue d’ensemble de Speech Studio](speech-studio-overview.md).

## <a name="manage-role-assignments-for-speech-resources"></a>Gérer les attributions de rôles pour les ressources Speech

Pour accorder l’accès à une ressource Azure Speech, vous ajoutez une attribution de rôle via l’outil Azure RBAC dans le portail Azure. 

Au bout de quelques minutes, le rôle sélectionné est attribué à la cible dans l’étendue sélectionnée. Pour recevoir del’aide relative à ces étapes, consultez [Attribuer des rôles Azure à l’aide du Portail Azure](/azure/role-based-access-control/role-assignments-portal?tabs=current).

## <a name="supported-built-in-roles-in-speech-studio"></a>Rôles intégrés pris en charge dans Speech Studio

Une définition de rôle est un ensemble d’autorisations. Utilisez les rôles intégrés recommandés suivants si vous n’avez pas de besoins personnalisés spécifiques pour les autorisations :

| **Rôle intégré** | **Autorisation de lister les clés des ressources** | **Autorisation pour les opérations Custom Speech** | **Autorisation pour les opérations Custom Voice**| **Autorisation pour d’autres fonctionnalités** |
| ---| ---| ---| ---| --|
|**Propriétaire** |Oui |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès total |
|**Contributeur** |Oui |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès total |
|**Contributeurs du service cognitif** |Oui |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès total |
|**Utilisateur du service cognitif** |Oui |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès total |
|**Contributeurs du service cognitif Speech** |Non |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès complet aux projets, y compris l’autorisation de créer, modifier ou supprimer un projet / des données / un modèle / des points de terminaison |Accès total |
|**Utilisateur du service cognitif Speech** |Non |Peut visualiser les projets/ jeux de données / modèles / points de terminaison ; ne peut pas créer, modifier supprimer |Peut visualiser les projets/ jeux de données / modèles / points de terminaison ; ne peut pas créer, modifier supprimer |Accès total |
|**Lecteur de données Cognitive Services (préversion)** |Non |Peut visualiser les projets/ jeux de données / modèles / points de terminaison ; ne peut pas créer, modifier supprimer |Peut visualiser les projets/ jeux de données / modèles / points de terminaison ; ne peut pas créer, modifier supprimer |Accès total |

Vous pouvez aussi [créer une définition de rôle personnalisé](/azure/role-based-access-control/custom-roles). Par exemple, vous pouvez créer un rôle personnalisé avec l’autorisation de charger des jeux de données vocaux personnalisés, mais sans avoir la possibilité de déployer un modèle vocal personnalisé sur un point de terminaison.

> [!NOTE]
> Speech Studio prend en charge l’authentification basée sur des clés. Les rôles qui ont l’autorisation de lister les clés des ressources (`Microsoft.CognitiveServices/accounts/listKeys/action`) seront tout d’abord authentifiés avec une clé de ressource et auront un accès complet aux opérations de Speech Studio, à condition que l’authentification par clé soit activée dans le portail Azure. Si l’authentification par clé est désactivée par l’administrateur du service, ces rôles perdent tous les accès à Studio.

> [!NOTE]
> Une ressource peut être affectée de plusieurs rôles ou hériter de ceux-ci, et le niveau d’accès final à cette ressource est une combinaison de des autorisations de tous vos rôles du niveau de l’opération.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Chiffrement des données au repos du service Speech](/azure/cognitive-services/speech-service/speech-encryption-of-data-at-rest).