---
title: Démarrage rapide pour Microsoft Azure Data Box Disk | Microsoft Docs
description: Utilisez ce guide de démarrage rapide pour déployer rapidement votre disque Azure Data Box dans le portail Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/07/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: b4ec329fc5b1f3df9e6641bee3e1378c3a4d09c6
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378344"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>Démarrage rapide : Déployer le disque Azure Data Box via le portail Azure (préversion)

Ce démarrage rapide explique comment déployer le disque Azure Data Box à l’aide du portail Azure. Vous y apprendrez notamment à créer une commande, recevoir des disques, déballer, vous connecter et copier des données vers des disques pour effectuer un chargement vers Azure. 

Pour obtenir des instructions détaillées sur le déploiement pas à pas, accédez à [Tutorial: Order Azure Data Box Disk](data-box-disk-deploy-ordered.md) (Didacticiel : commander un disque Azure Data Box). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution.

## <a name="prerequisites"></a>Prérequis

Avant de commencer :

- Assurez-vous que votre abonnement est activé pour le service Azure Data Box. Pour activer votre abonnement pour ce service, [inscrivez-vous pour le service](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [http://aka.ms/azuredataboxfromdiskdocs](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Ordre

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
    3. Exécutez l’outil de déverrouillage Data Box Disk et indiquez la clé d'accès. Pour toute réinsertion de disque, réexécutez l’outil de déverrouillage et fournissez la clé d’accès. **N’utilisez pas la boîte de dialogue BitLocker ni la clé BitLocker pour déverrouiller le disque.** Pour plus d’informations sur le déverrouillage de disques, voir [Déverrouiller des disques sur un client Windows]() ou [Déverrouiller des disques sur un client Linux]().
    4. La lettre de lecteur affectée au disque est affichée par l’outil. Notez la lettre de lecteur du disque. Elle est utilisée dans les étapes suivantes.

## <a name="copy-data-and-verify"></a>Copier des données et vérifier

Le temps nécessaire à cette opération dépend de la taille de vos données. 

1. Le lecteur contient les dossiers *PageBlob*, *BlockBlob* et *AzureImportExport*. Effectuez un glisser-déplacer pour copier les données qui doivent être importées en tant qu’objets blob de blocs dans le dossier *BlockBlob*. De même, glissez-déplacez des données telles que VHD/VHDX vers le dossier *PageBlob*.

    Un conteneur est créé dans le compte de stockage Azure de chaque sous-dossier sous les dossiers *BlockBlob* et *PageBlob*. Tous les fichiers sous les dossiers *BlockBlob* et *PageBlob* sont copiés dans un conteneur par défaut `$root` dans le compte de stockage Azure.

    > [!NOTE] 
    > - Tous les conteneurs et objets blob doivent être conformes aux [conventions d’affectation de noms Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Si ces règles ne sont pas respectées, le chargement des données vers Azure échoue.
    > - Les fichiers ne doivent pas dépasser ~4,75 Tio pour les objets blob de blocs ou ~ 8 Tio pour les objets blob de pages.

2. (Facultatif) Une fois la copie terminée, nous vous recommandons d’exécuter `DataBoxDiskValidation.cmd` fourni dans le dossier *AzureImportExport* pour générer des sommes de contrôle pour la validation. Selon la taille des données, cette étape peut prendre un certain temps. 
3. Débranchez le lecteur. 


## <a name="ship-to-azure"></a>Envoyer à Azure

Cette étape prend environ 5 à 7 minutes.

1. Rassemblez tous les disques dans le colis d’origine. Utilisez l’étiquette d’expédition incluse. Si l’étiquette est endommagée ou a été perdue, téléchargez-la à partir du portail. Accédez à **Vue d’ensemble** et cliquez sur **Télécharger une étiquette d’expédition** à partir de la barre de commandes.
2. Déposez le colis scellé au point de dépôt.  

Le service Data Box Disk envoie une notification par e-mail et met à jour l’état de la commande dans le portail Azure.


## <a name="verify-your-data"></a>Vérifier vos données

Le temps nécessaire à cette opération dépend de la taille de vos données.

1. Lorsque le disque Data Box est connecté au réseau du centre de données Azure, le chargement des données vers Azure démarre automatiquement. 
2. Le service Azure Data Box vous avertit que la copie des données est terminée via le portail Azure. 
    
    1. Vérifiez les journaux d’erreurs pour connaître les échecs éventuels et prendre les mesures appropriées.
    2. Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source.

## <a name="clean-up-resources"></a>Supprimer les ressources

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


