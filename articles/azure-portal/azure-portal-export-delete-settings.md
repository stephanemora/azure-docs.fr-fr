---
title: Exporter ou supprimer des paramètres du portail Azure | Microsoft Docs
description: Découvrez comment vous pouvez exporter ou supprimer vos paramètres utilisateur, les tableaux de bord privés et les paramètres personnalisés dans le portail Azure.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551677"
---
# <a name="export-or-delete-user-settings"></a>Exporter ou supprimer des paramètres utilisateur

Vous pouvez utiliser des paramètres et des fonctionnalités dans le portail Azure pour créer une expérience personnalisée. Informations sur vos paramètres personnalisés sont stockées dans Azure. Vous pouvez exporter ou supprimer les données utilisateur suivantes :

* Tableaux de bord privés dans le portail Azure
* Paramètres de l’utilisateur comme les abonnements Favoris ou de répertoires et de dernier répertoire connecté
* Thèmes et autres paramètres de portail personnalisés

Il est judicieux d’exporter et passez en revue vos paramètres avant de les supprimer. Reconstruction des tableaux de bord ou de rétablissement des paramètres personnalisés peut prendre du temps.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exporter ou supprimer vos paramètres de portail

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans l’en-tête du portail, sélectionnez **paramètres**.

    ![Capture d’écran qui affiche que l’icône d’engrenage du portail Azure](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Sélectionnez **Exporter tous les paramètres** ou **Supprimer tous les paramètres et les tableaux de bord privés**.

    ![Capture d’écran montrant le portail, exporter et supprimer des paramètres](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      Le tableau suivant décrit ces actions.

      | Action | Description |
      | --- | --- |
      | **Exporter tous les paramètres** | Crée un fichier .json qui contient vos paramètres utilisateur tels que votre thème de couleur, les Favoris et les tableaux de bord privés.|
      | **Supprimer tous les paramètres et les tableaux de bord privés** | Supprime tous les liens vers des tableaux de bord privés et d’autres paramètres personnalisés que vous avez apportées au portail. |

> [!NOTE]
> En raison de la nature dynamique des paramètres utilisateur et les risques d’altération des données, vous ne pouvez pas importer des paramètres à partir du fichier .json.
>
>


## <a name="next-steps"></a>Étapes suivantes

* [Créer et partager des tableaux de bord Azure](azure-portal-dashboard-share-access.md)
* [Ajouter, supprimer et trier les Favoris](azure-portal-add-remove-sort-favorites.md)
