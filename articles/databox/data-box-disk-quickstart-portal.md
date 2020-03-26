---
title: Démarrage rapide pour Microsoft Azure Data Box Disk | Microsoft Docs
description: Utilisez ce guide de démarrage rapide pour déployer rapidement votre disque Azure Data Box dans le portail Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fcc7c6ff74e17db2066d97597849c985f5a961e9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76514066"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Démarrage rapide : Déployer Azure Data Box Disk à l’aide du portail Azure

Ce démarrage rapide explique comment déployer le disque Azure Data Box à l’aide du portail Azure. Vous y apprendrez notamment à créer une commande, recevoir des disques, déballer, vous connecter et copier des données vers des disques pour effectuer un chargement vers Azure.

Pour obtenir des instructions pas à pas sur le déploiement et le suivi, consultez [Tutoriel : Commander Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

Ce guide vous décrit les étapes relatives à l’utilisation d’Azure Data Box Disk dans le portail Azure. Ce guide contribue à répondre aux questions suivantes.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Prérequis

Avant de commencer :

- Assurez-vous que votre abonnement est activé pour le service Azure Data Box. Pour activer votre abonnement pour ce service, [inscrivez-vous pour le service](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Passer en revue les prérequis** : Vérifiez le nombre de disques et de câbles, le système d’exploitation et les logiciels nécessaires.
> - **Connecter et déverrouiller** : Connectez l’appareil et déverrouillez le disque pour copier les données.
> - **Copier les données sur le disque et valider l’opération** : Copiez les données sur les disques dans les dossiers créés au préalable.
> - **Retourner les disques** : Retournez les disques au centre de données Azure où les données sont chargées sur votre compte de stockage.
> - **Vérifier les données dans Azure** : Vérifiez que vos données ont été chargées sur votre compte de stockage avant de les supprimer du serveur de données sources.

::: zone-end


::: zone target="docs"

## <a name="order"></a>JSON

Cette étape prend environ 5 minutes.

1. Créez une ressource Azure Data Box dans le portail Azure. 
2. Sélectionnez un abonnement activé pour ce service et choisissez le type de transfert **Importation**. Fournissez le **pays source** où résident les données et la **région de destination Azure** pour le transfert des données.
3. Sélectionnez **Data Box Disk**. La capacité maximale de la solution est de 35 To, et vous pouvez créer plusieurs commandes de disque pour des tailles de données supérieures.  
4. Entrez les détails de la commande et les informations d’expédition. Si le service est disponible dans votre région, fournissez les adresses e-mail de notification, vérifiez le résumé, puis créez la commande.

Une fois la commande créée, les disques sont préparés pour l’expédition.

## <a name="unpack"></a>Déballer

Cette étape prend environ 5 minutes.

Le Data Box Disk est envoyé dans un colis UPS Express. Ouvrez la boîte et vérifiez qu’elle contient :

- 1 à 5 disques USB protégés par du film à bulles.
- Un câble de connexion par disque.
- Une étiquette de retour.

## <a name="connect-and-unlock"></a>Connecter et déverrouiller

Cette étape prend environ 5 minutes.

1. Utilisez le câble inclus pour connecter le disque à un ordinateur Windows/Linux sous une version prise en charge. Pour plus d’informations sur les versions de système d’exploitation prises en charge, accédez à [Azure Data Box Disk system requirements](data-box-disk-system-requirements.md) (Conditions requises pour Azure Data Box Disk). 
2. Pour déverrouiller le disque :

    1. Dans le portail Azure, accédez à **Général > Détails de l’appareil** et obtenez la clé d’accès.
    2. Téléchargez et extrayez l’outil de déverrouillage Data Box Disk propre au système d’exploitation sur l’ordinateur utilisé pour copier les données sur des disques. 
    3. Exécutez l’outil de déverrouillage Data Box Disk et indiquez la clé d'accès. Pour toute réinsertion de disque, réexécutez l’outil de déverrouillage et fournissez la clé d’accès. **N’utilisez pas la boîte de dialogue BitLocker ni la clé BitLocker pour déverrouiller le disque.** Pour plus d’informations sur le déverrouillage de disques, accédez aux rubriques [Déverrouiller les disques sur le client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Déverrouiller les disques sur le client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. La lettre de lecteur affectée au disque est affichée par l’outil. Notez la lettre de lecteur du disque. Elle est utilisée dans les étapes suivantes.

## <a name="copy-data-and-validate"></a>Copier les données et valider

Le temps nécessaire à cette opération dépend de la taille de vos données.

1. Le lecteur contient les dossiers *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* et *DataBoxDiskImport*. Effectuez un glisser-déplacer pour copier les données qui doivent être importées en tant qu’objets blob de blocs dans le dossier *BlockBlob*. De même, glissez-déplacez des données telles que VHD/VHDX vers le dossier *PageBlob* et les données vers *AzureFile*. Copiez les disques durs virtuels que vous souhaitez charger en tant que disques managés dans un dossier sous *ManagedDisk*.

    Un conteneur est créé dans le compte de stockage Azure de chaque sous-dossier sous les dossiers *BlockBlob* et *PageBlob*. Un partage de fichiers est créé pour un sous-dossier sous *AzureFile*.

    Tous les fichiers sous les dossiers *BlockBlob* et *PageBlob* sont copiés dans un conteneur par défaut `$root` dans le compte de stockage Azure. Copiez les fichiers dans un dossier dans *AzureFile*. Tous les fichiers copiés directement dans le dossier *AzureFile* échouent et sont chargés en tant qu’objets blob de blocs.

    > [!NOTE]
    > - Tous les conteneurs, objets blob et fichiers doivent être conformes aux [conventions d’affectation de noms Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Si ces règles ne sont pas respectées, le chargement des données vers Azure échoue.
    > - Assurez-vous qu’ils ne dépassent pas ~4,75 Tio pour les objets blob de blocs, ~ 8 Tio pour les objets blob de pages et ~1 Tio pour Azure Files.

2. **(Facultatif, mais recommandé)**  Une fois la copie terminée, nous vous conseillons au minimum d’exécuter `DataBoxDiskValidation.cmd` fourni dans le dossier *DataBoxDiskImport*. Sélectionnez l’option 1 pour valider les fichiers. Nous vous recommandons également, si le temps le permet, d’utiliser l’option 2 pour générer également des sommes de contrôle pour la validation (cela peut prendre un certain temps en fonction de la taille des données). Ces étapes réduisent les risques d’échec lors du chargement des données dans Azure.
3. Retirez en toute sécurité le lecteur.

## <a name="ship-to-azure"></a>Envoyer à Azure

Cette étape prend environ 5 à 7 minutes.

1. Rassemblez tous les disques dans le colis d’origine. Utilisez l’étiquette d’expédition incluse. Si l’étiquette est endommagée ou a été perdue, téléchargez-la à partir du portail. Accédez à **Vue d’ensemble** et cliquez sur **Télécharger une étiquette d’expédition** à partir de la barre de commandes.
2. Déposez le colis scellé au point de dépôt.  

Le service Data Box Disk envoie une notification par e-mail et met à jour l’état de la commande dans le portail Azure.

## <a name="verify-your-data"></a>Vérifier vos données

Le temps nécessaire à cette opération dépend de la taille de vos données.

1. Lorsque le disque Data Box est connecté au réseau du centre de données Azure, le chargement des données vers Azure démarre automatiquement.
2. Le service Azure Data Box vous avertit que la copie des données est terminée via le portail Azure.
    
    1. Vérifiez les journaux d’activité d’erreurs pour connaître les échecs éventuels et prendre les mesures appropriées.
    2. Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Cette étape dure 2 à 3 minutes.

Pour effectuer une suppression, vous pouvez annuler la commande Data Box puis la supprimer.

- Vous pouvez annuler la commande Data Box dans le portail Azure avant son traitement. Une fois que la commande est traitée, elle ne peut plus être annulée. La commande suit une progression jusqu’à la phase finale.

    Pour annuler la commande, accédez à **Vue d’ensemble** et cliquez sur **Annuler** à partir de la barre de commandes.  

- Vous pouvez supprimer la commande une fois que l’état **Terminé** ou **Annulé** s’affiche dans le portail Azure.

    Pour supprimer la commande, accédez à **Vue d’ensemble** et cliquez sur **Supprimer** à partir de la barre de commandes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez déployé le disque Azure Data Box pour faciliter l’importation de vos données dans Azure. Pour en savoir plus sur la gestion du disque Azure Data Box, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box Disk](data-box-portal-ui-admin.md) (Utiliser le portail Azure pour administrer le disque Data Box)

::: zone-end
