---
title: Tutoriel - Commander Microsoft Azure Data Box Disk | Microsoft Docs
description: Ce tutoriel vous apprend à vous inscrire et à commander un disque Azure Data Box Disk pour importer des données dans Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: bd90d3c4c9207374d6a6085df6a3962ef42b68a9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091423"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Tutoriel : Commander un disque Azure Data Box Disk (préversion)

Azure Data Box Disk est une solution cloud hybride qui vous permet d’importer vos données locales dans Azure de manière rapide, simple et fiable. Vous transférez vos données vers des disques SSD fournis par Microsoft et renvoyez les disques. Ces données sont ensuite chargées dans Azure. 

Ce tutoriel explique comment commander un Azure Data Box Disk. Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * S’inscrire à Data Box Disk
> * Commander un Data Box Disk
> * Suivre la commande
> * Annuler la commande

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> - Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 
> - Pendant la période de préversion, Data Box Disk peut être envoyé aux clients se trouvant aux États-Unis, en Europe du Nord et de l’Ouest, au Canada et en Australie. Pour plus d’informations, consultez [Disponibilité des régions](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Inscription 

Data Box Disk est en préversion et vous devez vous inscrire pour utiliser ce service. Procédez comme suit pour vous inscrire au service Data Box Disk :

1. Connectez-vous au portail Azure à l’adresse : [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Sélectionnez l’abonnement que vous souhaitez activer pour la préversion. Répondez aux questions concernant la taille des données, le pays de résidence des données, le laps de temps et la fréquence de transfert des données. Cliquez sur **M’inscrire**.
3. Une fois que vous êtes inscrit et autorisé à utiliser la préversion, vous pouvez commander un Data Box Disk.

## <a name="order-data-box-disk"></a>Commander un Data Box Disk

Procédez comme suit dans le [portail Azure](https://aka.ms/azuredataboxfromdiskdocs) pour commander Data Box Disk.

1. Dans le coin supérieur gauche du portail, cliquez sur **+Créer une ressource**, puis recherchez *Azure Data Box*. Cliquez sur **Azure Data Box**.
    
   ![Recherchez Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Cliquez sur **Créer**.

3. Vérifiez si le service Data Box est disponible dans votre région. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Appliquer**.

    ![Sélectionnez l’option Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Sélectionnez l’abonnement pour lequel le service Data Box est activé.<br> L’abonnement est lié à votre compte de facturation. |
    |Type de transfert| Importer vers Azure|
    |Pays source | Sélectionnez le pays où vos données se trouvent actuellement.|
    |Région Azure de destination|Sélectionnez la région Azure où vous souhaitez transférer des données.|

  
5.  Sélectionnez **Data Box Disk**. La capacité maximale de la solution pour une seule commande de 5 disques est de 35 To. Vous pouvez créer plusieurs commandes pour des tailles de données supérieures. 

     ![Sélectionnez l’option Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  Dans **Commande**, indiquez les **Détails de la commande**. Entrez ou sélectionnez les informations suivantes :

    |Paramètre|Valeur|
    |---|---|
    |NOM|Indiquez un nom convivial pour suivre la commande.<br> Le nom peut comporter entre 3 et 24 caractères qui peuvent être des lettres, des chiffres et des traits d’union. <br> Il doit commencer et se terminer par une lettre ou un chiffre. |
    |Groupe de ressources| Créez-en un nouveau ou utilisez un groupe existant. <br> Un groupe de ressources est un conteneur logique pour les ressources qui peuvent être gérées ou déployées ensemble. |
    |Région Azure de destination| Sélectionnez une région pour votre compte de stockage.<br> Actuellement, les comptes de stockage de toutes les régions des États-Unis, d’Europe du Nord et de l’Ouest, du Canada et de l’Australie sont pris en charge. |
    |Compte(s) de stockage|Selon la région Azure spécifiée, effectuez la sélection dans la liste filtrée d’un compte de stockage existant. <br>Vous pouvez également créer un nouveau compte Usage général v1 et Usage général v2. |
    |Taille de données estimée en To| Entrez une estimation en To. <br>Selon la taille des données, Microsoft vous envoie un nombre de disques SSD de 8 To (7 To de capacité utilisable) approprié. <br>La capacité utilisable maximale de 5 disques peut atteindre 35 To. |

13. Cliquez sur **Suivant**. 

    ![Fournir les détails de la commande](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Dans l’onglet **Adresse d’expédition**, indiquez vos nom et prénom, le nom et l’adresse postale de la société et un numéro de téléphone valide. Cliquez sur **Valider l’adresse**. Le service valide l’adresse d’expédition de disponibilité du service. Si le service est disponible pour l’adresse de livraison indiquée, vous recevez une notification à cet effet. 

    ![Indiquer l’adresse de livraison](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Dans **Détails de la notification**, indiquez les adresses de messagerie. Le service envoie des notifications par courrier électronique concernant les mises à jour de l’état de la commande aux adresses de messagerie spécifiées. 

    Nous vous recommandons d’utiliser un e-mail de groupe afin de continuer à recevoir des notifications si un administrateur du groupe quitte l’entreprise.

16. Consultez le **Résumé** des informations liées à la commande, au contact, à la notification et aux conditions de confidentialité. Cochez la case correspondant à l’acceptation des conditions de confidentialité.

17. Cliquez sur **Commande**. La création d’une commande peut prendre quelques minutes.

 
## <a name="track-the-order"></a>Suivre la commande

Une fois la commande passée, vous pouvez suivre l’état de la commande à partir du portail Azure en préversion. Accédez à votre commande, puis à **Vue d’ensemble** pour afficher l’état. Le portail affiche le travail dont l’état est **Commandé**. 

![Data Box Disk - État Commandé](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Si les disques ne sont pas disponibles, vous recevez une notification. Si les disques sont disponibles, Microsoft identifie les disques à expédier et les prépare. Pendant la préparation du disque, les actions suivantes se produisent :

- Les disques sont chiffrés à l’aide du chiffrement AES-128 BitLocker.  
- Les disques sont verrouillés pour empêcher tout accès non autorisé aux disques.
- La clé de sécurité permettant de déverrouiller les disques est générée au cours de ce processus.

Une fois la préparation du disque terminée, le portail affiche la commande dont l’état est **Traité**.

Ensuite, Microsoft prépare et achemine les disques via un transporteur régional. Vous recevez un numéro de suivi une fois les disques expédiés. Le portail affiche la commande dont l’état est **Distribué**.



## <a name="cancel-the-order"></a>Annuler la commande

Pour annuler cette commande, dans le portail Azure en préversion, accédez à **Vue d’ensemble** et cliquez sur **Annuler** à partir de la barre de commandes. 

L’annulation est possible uniquement lorsque les disques sont commandés et la commande en cours de traitement en vue de l’expédition. Une fois la commande traitée, vous ne pouvez plus l’annuler. 

![Annuler la commande](media/data-box-disk-deploy-ordered/cancel-order1.png)

Pour supprimer une commande annulée, accédez à **Vue d’ensemble** et cliquez sur **Supprimer** à partir de la barre de commandes. 


## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * S’inscrire à Data Box Disk
> * Commander un Data Box Disk
> * Suivre la commande
> * Annuler la commande

Passez au tutoriel suivant pour apprendre à configurer votre Data Box Disk.

> [!div class="nextstepaction"]
> [Configurer votre Azure Data Box Disk](./data-box-disk-deploy-set-up.md)


