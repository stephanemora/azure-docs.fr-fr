---
title: Comment supprimer et exporter des données personnelles à partir d’Azure DevTest Labs | Microsoft Docs
description: Découvrez comment supprimer et exporter des données personnelles à partir du service Azure DevLast Labs pour prendre en charge vos obligations dans le cadre du Règlement général sur la protection des données (RGPD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394901"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exporter ou supprimer des données personnelles à partir d’Azure DevTest Labs
Cet article fournit des étapes pour supprimer et exporter des données personnelles à partir du service Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quelles sont les données personnelles collectées par DevTest Labs ?
DevTest Labs collecte deux types principaux de données personnelles auprès de l’utilisateur. Ce sont : l’adresse e-mail de l’utilisateur et l’ID de l’objet utilisateur. Ces informations sont essentielles pour permettre au service de fournir des fonctionnalités intégrées pour les administrateurs de laboratoire et les utilisateurs de laboratoire.

### <a name="user-email-address"></a>Adresse e-mail de l’utilisateur
DevTest Labs utilise l’adresse e-mail de l’utilisateur pour envoyer des notifications par e-mail d’arrêt automatique aux utilisateurs de laboratoire. L’e-mail notifie les utilisateurs de l’arrêt de leur ordinateur. Les utilisateurs peuvent retarder ou ignorer l’arrêt s’ils le souhaitent. Vous configurez l’adresse e-mail au niveau du laboratoire ou au niveau de la machine virtuelle.

**Définition de l’e-mail au niveau du laboratoire :**

![Définition de l’e-mail au niveau du laboratoire](./media/personal-data-delete-export/lab-user-email.png)

**Définition de l’e-mail au niveau de la machine virtuelle :**

![Définition de l’e-mail au niveau de la machine virtuelle](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID de l’objet utilisateur
DevTest Labs utilise l’ID de l’objet utilisateur pour afficher les tendances de coût d’un mois à l’autre et des informations sur le coût par ressource pour les administrateurs de laboratoire. Cela leur permet d’effectuer le suivi des coûts et de gérer les seuils pour leur laboratoire. 

**Tendance de coût estimé pour le mois calendaire actuel :**
![Tendance de coût estimé pour le mois calendaire actuel](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Coût en cumul mensuel à ce jour estimé par ressource :**
![Coût en cumul mensuel à ce jour estimé par ressource](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Pourquoi avons-nous besoin de ces données personnelles ?
Le service DevTest Labs utilise les données personnelles à des fins opérationnelles. Ces données sont essentielle pour permettre au service de fournir des fonctionnalités clés. Si vous définissez une stratégie de rétention sur l’adresse e-mail de l’utilisateur, les utilisateurs de laboratoire ne reçoivent pas les notifications par e-mail d’arrêt automatique en temps voulu après que leur adresse e-mail a été supprimée de notre système. De même, l’administrateur de laboratoire ne peut pas afficher les tendances de coût d’un mois sur l’autre et le coût par ressource pour les machines dans leurs laboratoires si les ID de l’objet utilisateur sont supprimés selon une stratégie de rétention. Par conséquent, ces données doivent être conservées tant que la ressource de l’utilisateur est active dans le laboratoire.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Comment faire pour que le système oublie mes données personnelles ?
En tant qu’utilisateur de laboratoire, si vous voulez que ces données personnelles soient supprimées, vous devez supprimer la ressource correspondante dans le laboratoire. Le service DevTest Labs rend anonymes les données personnelles supprimées 30 jours après leur suppression par l’utilisateur.

Par exemple, si vous supprimez votre machine virtuelle, ou votre e-mail, le service DevTest Labs rend ces données anonymes 30 jours après la suppression de la ressource. La stratégie de rétention de 30 jours après la suppression permet de s’assurer que nous fournissons une projection précise du coût d’un mois sur l’autre à l’administrateur de laboratoire.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Comment puis-je demander une exportation de mes données personnelles ?
En tant qu’utilisateur de laboratoire, vous pouvez demander une exportation des données personnelles stockées par le service DevTest Labs. Pour demander une exportation, accédez à l’option **Données personnelles** sur la page **Vue d’ensemble** de votre laboratoire. Sélectionnez le bouton **Demander l’exportation** pour lancer la création d’un fichier Excel téléchargeable dans le compte de stockage de l’administrateur de laboratoire. Vous pouvez ensuite contacter votre administrateur de laboratoire pour consulter ces données.

1. Sélectionnez **Données personnelles** dans le menu de gauche. 

    ![Page Données personnelles](./media/personal-data-delete-export/personal-data-page.png)
2. Sélectionnez le **groupe de ressources** qui contient le laboratoire.

    ![Sélection du groupe de ressources](./media/personal-data-delete-export/select-resource-group.png)
3. Sélectionnez le **compte de stockage** dans le groupe de ressources.
4. Sur la page du **compte de stockage**, sélectionnez **Blobs**.

    ![Sélectionner la vignette Blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Sélectionnez le conteneur nommé **labresourceusage** dans la liste des conteneurs.

    ![Sélectionner le conteneur blob](./media/personal-data-delete-export/select-blob-container.png)
6. Sélectionnez le **dossier** nommé d’après votre laboratoire. Dans ce dossier, vous trouverez des fichiers **csv** pour les **disques** et les **machines virtuelles** dans votre laboratoire. Vous pouvez télécharger ces fichiers csv, filtrer le contenu pour l’utilisateur de laboratoire demandant l’accès et le partager avec lui.

    ![Télécharger le fichier CSV](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Définir des stratégies pour un laboratoire](devtest-lab-get-started-with-lab-policies.md)
- [Forum Aux Questions](devtest-lab-faq.md)
