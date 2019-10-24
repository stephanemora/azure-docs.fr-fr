---
title: Activer et créer des partages de fichiers volumineux - Azure Files
description: Dans cet article, vous allez apprendre à activer et à créer des partages de fichiers volumineux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518161"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Guide pratique pour activer et créer des partages de fichiers volumineux

À l’origine, les partages de fichiers standard pouvaient uniquement être mis à l’échelle jusqu’à 5 Tio. Désormais, avec les partages de fichiers volumineux, ils peuvent subir un scale-up jusqu’à 100 Tio. Par défaut, vous pouvez effectuer un scale-up des partages de fichiers Premium jusqu’à 100 Tio. 

Pour un scale-up jusqu’à 100 Tio à l’aide de partages de fichiers standard, vous devez autoriser votre compte de stockage à utiliser des partages de fichiers volumineux. Vous pouvez autoriser un compte existant ou créer un nouveau compte pour utiliser des partages de fichiers volumineux.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="restrictions"></a>Restrictions

Les comptes activés pour les partages de fichiers volumineux prennent en charge LRS ou ZRS. Pour l’instant, les comptes activés pour les partages de fichiers volumineux ne prennent pas en charge GZRS, GRS ou RA-GRS. L’activation des partages de fichiers volumineux sur un compte est un processus irréversible. Une fois l’activation effectuée, votre compte ne peut pas être converti en GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage

Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste des ressources, tapez **Comptes de stockage**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Comptes de stockage**.
1. Sur la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.
1. Sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sous le champ **Groupe de ressources**, sélectionnez **Créer**. Entrez un nom pour votre nouveau groupe de ressources, comme indiqué dans l’image suivante.

    ![Capture d’écran montrant comment créer un groupe de ressources sur le portail](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez un emplacement pour votre compte de stockage et vérifiez qu’il s’agit de l’[une des régions prises en charge pour LFS](storage-files-planning.md#regional-availability).
1. Affectez **Stockage localement redondant** ou **Stockage redondant interzone** comme valeur pour la réplication.
1. Laissez ces champs définis sur leur valeur par défaut :

   |Champ  |Valeur  |
   |---------|---------|
   |Modèle de déploiement     |Gestionnaire de ressources         |
   |Performances     |standard         |
   |Type de compte     |StorageV2 (usage général v2)         |
   |Niveau d’accès     |À chaud         |

1. Sélectionnez **Avancé**, puis **Activé** pour **Partages de fichiers volumineux**.
1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.

    ![large-file-shares-advanced-enable.png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Sélectionnez **Create** (Créer).

## <a name="enable-on-existing-account"></a>Activer sur un compte existant

Vous pouvez également activer les partages de fichiers volumineux sur des comptes existants. Dans ce cas, le compte ne pourra plus être converti en GZRS, GRS ou RA-GRS. Ce choix est irréversible sur ce compte.

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez au compte de stockage sur lequel vous souhaitez activer les partages de fichiers volumineux.
1. Ouvrez le compte de stockage et sélectionnez **Configuration**.
1. Sélectionnez **Activé** pour **Partages de fichiers volumineux**, puis sélectionnez Enregistrer.
1. Sélectionnez **Vue d’ensemble** puis **Actualiser**.

![enable-large-file-shares-on-existing.png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage.

Si vous recevez l’erreur suivante : « Les grands partages de fichiers ne sont pas encore disponibles pour le compte. » Vous pouvez patienter un peu, car votre région est probablement en cours de déploiement, ou, si vous avez des besoins urgents, contactez le support technique.

## <a name="create-a-large-file-share"></a>Créer un partage de fichiers volumineux

La création d’un partage de fichiers volumineux est presque identique à la création d’un partage de fichiers standard. La principale différence est que vous pouvez définir un quota allant jusqu’à 100 Tio.

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Sélectionnez **+ Partage de fichiers**.
1. Entrez un nom pour votre partage de fichiers et (éventuellement) la taille de quota souhaitée, jusqu’à 100 Tio, puis sélectionnez **Créer**. 

![large-file-shares-create-share.png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Étendre des partages de fichiers existants

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez étendre des partages existants au quota supérieur.

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Cliquez avec le bouton droit sur votre partage de fichiers et sélectionnez **Quota**.
1. Entrez la nouvelle taille souhaitée, puis sélectionnez **OK**.

![update-large-file-share-quota.png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Étapes suivantes

* [Connexion et montage du partage de fichiers – Windows](storage-how-to-use-files-windows.md)
* [Connexion et montage du partage de fichiers – Linux](../storage-how-to-use-files-linux.md)
* [Connexion et montage du partage de fichiers – MacOS](storage-how-to-use-files-mac.md)