---
title: Résoudre des problèmes liés au contrôle d’accès en fonction du rôle dans Azure | Microsoft Docs
description: Résoudre des problèmes liés au contrôle d’accès en fonction du rôle Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223294"
---
# <a name="troubleshoot-rbac-in-azure"></a>Résoudre des problèmes liés au contrôle d’accès en fonction du rôle dans Azure

Cet article répond aux questions fréquentes sur le contrôle d’accès en fonction du rôle, afin que vous sachiez à quoi vous attendre lorsque vous utilisez les rôles sur le Portail Azure et que vous puissiez résoudre les problèmes d’accès.

## <a name="web-app-features-that-require-write-access"></a>Fonctionnalités d’application web qui nécessitent un accès en écriture

Si vous accordez un accès utilisateur en lecture seule à une seule application web, certaines fonctionnalités sont désactivées, ce que vous n’avez peut-être pas prévu. Les fonctionnalités de gestion suivantes exigent un accès en **écriture** à une application web (Collaborateur ou Propriétaire) et ne sont pas disponibles en lecture seule.

* Commandes (comme start, stop, etc.)
* Modification de paramètres tels que la configuration générale, les paramètres de mise à l’échelle, les paramètres de sauvegarde et les paramètres d’analyse
* Accès aux informations d’identification de publication et autres informations secrètes, telles que les paramètres d’application et les chaînes de connexion
* Diffusion de journaux
* Configuration des journaux de diagnostic
* Console (invite de commandes)
* Déploiements actifs et récents (pour le déploiement continu Git local)
* Estimation de dépense
* Tests web
* Réseau virtuel (accessible à un lecteur uniquement si un réseau virtuel a été précédemment configuré par un utilisateur avec accès en écriture).

Si vous ne pouvez accéder à aucune de ces vignettes, vous devez obtenir l’accès Collaborateur à l’application web auprès de votre administrateur.

## <a name="web-app-resources-that-require-write-access"></a>Ressources d’application web qui nécessitent un accès en écriture

Les applications web sont compliqués par la présence de quelques ressources différentes qui interagissent. Voici un groupe de ressources classique avec plusieurs sites web :

![Groupe de ressources d'application web](./media/troubleshooting/website-resource-model.png)

En conséquence, si vous accordez à un utilisateur le seul accès à l’application web, la plupart des fonctionnalités du volet Site web dans le portail Azure sont désactivées.

Les éléments suivants requièrent l’accès **en écriture** au **plan App Service** qui correspond à votre site web :  

* Affichage du niveau de tarification de l'application web (Gratuit ou Standard)  
* Configuration de mise à l'échelle (le nombre d'instances, la taille de la machine virtuelle, les paramètres de mise à l'échelle automatique)  
* Quotas (stockage, bande passante, UC)  

Les éléments suivants requièrent l’accès **en écriture** à l’ensemble du **Groupe de ressources** qui contient le site web :  

* Certificats et liaisons SSL (les certificats SSL peuvent être partagés entre différents sites dans le même groupe de ressources et emplacement)  
* Règles d'alerte  
* Paramètres de mise à l'échelle automatique  
* Composants Application Insights  
* Tests web  

## <a name="virtual-machine-features-that-require-write-access"></a>Fonctionnalités de machine virtuelle qui nécessitent un accès en écriture

Comme pour les applications web, certaines fonctionnalités du volet de la machine virtuelle exigent un accès en écriture à la machine virtuelle ou à d’autres ressources du groupe de ressources.

Les machines virtuelles sont associées aux noms de domaine, réseaux virtuels, comptes de stockage et règles d'alerte.

Les éléments suivants requièrent l’accès **en écriture** à la **machine virtuelle** :

* Points de terminaison  
* Adresses IP  
* Disques  
* Extensions  

Les éléments suivants requièrent l’accès **en écriture** à la **machine virtuelle**, ainsi qu’au **Groupe de ressources** (avec le nom de domaine) auxquels ils appartiennent :  

* Groupe à haute disponibilité  
* Jeu d'équilibrage de la charge  
* Règles d'alerte  

Si vous ne pouvez accéder à aucune de ces vignettes, demandez l’accès Collaborateur au groupe de ressources à votre administrateur.

## <a name="azure-functions-and-write-access"></a>Azure Functions et accès en écriture

Certaines fonctionnalités de [Azure Functions](../azure-functions/functions-overview.md) nécessitent un accès en écriture. Par exemple, si un utilisateur est assigné au rôle de Lecteur, il ne peut pas voir les fonctions au sein d’une application de fonction. Le portail affiche **(aucun accès)**.

![Aucun accès aux applications de fonction](./media/troubleshooting/functionapps-noaccess.png)

Un lecteur peut cliquer sur l’onglet **Fonctionnalités de plateforme**, puis cliquez sur **Tous les paramètres** pour afficher certains paramètres liés à une application de fonction (semblable à une application Web), mais il ne peut pas modifier ces paramètres.

## <a name="rbac-changes-are-not-being-detected"></a>Les changements de contrôle d’accès en fonction du rôle ne sont pas détectés

Azure Resource Manager met parfois en cache des données et des configurations pour améliorer les performances. Lors de la création ou de la suppression d’attributions de rôles, un délai de 30 minutes maximum peut être nécessaire avant que les modifications soient prises en compte. Si vous utilisez le portail Azure, Azure PowerShell ou Azure CLI, vous pouvez forcer une actualisation de vos modifications d’attribution de rôle en vous déconnectant et en vous reconnectant. Si vous apportez des modifications d’attribution de rôle à l’aide d’appels d’API REST, vous pouvez forcer une actualisation en actualisant votre jeton d’accès.

## <a name="next-steps"></a>Étapes suivantes
* [Manage access using RBAC and the Azure portal](role-assignments-portal.md) (Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du Portail Azure)
* [Afficher les journaux d’activité des changements de contrôle d’accès en fonction du rôle](change-history-report.md)

