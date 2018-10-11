---
title: Commander une Microsoft Azure Data Box | Microsoft Docs
description: En savoir plus sur les conditions préalables au déploiement et la commande d’une Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: 0d77353b510dbeb18e96e2d8313e9010c04e25a7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077931"
---
# <a name="tutorial-order-azure-data-box"></a>Didacticiel : Commander une Azure Data Box

Azure Data Box est une solution hybride qui vous permet d’importer vos données locales dans Azure de manière rapide, simple et fiable. Vous transférez vos données vers un périphérique de stockage de 80 To (capacité utilisable) fourni par Microsoft, puis renvoyez l’appareil. Ces données sont ensuite chargées dans Azure.

Ce tutoriel explique comment commander une Azure Data Box. Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * S’inscrire à Data Box
> * Commander une Data Box
> * Suivre la commande
> * Annuler la commande

## <a name="prerequisites"></a>Prérequis

Effectuez les prérequis de configuration suivants pour l’appareil et le service Data Box avant de déployer l’appareil.

### <a name="for-service"></a>Pour le service

Avant de commencer, assurez-vous que :
- Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.
- L’abonnement que vous utilisez pour le service Data Box est un des types suivants :
    - Contrat Entreprise (EA) Microsoft. En savoir plus sur les [abonnements EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fournisseur de solutions cloud (CSP). En savoir plus sur le [programme Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Consommation : paiement à l’utilisation. Plus d’informations sur les [abonnements avec paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) Azure.

- Vous disposez d’un accès propriétaire ou contributeur à l’abonnement pour créer une commande Data Box.

### <a name="for-device"></a>Pour l’appareil

Avant de commencer, assurez-vous que :
- Vous disposez d’un ordinateur hôte connecté au réseau du centre de données. La Data Box va copier les données de cet ordinateur. Votre ordinateur hôte doit exécuter un système d’exploitation pris en charge comme décrit dans [Conditions requises pour le système Azure Data Box](data-box-system-requirements.md).
- Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, vous pouvez utiliser une liaison de données 1 GbE. Cependant, cela a une incidence sur les vitesses de copie.


## <a name="order-data-box"></a>Commander une Data Box

Procédez comme suit dans le portail Azure pour commander un appareil.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter à cette URL : [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **+ Créer une ressource** et recherchez *Azure Data Box*. Cliquez sur **Azure Data Box**.
    
   [![Rechercher Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Cliquez sur **Créer**.

4. Vérifiez si le service Data Box est disponible dans votre région. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Appliquer**. 
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement     | Sélectionnez un abonnement EA, CSP ou avec paiement à l’utilisation pour le service Data Box. <br> L’abonnement est lié à votre compte de facturation.       |
    |Type de transfert     | Sélectionnez **Importer vers Azure**.        |
    |Pays source     |   Sélectionnez le pays où vos données se trouvent actuellement.         |
    |Région Azure de destination     |     Sélectionnez la région Azure où vous souhaitez transférer des données.        |

5. Sélectionnez **Data Box**. La capacité maximale de la solution pour une commande unique est de 80 To. Vous pouvez créer plusieurs commandes pour des tailles de données supérieures.

      [![Sélectionner Data Box, option 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. Dans **Commande**, indiquez les **Détails de la commande**. Saisissez ou sélectionnez les informations suivantes, puis cliquez sur **Suivant**.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |NOM     |  Indiquez un nom convivial pour suivre la commande. <br> Le nom peut comporter entre 3 et 24 caractères qui peuvent être des lettres, des chiffres et des traits d’union. <br> Il doit commencer et se terminer par une lettre ou un chiffre.      |
    |Groupe de ressources     |   Créez-en un nouveau ou utilisez un groupe existant. <br> Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble.         |
    |Région Azure de destination     | Sélectionnez une région pour votre compte de stockage. <br> Pour plus d’informations, consultez la [disponibilité des régions](data-box-overview.md#region-availability).        |
    |Compte(s) de stockage     | Selon la région Azure spécifiée, sélectionnez un ou plusieurs comptes de stockage dans la liste filtrée d’un compte de stockage existant. La Data Box peut être liée à 10 comptes de stockage maximum. <br> Vous pouvez également créer un compte de **stockage blob**, **Usage général v1** ou **Usage général v2**.        |
    
7. Dans la zone **Adresse d’expédition**, indiquez vos nom et prénom, le nom et l’adresse postale de la société, et un numéro de téléphone valide. Cliquez sur **Valider l’adresse**. Le service valide l’adresse d’expédition de disponibilité du service. Si le service est disponible pour l’adresse de livraison indiquée, vous recevez une notification à cet effet. Cliquez sur **Suivant**.

8. Dans **Détails de la notification**, indiquez les adresses de messagerie. Le service envoie des notifications par courrier électronique concernant les mises à jour de l’état de la commande aux adresses de messagerie spécifiées.

    Nous vous recommandons d’utiliser un e-mail de groupe afin de continuer à recevoir des notifications si un administrateur du groupe quitte l’entreprise.

9. Consultez le **Résumé** des informations liées à la commande, au contact, à la notification et aux conditions de confidentialité. Cochez la case correspondant à l’acceptation des conditions de confidentialité.

10. Cliquez sur **Commande**. La création d’une commande peut prendre quelques minutes. 


## <a name="track-the-order"></a>Suivre la commande

Une fois la commande passée, vous pouvez suivre son état à partir du portail Azure. Accédez à votre commande, puis à **Vue d’ensemble** pour afficher l’état. Le portail affiche la commande avec l’état **Commandé**.

Si l’appareil n’est pas disponible, vous recevez une notification. Si l’appareil est disponible, Microsoft identifie l’appareil à expédier et le prépare. Pendant la préparation de l’appareil, les actions suivantes se produisent :

- Des partages SMB sont créés pour chaque compte de stockage associé à l’appareil. 
- Pour chaque partage, des informations d’identification d’accès (nom d’utilisateur et mot de passe) sont générées.
- Un mot de passe qui permet de déverrouiller l’appareil est également généré. 
- La Data Box est verrouillée pour empêcher tout accès non autorisé à l’appareil à tout moment.

Une fois l’appareil préparé, le portail affiche la commande avec l’état **Traité**.

![Commande de Data Box traitée](media/data-box-overview/data-box-order-status-processed.png)

Ensuite, Microsoft prépare et achemine l’appareil via un transporteur régional. Vous recevez un numéro de suivi une fois l’appareil expédié. Le portail affiche la commande dont l’état est **Distribué**.

![Commande de Data Box expédiée](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Annuler la commande

Pour annuler cette commande, dans le portail Azure, accédez à **Vue d’ensemble** et cliquez sur **Annuler** dans la barre de commandes.

Après avoir passé commande, vous pouvez l’annuler à tout moment tant qu’elle n’a pas été traitée.
 
Pour supprimer une commande annulée, accédez à **Vue d’ensemble** et cliquez sur **Supprimer** à partir de la barre de commandes.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Conditions préalables au déploiement de la Data Box
> * Commander une Data Box
> * Suivre la commande
> * Annuler la commande

Passez au tutoriel suivant pour apprendre à configurer votre Data Box.

> [!div class="nextstepaction"]
> [Configurer votre Azure Data Box](./data-box-deploy-set-up.md)


