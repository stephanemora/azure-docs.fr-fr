---
title: Exporter ou supprimer des paramètres du portail Azure
description: Découvrez comment vous pouvez exporter ou supprimer vos paramètres utilisateur, des tableaux de bord privés et des paramètres personnalisés dans le portail Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900749"
---
# <a name="export-or-delete-user-settings"></a>Exporter ou supprimer des paramètres utilisateur

Vous pouvez utiliser différents paramètres et différentes fonctionnalités dans le portail Azure pour créer une expérience personnalisée. Les informations sur vos paramètres personnalisés sont stockées dans Azure. Vous pouvez exporter ou supprimer les données utilisateur suivantes :

* Les tableaux de bord privés sur le portail Azure
* Les paramètres utilisateur, comme les abonnements ou les répertoires de favoris et le répertoire de la dernière ouverture de session
* Les thèmes et autres paramètres personnalisés du portail

Nous vous recommandons d’exporter et de vérifier vos paramètres avant de les supprimer. En effet, le recréation des tableaux de bord ou des paramètres personnalisés peut prendre du temps.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exporter ou supprimer vos paramètres du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans l’en-tête du portail, sélectionnez l'![icône des paramètres](media/azure-portal-export-delete-settings/settings-icon.png) **Paramètres**.

1. Sélectionnez **Exporter tous les paramètres** ou **Supprimer tous les paramètres et les tableaux de bord privés**.

    ![Paramètres du portail Azure et options des paramètres](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Le tableau suivant décrit ces actions.

      | Action | Description |
      | --- | --- |
      | **Exporter tous les paramètres** | Crée un fichier *.json* contenant vos paramètres utilisateur, tels que votre thème, vos favoris et vos tableaux de bord privés.|
      | **Supprimer tous les paramètres et les tableaux de bord privés** | Supprime tous les liens aux tableaux de bord privés et autres paramètres personnalisés que vous avez définis dans le portail. |

> [!NOTE]
> En raison de la nature dynamique des paramètres utilisateur et des risques d’altération des données, vous ne pouvez pas importer de paramètres à partir du fichier *.json*.
>
>

## <a name="next-steps"></a>Étapes suivantes

* [Partager des tableaux de bord Azure à l’aide d’un contrôle d’accès en fonction du rôle](azure-portal-dashboard-share-access.md)
* [Ajouter, supprimer et réorganiser des favoris](azure-portal-add-remove-sort-favorites.md)
