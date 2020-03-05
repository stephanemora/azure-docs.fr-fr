---
title: Tutoriel sur le renvoi d’Azure Data Box Disk | Microsoft Docs
description: Utilisez ce didacticiel pour savoir comment renvoyer vos disques Azure Data Box à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1003b862d7fbe19545cf705570866479132c1f03
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273172"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Renvoyer un disque Azure Data Box 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutoriel : Renvoyer un disque Azure Data Box 

Ce tutoriel explique comment planifier un enlèvement pour renvoyer un disque Azure Data Box. Les instructions d’enlèvement varient selon la destination de renvoi de l’appareil. 

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Expédier le disque Data Box à Microsoft
> * Procéder à l’enlèvement du disque Data Box dans différentes régions

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi le [tutoriel : Copier des données sur Azure Data Box Disk et procéder à une vérification](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Renvoyer un disque Data Box

::: zone-end

1. Une fois que la validation des données est terminée, déconnectez les disques. Débranchez les câbles de connexion.
2. Emballez tous les disques et les câbles de connexion dans du papier à bulles et placez le tout dans la boîte d’envoi. Des frais peuvent s’appliquer si les accessoires sont manquants.
    - Réutilisez l’emballage de la première expédition.  
    - Nous vous recommandons d’emballer les disques avec du papier bulle pour les protéger.
    - Vérifiez qu’ils sont suffisamment serrés dans la boîte pour éviter qu’ils ne bougent.

Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil. Les instructions sont différentes pour les États-Unis/le Canada, l’Union européenne (UE), l’Australie et les pays d’Asie.

### <a name="in-us-or-canada"></a>[Aux États-Unis ou au Canada](#tab/in-us-or-canada)

Effectuez les étapes suivantes si le retour de l’appareil s’effectue aux États-Unis ou au Canada.

1. Utilisez l’étiquette de retour figurant dans la pochette en plastique transparente collée sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition** et téléchargez une étiquette d’expédition de retour.
    - Collez l’étiquette sur l’appareil.

2. Scellez la boîte d’envoi et assurez-vous que l’étiquette de retour est visible.
3. Planifiez un enlèvement avec UPS. Pour planifier l’enlèvement :

    - Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays/région).
    - Lors de votre appel, indiquez le numéro de suivi de retour mentionné sur votre étiquette imprimée.
    - Si le numéro de suivi n’est pas indiqué, UPS vous facturera des frais supplémentaires à l’enlèvement.
    - Au lieu de planifier l’enlèvement, vous pouvez également déposer le disque Data Box Disk à l’emplacement de dépôt le plus proche.

### <a name="in-europe"></a>[En Europe](#tab/in-europe)

Effectuez les étapes suivantes si le retour de l’appareil s’effectue en Europe.

1. Utilisez l’étiquette de retour figurant dans la pochette en plastique transparente collée sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition** et téléchargez une étiquette d’expédition de retour.
    - Collez l’étiquette sur l’appareil.

2. Scellez la boîte d’envoi et assurez-vous que l’étiquette de retour est visible.
3. Si vous renvoyez l’appareil en Europe via DHL, faites une demande d’enlèvement auprès de DHL en vous rendant sur leur site web et en spécifiant le numéro de fiche de transport aérien.
4. Accédez au site web DHL Express du pays ou de la région et choisissez **Book a Courier Collection (Réserver un enlèvement) > eReturn Shipment (Expédition eReturn)** .    
3. Spécifiez le numéro de la fiche de transport et cliquez sur **Planifier l’enlèvement** pour choisir une date.

### <a name="in-australia"></a>[En Australie](#tab/in-australia)

Les centres de données Azure de l’Australie ont une notification de sécurité supplémentaire. Toutes les expéditions entrantes doivent avoir une notification préalable. Effectuez les étapes suivantes pour l’enlèvement en Australie.

1. Utilisez l’étiquette d’expédition de retour et assurez-vous que le code TAU (numéro de référence) est écrit dessus. Si l’étiquette d’expédition fournie est manquante ou si vous rencontrez tout autre problème, envoyez un e-mail à [Data Box Asia Operations](mailto:adbo@microsoft.com). Indiquez le nom de la commande dans l’en-tête d’objet et les détails du problème auquel vous êtes confronté.
3. Collez l’étiquette sur le colis. 
4. Réservez un enlèvement en ligne sur https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference. 

### <a name="in-japan"></a>[Au Japon](#tab/in-japan)

1. Écrivez le nom et l’adresse de votre entreprise dans la partie du bordereau de réexpédition réservée à l’expéditeur.
2. Envoyez un courrier à Quantium Solutions en utilisant le modèle d’e-mail suivant.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Si l’enlèvement doit se faire à Osaka**, remplacez l’objet du modèle d’e-mail par celui-ci : `Pickup request for Microsoft Azure OSA`.
    - Si le bordereau de réexpédition de Japon Post Chakubarai était manquant, consignez ce fait que dans cet e-mail. Quantium Solutions demandera à Japan Post d’apporter le bordereau de réexpédition à l’enlèvement.
    - Si vous avez plusieurs commandes, envoyez un e-mail pour garantir un enlèvement individuel.

3. Recevoir un e-mail de confirmation de Quantium Solutions près avoir réservé un enlèvement. L’e-mail de confirmation inclut également des informations sur le bordereau de réexpédition de Chakubarai.

Si nécessaire, vous pouvez contacter le support de Quantium Solutions (en langue japonaise) dont les coordonnées sont les suivantes : 

- E-mail ：Customerservice.JP@quantiumsolutions.com 
- Téléphone：+81 3 57 55 01 50 

### <a name="in-korea"></a>[En Corée](#tab/in-korea)

1. Veillez à inclure le bordereau de réexpédition.
2. Pour demander un enlèvement lorsque le bordereau de réexpédition est présent :
    1. Appelez l’assistance téléphonique de *Quantium Solutions International* au 070-8231-1418 pendant les heures de bureau (du lundi au vendredi de 10 h à 17 h). Indiquez *Enlèvement Microsoft Azure* et le numéro de la demande de service pour organiser l’enlèvement.  
    2. Si l’assistance téléphonique est occupée, envoyez un e-mail à `microsoft@rocketparcel.com`, avec l’objet *Enlèvement Microsoft Azure* et le numéro de demande de service comme référence.
    3. Si le transporteur ne procède pas à l’enlèvement, appelez l’assistance téléphonique de *Quantium Solutions International* pour trouver une autre solution. 
    4. Vous recevez un e-mail de confirmation pour la planification de l’enlèvement.
3. Effectuez cette étape uniquement si le bordereau de réexpédition n’est pas présent. Pour demander un enlèvement :
    1. Appelez l’assistance téléphonique de *Quantium Solutions International* au 070-8231-1418 pendant les heures de bureau (du lundi au vendredi de 10 h à 17 h). Indiquez *Enlèvement Microsoft Azure* et le numéro de la demande de service pour organiser l’enlèvement. Spécifiez que vous avez besoin d’un nouveau bordereau de réexpédition pour organiser un enlèvement. Fournissez à l’expéditeur (client) les informations du destinataire (centre de données Azure) et le numéro de référence (numéro de la demande de service). 
    2. Si l’assistance téléphonique est occupée, envoyez un e-mail à `microsoft@rocketparcel.com`, avec l’objet *Enlèvement Microsoft Azure* et le numéro de demande de service comme référence.
    3. Si le transporteur ne procède pas à l’enlèvement, appelez l’assistance téléphonique de *Quantium Solutions International* pour trouver une autre solution. 
    4. Si la demande est effectuée par téléphone, vous recevez une confirmation verbale.


### <a name="in-singapore"></a>[À Singapour](#tab/in-singapore)

1. Imprimez l’étiquette d’expédition et apposez-la sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition** et récupérez une étiquette d’expédition de retour.
    - Collez l’étiquette sur l’appareil. Vérifiez que l’étiquette est visible.

2. Pour demander la collecte, envoyez un e-mail au service client SingPost en utilisant le modèle suivant, avec le numéro de suivi (le numéro de suivi se trouve sur l’étiquette de retour fournie dans le package remis).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - XZ00001234567 
    Body: 
     a. Requestor name
     b. Requestor contact number
     c. Requestor collection address
     d. Preferred collection date
    ```

   > [!NOTE]
   > Pour les demandes de réservation reçues un jour ouvré :
   > - Avant 15 h, la collecte sera effectuée le jour ouvré suivant entre 9 h et 13 h.
   > - Après 15 h, la collecte sera effectuée le jour ouvré suivant entre 14 h et 18 h.

   Si vous rencontrez des problèmes, contactez l’équipe Data Box Operations Asia à l’adresse suivante : adbo@microsoft.com. Indiquez le nom de la tâche dans l’objet du message, ainsi que le problème rencontré.

3. Remettez le colis au transporteur.

### <a name="in-self-managed"></a>[Si auto-gestion](#tab/in-selfmanaged)

Si vous utilisez Data Box Disk au Japon, à Singapour, en Corée et dans la région Europe Ouest et que vous avez sélectionné l’option d’expédition auto-gérée pendant la création de la commande, suivez ces instructions. 

1. Accédez au panneau **Vue d’ensemble** de votre commande dans le portail Azure. Parcourez les instructions qui s’affichent lorsque vous sélectionnez **Planifier l’enlèvement**. Un code d’autorisation doit apparaître. Il est utilisé au moment de la remise de la commande.

2. Envoyez un e-mail à l’équipe des opérations Azure Data Box à l’aide du modèle suivant lorsque vous êtes prêt à renvoyer l’appareil.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: ‘orderName’
    Body: 
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. L’équipe des opérations Azure Data Box vous accompagne pour organiser la remise au centre de données Azure.

::: zone target="docs"

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Expédier le disque Data Box à Microsoft
> * Procéder à l’enlèvement du disque Data Box dans différentes régions

Passez à la procédure suivante pour savoir comment vérifier le chargement de données à partir d’un disque Data Box vers un compte de stockage Azure.

> [!div class="nextstepaction"]
> [Vérifier le chargement de données à partir d’un disque Azure Data Box](./data-box-disk-deploy-picked-up.md)

::: zone-end




