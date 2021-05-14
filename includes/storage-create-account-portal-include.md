---
title: Fichier include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718159"
---
Pour créer un compte de stockage à usage général v2 dans le portail Azure, procédez comme suit :

1. Dans le menu du portail Azure, sélectionnez **Tous les services**. Dans la liste des ressources, tapez **Comptes de stockage**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Comptes de stockage**.
1. Dans la fenêtre **Comptes de stockage** qui s’affiche, choisissez **+Nouveau**.
1. Dans le panneau **Informations de base**, sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sous le champ **Groupe de ressources** , sélectionnez le groupe de ressources désiré ou créez-en un.  Pour plus d’informations sur les groupes de ressources Azure, consultez l’article [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez la région de votre compte de stockage, ou utilisez la région par défaut.
1. Sélectionnez un niveau de performances. Le niveau par défaut est *Standard*.
1. Spécifiez la méthode de réplication du compte de stockage. L’option de redondance par défaut est le *stockage géoredondant (GRS)* . Pour plus d’informations sur les options de réplication disponibles, consultez [Redondance de Stockage Azure](../articles/storage/common/storage-redundancy.md).
1. Des options supplémentaires sont disponibles sous les onglets **Avancé**, **Réseau**, **Protection des données** et **Étiquettes**. Pour utiliser Azure Data Lake Storage, choisissez le panneau **Avancé**, puis affectez la valeur **Activé** à **Espace de noms hiérarchiques**. Pour plus d’informations, consultez [Introduction à d’Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.
1. Sélectionnez **Create** (Créer).

L’illustration suivante montre les paramètres du panneau **Informations de base** d’un nouveau compte de stockage :

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Capture d’écran montrant comment créer un compte de stockage dans le portail Azure." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
