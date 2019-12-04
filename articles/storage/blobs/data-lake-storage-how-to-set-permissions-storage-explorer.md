---
title: 'Explorateur Stockage Azure : Gérer l’accès dans Azure Data Lake Storage Gen2'
description: Dans cette procédure, vous allez apprendre à définir des autorisations avec l’Explorateur Stockage Azure sur des fichiers et des répertoires à l’intérieur de votre compte de stockage compatible Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b34103e521def678acce17e3292e04fca95b5e6e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327980"
---
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Utiliser l’Explorateur Stockage Azure pour gérer les accès dans Azure Data Lake Storage Gen2

Les fichiers stockés dans Azure Data Lake Storage Gen2 prennent en charge les autorisations de granularité fine et la gestion de la liste de contrôle d’accès (ACL). Ensemble, les autorisations de granularité fine et la gestion ACL vous permettent de gérer l’accès à vos données à un niveau très précis.

Dans cet article, vous apprenez à utiliser l’Explorateur de stockage Azure pour :

> [!div class="checklist"]
> * Définir des autorisations au niveau des fichiers
> * Définir des autorisations au niveau des répertoires
> * Ajouter des utilisateurs ou des groupes à une liste de contrôle d’accès

## <a name="prerequisites"></a>Prérequis

Afin de mieux décrire le processus, nous vous demandons de terminer notre [guide de démarrage rapide pour l’explorateur de stockage Azure](data-lake-storage-Explorer.md). Cela garantit que votre compte de stockage sera dans l’état le plus approprié (conteneur créé et données chargées dedans).

## <a name="managing-access"></a>Gestion de l’accès

Vous pouvez définir des autorisations à la racine de votre conteneur. Pour ce faire, vous devez être connecté à l’Explorateur de stockage Azure avec votre compte spécifique disposant des droits adaptés (par opposition à une chaîne de connexion). Cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Gérer les autorisations**, ce qui fait apparaître la boîte de dialogue **Gérer les autorisations**.

![Explorateur de stockage Microsoft Azure - Gérer l’accès au répertoire](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

La boîte de dialogue **Gérer les autorisations** vous permet de gérer les autorisations pour le propriétaire et le groupe de propriétaires. Elle vous permet également d’ajouter de nouveaux utilisateurs et groupes à la liste de contrôle d’accès pour lesquels vous pouvez ensuite gérer les autorisations.

Pour ajouter un nouvel utilisateur ou un nouveau groupe à la liste de contrôle d’accès, sélectionnez le champ **Ajouter un utilisateur ou groupe**.

Entrez l’entrée Azure Active Directory (AAD) correspondante que vous souhaitez ajouter à la liste, puis sélectionnez **Ajouter**.

L’utilisateur ou le groupe apparaît alors dans le champ **Utilisateurs et groupes**, ce qui vous permet de commencer à gérer leurs autorisations.

> [!NOTE]
> Créer un groupe de sécurité dans AAD et mettre à jour les autorisations au niveau du groupe plutôt qu’au niveau des utilisateurs individuels est recommandé et constitue une meilleure pratique. Pour plus d’informations sur cette recommandation ainsi que sur les autres meilleures pratiques, consultez [Meilleures pratiques pour Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Il existe deux catégories d’autorisations pouvant être attribuées : les ACL d’accès et les ACL par défaut.

* **Accès** : Les ACL d’accès contrôlent l’accès à un objet. Les fichiers et les répertoires ont tous des ACL d’accès.

* **Par défaut** : Un modèle d’ACL associées à un répertoire, qui détermine les ACL d’accès pour tous les éléments enfants créés dans ce répertoire. Les fichiers n’ont pas d’ACL par défaut.

Dans ces deux catégories, il existe trois autorisations, que vous pouvez ensuite attribuer à des fichiers ou des répertoires : **Lire**, **écrire** et **exécuter**.

>[!NOTE]
> Les sélections faites ici ne définiront pas d’autorisations sur tous les éléments existants actuellement au sein du répertoire. Vous devez accéder à chaque élément et définir les autorisations manuellement, si le fichier existe déjà.

Vous pouvez gérer les autorisations sur des répertoires individuels, ainsi que sur des fichiers individuels, permettant un contrôle d’accès précis. Le processus de gestion des autorisations pour les répertoires et les fichiers est identique à celui décrit ci-dessus. Faites un clic droit sur le fichier ou le répertoire dont vous souhaitez gérer les autorisations et suivez le même processus.

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure, vous avez appris à définir des autorisations sur des fichiers et des répertoires à l’aide de **l’Explorateur de stockage Azure**. Pour en savoir plus sur les ACL, y compris les ACL par défaut, les ACL d’accès, leur comportement et leurs autorisations, continuez en consultant notre article conceptuel dessus.

> [!div class="nextstepaction"]
> [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
