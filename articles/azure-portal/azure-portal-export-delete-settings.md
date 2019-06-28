---
title: Exporter ou supprimer des paramètres du portail Azure | Microsoft Docs
description: Découvrez comment vous pouvez exporter ou supprimer vos paramètres utilisateur, des tableaux de bord privés et des paramètres personnalisés dans le portail Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551677"
---
# <a name="export-or-delete-user-settings"></a>Exporter ou supprimer des paramètres utilisateur

Vous pouvez utiliser différents paramètres et différentes fonctionnalités dans le portail Azure pour créer une expérience personnalisée. Les informations sur vos paramètres personnalisés sont stockées dans Azure. Vous pouvez exporter ou supprimer les données utilisateur suivantes :

* Les tableaux de bord privés sur le portail Azure
* Les paramètres utilisateur, comme les abonnements ou les répertoires de favoris et le répertoire de la dernière ouverture de session
* Les thèmes et autres paramètres personnalisés du portail

Nous vous recommandons d’exporter et de vérifier vos paramètres avant de les supprimer. En effet, le recréation des tableaux de bord ou des paramètres personnalisés peut prendre du temps.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exporter ou supprimer vos paramètres du portail

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans l’en-tête du portail, sélectionnez **Paramètres**.

    ![Capture d’écran qui affiche que l’icône d’engrenage du portail Azure](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Sélectionnez **Exporter tous les paramètres** ou **Supprimer tous les paramètres et les tableaux de bord privés**.

    ![Capture d’écran qui montre l’exportation et la suppression des paramètres du portail](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      Le tableau suivant décrit ces actions.

      | Action | Description |
      | --- | --- |
      | **Exporter tous les paramètres** | Crée un fichier .json qui contient vos paramètres utilisateur, comme votre thème, vos favoris et vos tableaux de bord privés.|
      | **Supprimer tous les paramètres et les tableaux de bord privés** | Supprime tous les liens aux tableaux de bord privés et autres paramètres personnalisés que vous avez définis dans le portail. |

> [!NOTE]
> En raison de la nature dynamique des paramètres utilisateur et des risques d’altération des données, vous ne pouvez pas importer de paramètres à partir du fichier .json.
>
>


## <a name="next-steps"></a>Étapes suivantes

* [Créer et partager des tableaux de bord Azure](azure-portal-dashboard-share-access.md)
* [Ajouter, supprimer et trier des favoris](azure-portal-add-remove-sort-favorites.md)
