---
title: Mettre à jour une offre de place de marché commerciale existante
description: Comment mettre à jour une offre de place de marché commerciale existante, notamment la modification, la suppression d’un brouillon, l’annulation d’une demande de publication, l’arrêt de la vente d’une offre ou d’un plan et la synchronisation des audiences privées.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 01/16/2020
ms.openlocfilehash: f83f5da03d2db5354b020ce7d0c3c8d70f1830a0
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650095"
---
# <a name="update-an-existing-offer-in-the-commercial-marketplace"></a>Mettre à jour une offre existante dans la place de marché commerciale

Vous pouvez afficher vos offres existantes dans l’onglet **Vue d’ensemble** du [portail de la Place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans l’Espace partenaires.

Pour mettre à jour une offre existante actuellement en direct dans la place de marché commerciale  :

1. Sélectionnez le nom de l’offre à mettre à jour. L’état de l’offre peut être répertorié comme suit : **Préversion**, **Activée**, **Publication en cours**, **Brouillon**, **Attention requise** ou **Non disponible** (si vous avez précédemment choisi d’arrêter de vendre l’offre). Une fois sélectionnée, la page **Vue d’ensemble de l’offre** de cette offre s’ouvre.
2. Sélectionnez **Mettre à jour** pour la zone que vous souhaitez mettre à jour dans la carte sur la page de présentation de l’offre ou dans l’élément de menu dans le volet de navigation gauche. Vous souhaitez peut-être mettre à jour la **Configuration de l’offre**, **Propriétés**, **Liste de l’offre**, **Préversion**, **Configuration technique**, **Vue d’ensemble du plan** ou **Test drive**.
3. Apportez vos modifications, puis sélectionnez **Enregistrer le brouillon**. Répétez ce processus jusqu'à ce que toutes vos modifications soient terminées.

## <a name="review-and-publish-an-updated-offer"></a>Passer en revue et publier une offre mise à jour

Lorsque vous êtes prêt à publier votre offre mise à jour, sélectionnez **Passer en revue et publier** à partir de n’importe quelle page. La page **Passer en revue et publier** s’ouvre. Dans cette page, vous pouvez :

- Consulter l’état d’achèvement des sections de l’offre que vous avez mise à jour : 
    - **Modifications non publiées** : la section a été mise à jour et est complète. Toutes les données requises ont été fournies et aucune erreur ne s’est glissée dans les mises à jour.
    - **Caractère incomplet** : les mises à jour de la section ont entraîné des erreurs qui doivent être corrigées, ou qui nécessitent plus d’informations.
- Fournissez des informations supplémentaires à l’équipe de test de certification pour garantir un bon déroulement des tests.
- Envoyez l’offre mise à jour pour publication en sélectionnant **Publier**.  Nous vous enverrons un e-mail quand une préversion de l’offre mise à jour sera disponible pour examen et approbation.

> [!IMPORTANT]
> Vous devez examiner votre préversion de l’offre une fois qu’elle est disponible et sélectionner **Démarrer** pour publier votre offre mise à jour à l’attention de votre audience ciblée (publique ou privée).

## <a name="add-a-plan-to-an-existing-offer"></a>Ajouter un plan à une offre existante

Pour ajouter un plan à une offre existante que vous avez déjà publiée :

1. Avec la page **Vue d’ensemble de l’offre** pour votre offre existante ouverte, allez à la page **Vue d’ensemble du plan** et sélectionnez **Créer un nouveau plan**.
1. Créez un nouveau plan en suivant les [instructions](./create-new-saas-offer.md#plan-overview) à l’aide du**modèle de tarification des plans existant**.
1. Sélectionnez**Enregistrer le brouillon** après avoir changer le nom du plan.
1. Sélectionnez **Publier** une fois que vous êtes prêt à publier vos mises à jour. La page **[Examiner et publier](#review-and-publish-an-updated-offer)** s’ouvre et fournit un état d’achèvement pour vos mises à jour.

## <a name="update-a-plan-within-an-existing-offer"></a>Mettre à jour un plan au sein d’une offre existante

Pour apporter des modifications à un plan au sein d’une offre existante que vous avez déjà publiée :

1. Avec la page **Vue d’ensemble de l’offre** ouverte pour votre offre existante, choisissez le plan que vous souhaitez modifier. Si le plan n’est pas accessible à partir de la liste **Vue d’ensemble du plan**, sélectionnez **Voir tous les plans**.
1. Sélectionnez le **nom**, le **modèle de tarification** ou la **disponibilité** du plan. *Actuellement, les plans sont disponibles uniquement en anglais (États-Unis)* .
1. Sélectionnez **Enregistrer le brouillon** après avoir apporté les modifications nécessaires au nom du plan, à sa description ou à sa disponibilité pour l’audience.
1. Si vous êtes prêt à publier vos mises à jour, sélectionnez **Passer en revue et publier**. La page **[Examiner et publier](#review-and-publish-an-updated-offer)** s’ouvre et fournit un état d’achèvement pour vos mises à jour.
1. Envoyez le plan mis à jour pour publication en sélectionnant **Publier**. Nous vous enverrons un e-mail quand une préversion de l’offre mise à jour sera disponible pour examen et approbation.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Offrir un plan d’ordinateur virtuel à un nouveau prix

Après la publication d’un plan d’ordinateur virtuel, son prix ne peut pas être modifié. Pour offrir le même plan à un prix différent, vous devez masquer celui-ci et en créer un nouveau avec le tarif mis à jour. Tout d’abord, masquez le plan avec le prix que vous souhaitez modifier :

1. Avec la page **Vue d’ensemble de l’offre** ouverte pour votre offre existante, choisissez le plan que vous souhaitez modifier. Si le plan n’est pas accessible à partir de la liste **Vue d’ensemble du plan**, sélectionnez **Voir tous les plans**.
1. Activez la case à cocher **Masquer le plan**. Enregistrez le brouillon avant de continuer.

Maintenant que vous avez masqué le plan avec l’ancien prix, créez une copie de ce plan avec le tarif mis à jour :

1. Dans l’Espace partenaires, revenez à **Vue d’ensemble du plan**.
2. Sélectionnez **Créer un plan**. Entrez un **ID de plan** et un **Nom de plan**, puis sélectionnez **Créer**.
1. Pour réutiliser la configuration technique du plan que vous avez masqué, activez la case à cocher **Réutiliser la configuration technique**. Pour en savoir plus, consultez [Vue d’ensemble du plan](azure-vm-create-offer.md#plan-overview).
    > [!IMPORTANT]
    > Si vous sélectionnez **Ce plan réutilise la configuration technique d’un autre plan**, vous ne pourrez pas arrêter la vente du plan parent ultérieurement. N’utilisez pas cette option si vous souhaitez arrêter la vente du plan parent.
3. Renseignez toutes les sections requises pour le nouveau plan, y compris le nouveau prix.
1. Sélectionnez **Enregistrer le brouillon**.
1. Une fois que vous avez terminé toutes les sections requises pour le nouveau plan, sélectionnez **Vérifier et publier**. Cela permet d’envoyer votre offre à des fins de révision et de publication. Lisez [Consulter et publier une offre sur la place de marché commerciale](../review-publish-offer.md) pour plus d’informations.

## <a name="compare-changes-to-commercial-marketplace-offers"></a>Comparer les modifications apportées aux offres de la place de marché commerciale

Vous pouvez auditer les modifications apportées à une offre [publiée](#compare-changes-to-published-offer) ou [en préversion](#compare-changes-to-a-preview-offer) avant de les publier en direct en utilisant **Comparer**.

> [!NOTE]
> Une offre publiée est une offre qui a été publiée en préversion ou en direct.

Voir ci-dessous pour obtenir des informations d’audit d’ordre général :

- Vous pouvez utiliser **Comparer** à tout moment pendant le processus de modification.
- Sélectionnez un champ dans la page **Comparer** pour accéder à la valeur que vous souhaitez modifier.
- Si vous êtes prêt à publier vos mises à jour, sélectionnez **Passer en revue et publier**.
- Pour voir les valeurs de tous les champs, même les champs qui ne sont pas mis à jour, sélectionnez le filtre **Tous les champs**. Vous pouvez modifier les filtres dans ces champs en sélectionnant **Modified fields** (Champs modifiés), puis l’un des filtres ci-dessous :
    - Le filtre **Removed values** (Valeurs supprimées) affiche les champs que vous avez publiés et que vous supprimez maintenant complètement.
    - Le filtre **Added values** (Valeurs ajoutées) affiche les champs que vous n’avez pas publiés à l’origine et que vous ajoutez maintenant.
    - Le filtre **Edited values** (Valeurs modifiées) affiche les champs qui ont été publiés, mais dont vous venez de mettre à jour le contenu.

      >[!NOTE]
      > Si l’un de ces filtres n’est pas disponible, cela signifie que vous n’avez pas effectué de mise à jour de ce type.

- Pour voir uniquement les valeurs qui n’ont pas été mises à jour, sélectionnez le filtre **Unchanged fields** (Champs inchangés). Les valeurs de champ indiquées pour la version publiée et brouillon sont les mêmes.

  ![Filtres pour la comparaison des mises à jour de votre offre publiée ou en préversion](./media/compare-changes-marketplace.png)

>[!NOTE]
> Les pages suivantes ne prennent pas en charge **Comparer** pour l’instant :
>- Public du revendeur CSP
>- Configuration technique du test drive
>- Liste de test drives de la Place de marché
>- Co-vente
>- Fichiers supplémentaires

### <a name="compare-changes-to-published-offer"></a>Comparer les modifications apportées à l’offre publiée

Suivez les instructions ci-dessous pour comparer les modifications apportées à l’offre publiée :

1. Sélectionnez **Comparer** dans la barre de commandes de la page. La page **Comparer** fournit des versions côte à côte des modifications enregistrées de cette offre et de l’offre de Place de marché publiée.
2. Lorsque vous accédez à **Comparer** à partir d’une page spécifique de l’offre, seules les modifications apportées à cette page sont affichées par défaut.
    - Si vous souhaitez comparer les modifications apportées à toutes les pages, changez la page à partir de **Sélectionner une page à comparer** .  
    - Si vous souhaitez comparer les modifications apportées à l’offre dans toutes les pages, sélectionnez **Toutes les pages**.

Par défaut, **Comparer** compare les nouvelles modifications que vous apportez à l’offre de Place de marché en direct.

Pensez à republier votre offre après avoir apporté des mises à jour pour que les modifications prennent effet.

### <a name="compare-changes-to-a-preview-offer"></a>Comparer les modifications apportées à une offre en préversion

Si vous avez des modifications en préversion qui ne sont pas en direct, vous pouvez comparer les nouvelles modifications à l’offre de Place de marché en préversion.

Suivez les instructions ci-dessous pour comparer les nouvelles modifications à votre offre de Place de marché en préversion :

1. Sélectionnez **Comparer** dans la barre de commandes de la page.
2. Sélectionnez la liste déroulante **Par** et remplacez **Live offer** (Offre en direct) par **Preview offer** (Offre en préversion).
3. La page **Comparer** fournit des versions côte à côte qui affichent les modifications.

>[!NOTE]
>Si votre offre n’est pas encore en direct, mais que vous l’avez publiée en préversion, vous ne pouvez pas la comparer à une offre en direct.

Pensez à republier votre offre après avoir apporté des mises à jour pour que les modifications prennent effet.

## <a name="delete-a-draft-offer"></a>Supprimer un brouillon d’offre

Vous pouvez supprimer un brouillon d’offre (qui n’a pas été publié) en sélectionnant **Supprimer le brouillon** à partir de la page **Vue d’ensemble de l’offre**. Cette option ne sera pas disponible pour vous si vous avez publié l’offre précédemment.

Après avoir confirmé que vous souhaitez supprimer le brouillon d’offre, cette dernière ne sera plus ni visible ni accessible dans l’Espace partenaires et la page **Toutes les offres** s’ouvrira.

## <a name="delete-a-draft-plan"></a>Supprimer un plan de projet

Pour supprimer un plan qui n’a pas été publié, sélectionnez **Supprimer le brouillon** à partir de la page **Vue d’ensemble du plan**. Cette option ne sera pas disponible pour vous si vous avez publié l’offre précédemment.

Après avoir confirmé que vous souhaitez supprimer le brouillon de plan, ce dernier ne sera plus ni visible ni accessible dans l’Espace partenaires.

## <a name="cancel-publishing"></a>Annuler la publication

Pour annuler une offre avec l’état **Publication en cours d’exécution** :

1. Sélectionnez le nom de l’offre pour ouvrir la page **Vue d’ensemble de l’offre**.
1. Sélectionnez **Annuler la publication** à partir de l’angle supérieur droit de la page.
1. Confirmez que vous souhaitez arrêter la publication de l’offre.

Si vous souhaitez publier l’offre ultérieurement, vous devrez redémarrer le processus de publication.

> [!NOTE]
> Vous pouvez arrêter la publication d’une offre seulement si elle n’a pas encore atteint l’étape d’approbation de l’éditeur. Après avoir sélectionné **Publier**, vous n’avez plus la possibilité d’annuler la publication.

## <a name="stop-selling-an-offer-or-plan"></a>Arrêter la vente d’une offre ou d’un plan

Pour différentes raisons, vous pouvez décider de supprimer votre liste d’offres de la place de marché commerciale Microsoft. La suppression de l’offre permet de s’assurer que les nouveaux clients ne puissent plus acheter ou déployer votre offre, mais elle n’a aucun impact sur les clients existants.

Pour arrêter la vente d’une offre après sa publication, sélectionnez **Arrêter la vente** à partir de la page **Vue d’ensemble de l’offre**.

Quelques heures après que vous aurez confirmé que vous souhaitez arrêter de vendre l’offre, elle ne sera plus visible dans la place de marché commerciale, et les nouveaux clients ne pourront pas la télécharger.

Pour arrêter de vendre un plan, sélectionnez **Arrêter la vente** à partir de la page **Vue d’ensemble du plan**. L’option d’arrêt d’un plan est uniquement disponible si l’offre contient plus d’un plan. Vous pouvez choisir d’arrêter la vente d’un seul plan sans influer sur les autres plans au sein de votre offre. Une fois que vous avez confirmé vouloir arrêter la vente du plan, vous devez republier l’offre pour que la modification prenne effet. Une fois l’offre republiée, le plan n’est plus visible dans la place de marché commerciale, et aucun nouveau client ne peut le télécharger.

Les clients ayant déjà acquis l’offre ou le plan peuvent continuer à les utiliser. Ils peuvent les télécharger à nouveau, mais ne recevront pas de mises à jour si vous mettez à jour et publiez à nouveau l’offre ou le plan ultérieurement.

Une fois votre demande d’arrêt de la vente de l’offre/du plan effectuée, vous continuez à les voir dans le portail de la place de marché commerciale sur Espace partenaires avec l’état **Non disponible**.

Si vous décidez de répertorier ou de vendre à nouveau cette offre ou ce plan, suivez les instructions pour [mettre à jour une offre existante](#update-an-existing-offer-in-the-commercial-marketplace). N’oubliez pas que vous devrez **publier** à nouveau l’offre ou le plan après y avoir apporté des modifications.

## <a name="remove-offers-from-existing-customers"></a>Supprimer des offres de clients existants

Pour supprimer des offres de clients existants, [enregistrez une demande de support](https://aka.ms/marketplacepublishersupport). Dans la liste des rubriques du support, sélectionnez **Marketplace commercial** > **Déréférencement, suppression ou arrêt d’une offre ou d’une application** et envoyez la demande. L’équipe de support vous guidera tout au long du processus de suppression de l’offre.

## <a name="sync-private-plan-audiences"></a>Synchroniser les audiences de plan privées

Si votre offre inclut un ou plusieurs plans configurés pour être uniquement disponibles pour une audience privée limitée, il est possible de mettre à jour uniquement l’audience pouvant accéder à ce plan privé sans publier d’autres modifications de l’offre. 

Pour mettre à jour et synchroniser l’audience privée pour votre ou vos plans :

- Modifiez l’audience dans un ou plusieurs plans privés à l’aide du bouton + **Ajouter ID** ou **Importer les clients (csv)** , puis enregistrez les modifications.
- Sélectionnez **Synchroniser l’audience privé** à partir de la page **Vue d’ensemble du plan**.

**Synchroniser l’audience privé** publiera uniquement les modifications apportées à votre audience privée, sans publier d’autres mises à jour éventuellement apportées au brouillon de l’offre.

## <a name="next-steps"></a>Étapes suivantes

- [Vérifier l’état de publication de votre offre de place de marché commerciale](./publishing-status.md)
