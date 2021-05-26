---
title: Enregistrer un ticket de support pour Azure Stack Edge, Azure Data Box Gateway | Microsoft Docs
description: Découvrez comment enregistrer une demande de support pour les problèmes liés à vos commandes Azure Stack Edge ou Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: e109c09a70b96e81daad8929fc82b6059b3619b6
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110060541"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Ouvrir un ticket de support pour Azure Stack Edge et Azure Data Box Gateway

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-databox-gateway-sku.md)]

Cet article s’applique aux services Azure Stack Edge et Azure Data Box Gateway, qui sont managés par le service Azure Stack Edge/Azure Data Box Gateway. Si vous rencontrez des problèmes avec votre service, vous pouvez créer une demande de service pour obtenir un support technique. Cet article vous guide tout au long des procédures suivantes :

* Création d’une demande de support
* Gestion du cycle de vie d’une demande de support à partir du portail

## <a name="create-a-support-request"></a>Création d’une demande de support

Procédez comme suit pour créer une demande de support :

1. Accédez à votre commande Azure Stack Edge ou Data Box Gateway. Accédez à la section **Support + dépannage**, puis cliquez sur **Nouvelle demande de support**.

2. Dans **Nouvelle demande de support**, dans l'onglet **De base**, procédez comme suit :

    1. Dans la liste déroulante **Type de problème**, sélectionnez **Technique**.
    2. Choisissez votre **abonnement**.
    3. Sous **Service**, sélectionnez **Mes services**. Dans la liste déroulante, sélectionnez **Azure Stack Edge et Data Box Gateway**.
    4. Sélectionnez votre **ressource**. Elle correspond au nom de votre commande.
    5. Fournissez un bref **résumé** du problème que vous rencontrez. 
    6. Sélectionnez votre **type de problème**.
    7. Selon le type de problème que vous avez sélectionné, choisissez le **sous-type de problème** correspondant.
    8. Sélectionnez **Suivant : Solutions >>** .

        ![Concepts de base](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Sous l’onglet **Détails**, effectuez les étapes suivantes :

    1. Fournissez la date et l’heure de début du problème.
    2. Fournissez une **description** de votre problème.
    3. Dans le panneau **Chargement de fichiers**, sélectionnez l’icône de dossier pour sélectionner d’autres fichiers, le cas échéant.
    4. Sélectionnez la case à cocher **Partager les informations de diagnostic**.
    5. En fonction de votre abonnement, un **plan de support** est automatiquement rempli.
    6. Dans la liste déroulante, sélectionnez la **gravité**.
    7. Spécifiez une **méthode de contact préférée**.
    8. Les **heures de réponse** sont automatiquement sélectionnées en fonction de votre plan d’abonnement.
    9. Indiquez la langue que vous préférez pour le support.
    10. Dans **Informations de contact**, fournissez votre nom, votre adresse e-mail, votre numéro de téléphone, un contact secondaire (facultatif) et votre pays/région. Le Support Microsoft vous contacte à l’aide de ces informations pour approfondir le diagnostic et vous aider à résoudre le problème. 
    11. Sélectionnez **Suivant : Vérifier + créer >>** .

        ![Problème](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Dans l'onglet **Vérifier + créer**, vérifiez les informations de votre ticket de support. Sélectionnez **Create** (Créer). 

    ![Problème 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Une fois votre ticket de support créé, un ingénieur de support vous contactera dès que possible pour traiter votre demande.

## <a name="get-hardware-support"></a>Obtenir une assistance matérielle

Ces informations ne s'appliquent qu'à l’appareil Azure Stack. Le processus de signalement des problèmes matériels est le suivant :

1. Ouvrez un ticket de support depuis le portail Azure pour signaler un problème matériel. Sous **Type de problème**, sélectionnez **Azure Stack Hardware (Matériel Azure Stack)** . Choisissez le **sous-type de problème** **Hardware failure (Défaillance matérielle)** .

    ![Problème matériel](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Une fois votre ticket de support créé, un ingénieur de support vous contactera dès que possible pour traiter votre demande.

2. Si le Support Microsoft détermine qu’il s’agit d’un problème matériel, l’une des actions suivantes se produit :

    * Une unité de remplacement de champ (FRU) pour la pièce défectueuse est envoyée. Actuellement, les blocs d’alimentation et les disques durs SSD sont les seules unités remplaçables sur site (FRU) prises en charge.
    * Seules les unités remplaçables sur site sont remplacées le jour ouvrable suivant, tout le reste nécessite le remplacement complet du système (FSR).

3. S’il est déterminé qu’un remplacement FRU est requis avant 13h, heure locale, (du lundi au vendredi), un technicien est dépêché sur place le jour ouvré suivant pour procéder à un remplacement FRU. Un remplacement complet du système prend généralement plus de temps, car les pièces sont expédiées à partir de notre usine et peuvent faire l’objet de retards de transport et de douane.

## <a name="manage-a-support-request"></a>Gérer une demande de support

Après avoir créé un ticket de support, vous pouvez gérer son cycle de vie depuis le portail.

### <a name="to-manage-your-support-requests"></a>Pour gérer vos demandes de support

1. Pour accéder à la page d’aide et de support, cliquez sur **Parcourir > Aide + support**.

    ![Gérer les demandes de support](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. Une liste tabulaire des **demandes de support récentes** s’affiche sous **Aide + support**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Sélectionnez une demande de support. Vous pouvez afficher l’état et les détails de cette demande. Si vous souhaitez donner suite à cette demande, cliquez sur **+ Nouveau message**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [résoudre les problèmes liés au service Azure Stack Edge](azure-stack-edge-troubleshoot.md).
Découvrez comment [résoudre les problèmes liés au service Data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).