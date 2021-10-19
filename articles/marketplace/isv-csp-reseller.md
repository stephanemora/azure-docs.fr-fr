---
title: Configurer les offres privées fournisseur ISV à revendeur cloud dans l’Espace partenaires Microsoft
description: Configurez les offres privées fournisseur ISV à revendeur cloud dans l’Espace partenaires Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emerb19
ms.author: emerb
ms.date: 10/07/2021
robots: noindex
ms.openlocfilehash: 28f14cf4414266e6395ec60432c6d2e106a1a69a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716071"
---
# <a name="isv-to-cloud-reseller-private-offers"></a>Offres privées fournisseur ISV à revendeur cloud

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique.

## <a name="overview"></a>Vue d’ensemble

Les offres privées permettent aux éditeurs de logiciels indépendants (ISV) et aux revendeurs cloud (partenaires CSP) d’augmenter leurs revenus en créant des marges personnalisées limitées dans le temps qui répondent aux besoins métier de chaque entité.

En tant que fournisseur ISV, vous pouvez spécifier la marge et la durée pour créer un prix de vente en gros pour votre partenaire CSP. Quand votre partenaire fait une vente à un client, Microsoft vous paie à partir du prix de vente en gros.

En tant que partenaire CSP, vous pouvez découvrir toutes les marges disponibles à l’aide de l’interface utilisateur ou de l’API de l’Espace partenaires. Pour toute vente effectuée, vous recevez votre facture de Microsoft au prix de vente en gros. Vous continuez à définir le prix du client et à facturer votre client en dehors de la place de marché. En savoir plus sur l’expérience de partenaire CSP pour les offres privées à la rubrique [Découverte des marges configurées par les fournisseurs ISV](/partner-center/csp-commercial-marketplace-margins).

:::image type="content" source="media/isv-csp-cloud/isv-private-offer-experience.png" alt-text="Montre la progression de l’expérience d’offre privée ISV.":::

:::image type="content" source="media/isv-csp-cloud/csp-private-offer-experience.png" alt-text="Montre la progression de l’expérience d’offre privée CSP.":::

> [!NOTE]
> Si vous publiez uniquement votre offre sur Microsoft AppSource (elle n’est donc pas disponible sur la place de marché Azure) et que vous avez choisi de la vendre via des partenaires dans le programme Fournisseur de solutions Cloud (CSP), il est possible que votre offre s’affiche avec un certain retard comme étant disponible à la vente sur le portail des partenaires CSP. Veuillez contacter le [support](/azure/marketplace/support) si vous pensez que votre offre n’est pas disponible pour les transactions pour votre partenaire CSP.

## <a name="prerequisites-to-create-a-private-offer-for-cloud-resellers"></a>Prérequis pour la création d’une offre privée pour les revendeurs cloud

Ces prérequis doivent être respectés pour la création d’une offre privée pour les revendeurs cloud :

1. Vous avez créé un compte de la place de marché commerciale dans l’Espace partenaires.
2. Votre compte est inscrit dans le programme de la place de marché commerciale.
3. Vous avez publié une offre pouvant faire l’objet d’une transaction et disponible publiquement sur la Place de marché Azure.
4. Votre offre est activée dans le canal du revendeur cloud.
5. Vous créez une offre privée pour un revendeur cloud qui fait partie du programme Fournisseur de solutions Cloud (CSP) de Microsoft.

### <a name="supported-offer-types"></a>Types d’offres pris en charge

Des offres privées peuvent être créées pour tous les types d’offres pouvant faire l’objet d’une transaction de la place de marché. Cela comprend les solutions SaaS (Software as a Service), Machines virtuelles Azure et Applications Azure.

> [!NOTE]
> Les marges sont appliquées à toutes les dimensions de compteur personnalisées de votre offre. Les marges sont appliquées uniquement aux frais de logiciels que vous avez définis, et pas aux frais de matériel de l’infrastructure Azure associés.

## <a name="private-offers-dashboard"></a>Tableau de bord des offres privées

Le tableau de bord des **offres privées** dans le menu de navigation de gauche de l’Espace partenaires est votre emplacement centralisé pour créer et gérer des offres privées. Le tableau de bord présente deux onglets :

1. **Clients** : en savoir plus sur les offres privées pour les clients quand la fonctionnalité passe en préversion privée à l’automne 2021.
2. **Revendeurs cloud** : ouvre le tableau de bord de l’offre privée du revendeur cloud qui vous permet de :

    - Créer des offres privées
    - Afficher l’état de l’ensemble de vos offres privées
    - Cloner les offres privées existantes
    - Retirer des offres privées
    - Supprimer des offres privées

## <a name="create-a-new-private-offer-for-cloud-resellers"></a>Créer une offre privée pour les revendeurs cloud

1. Connectez-vous à l’Espace partenaires.
2. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
3. Sélectionnez l’onglet **Revendeurs cloud**.
4. Sélectionnez **+ Nouvelle offre privée**.
5. Entrez un nom d’offre privée. Il s’agit d’un nom descriptif que vous allez utiliser pour faire référence à votre offre privée dans l’Espace partenaires. Ce nom n’est pas visible par les revendeurs cloud.  

### <a name="offer-setup"></a>Configuration de l’offre

La page de configuration de l’offre vous permet de définir les conditions de l’offre privée, le contact de notification, la tarification et les revendeurs cloud.

1. Les **conditions de l’offre privée** déterminent la durée pendant laquelle vos revendeurs cloud peuvent découvrir et vendre votre offre privée.

    - Pour que votre offre privée démarre immédiatement, choisissez comme **Date de début** « Dès que possible ». Si tous les prérequis sont remplis, votre offre privée est disponible dans un délai de 15 minutes après l’envoi. Si une offre privée est étendue à un client existant d’un produit avec paiement à l’utilisation, le tarif privé est applicable pour l’ensemble du mois.
    - Pour que votre offre privée commence ultérieurement à un mois spécifique, sélectionnez un **Mois spécifique** et effectuez une sélection. La date de début de cette option est toujours le premier du mois.
    - Choisissez le mois pour la **Date d’expiration** de votre offre privée. Il s’agit toujours du dernier jour du mois.

2. Fournissez jusqu’à cinq e-mails en tant que **Contacts de notification** pour recevoir des mises à jour par e-mail sur l’état de votre offre privée. Ces e-mails sont envoyés quand votre offre privée devient **Active**, quand elle est **Terminée** ou **Retirée**.

3. Configurez un pourcentage de marge de **Tarification** pour jusqu’à 10 offres/plans dans une offre privée. La marge reçue par le revendeur cloud est un pourcentage du tarif de votre plan sur la place de marché.

    - Sélectionnez **+ Ajouter des offres/plans** pour choisir les offres/plans auxquels vous souhaitez fournir une offre privée.
    - Choisissez si vous souhaitez fournir une offre privée au niveau de l’offre (un tarif privé est associé à l’ensemble des plans actuels et futurs sous cette offre) ou au niveau du plan (le tarif privé est associé uniquement au plan que vous avez sélectionné).
    - Choisissez jusqu’à 10 offres/plans, puis sélectionnez **Ajouter**.
    - Entrez le pourcentage de marge à fournir à vos revendeurs cloud. La marge que vous fournissez est calculée en tant que pourcentage du tarif de votre plan sur la place de marché.

    > [!NOTE]
    > Seuls les offres/plans pouvant faire l’objet d’une transaction dans Microsoft AppSource ou la place de marché Azure s’affichent dans le menu de sélection.

4. Sélectionnez les **revendeurs cloud** autorisés à vendre votre offre privée.

    1. Sélectionnez **+Ajouter des revendeurs cloud**.
    2. Recherchez votre revendeur cloud par nom/ID de locataire. Vous pouvez également effectuer une recherche en appliquant des filtres, comme des régions, des qualifications ou des compétences.
    3. Choisissez les revendeurs cloud et sélectionnez **Ajouter**.

    > [!NOTE]
    > - Vous pouvez sélectionner uniquement les revendeurs cloud qui font partie du programme Fournisseur de solutions Cloud (CSP) de Microsoft. 
    > - Une fois votre offre privée terminée, les revendeurs cloud autorisés peuvent continuer à vendre votre offre de la place de marché au tarif de la liste.
    > - Les offres privées peuvent être étendues à un maximum de 400 locataires de revendeurs cloud.

### <a name="review-and-submit"></a>Passer en revue et envoyer

Cette page vous permet de passer en revue l’ensemble des informations que vous avez fournies.

Une fois envoyées, les offres privées ne peuvent pas être modifiées. Assurez-vous que vos informations sont correctes.

Quand vous êtes prêt, cliquez sur **Envoyer**. Vous êtes redirigé vers le tableau de bord dans lequel vous pouvez afficher l’état de votre offre privée. Le ou les contacts de notification reçoivent un e-mail quand l’offre privée devient active.

## <a name="view-private-offers-status"></a>Afficher l’état des offres privées

Pour afficher l’état de votre offre privée :

1. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
2. Sélectionnez l’onglet **Revendeurs cloud**.
3. Examinez la colonne **État**.

L’état de l’offre privée peut être :

- **Brouillon** : vous avez commencé le processus de création d’une offre privée, mais vous ne l’avez pas encore envoyée.
- **En cours** : vous avez envoyé une offre privée qui est actuellement en cours de publication dans nos systèmes.
- **Active** : votre offre privée est détectable et peut faire l’objet d’une transaction par les revendeurs cloud.
- **Terminé** : votre offre privée a expiré ou a dépassé sa date de fin.

## <a name="clone-a-private-offer"></a>Cloner une offre privée

Cloner une offre privée vous permet d’en créer rapidement une nouvelle.

1. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
2. Sélectionnez l’onglet **Revendeurs cloud**.
3. Cochez la case de l’offre privée que vous souhaitez cloner.
4. Sélectionnez **Cloner**.
5. Entrez le nom de la nouvelle offre privée.
6. Sélectionnez **Cloner**.
7. Modifiez les détails de la page de configuration de l’offre selon vos besoins.
8. Sélectionnez **Soumettre** la nouvelle offre privée.

## <a name="withdraw-a-private-offer"></a>Retirer une offre privée

Quand vous retirez une offre privée, vos revendeurs cloud cessent immédiatement de recevoir une marge, et tous les achats futurs sont effectués au tarif de la liste.

> [!IMPORTANT]
> Les offres privées peuvent être retirées uniquement si elles n’ont pas été vendues à un client par un revendeur cloud.

Pour retirer une offre privée :

1. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
2. Sélectionnez l’onglet **Revendeurs cloud**.
3. Cochez la case de l’offre privée que vous souhaitez retirer.
4. Sélectionnez **Retirer**.
5. Sélectionnez **Demander le retrait**.
6. Votre ou vos contacts de notification reçoivent un e-mail si l’offre privée a été retirée avec succès.

## <a name="delete-a-private-offer"></a>Supprimer une offre privée

Pour supprimer une offre privée qui est à l’état de brouillon :

1. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
2. Sélectionnez l’onglet **Revendeurs cloud**.
3. Cochez la case de l’offre privée que vous souhaitez supprimer.
4. Sélectionnez **Supprimer**.
1. Sélectionnez **Confirmer**.

## <a name="find-additional-details"></a>Rechercher des détails supplémentaires

Quand la publication de votre offre privée est en cours, vous pouvez afficher des détails supplémentaires sur l’état actuel :

1. Sélectionnez **Offres privées** dans le menu de navigation de gauche pour ouvrir le tableau de bord.
2. Sélectionnez l’onglet **Revendeurs cloud**.
3. Sélectionnez le lien hypertexte « En cours » de l’offre privée dans la colonne **État**.

Les détails supplémentaires peuvent être les suivants :

- **Autorisation du revendeur cloud en cours** : nous effectuons actuellement l’autorisation du revendeur de cloud donné afin qu’il puisse vendre votre offre.
- **Publication d’offre privée en cours** : nous publions actuellement le tarif privé du revendeur cloud donné.
- **Active** : l’offre privée est désormais active pour ce revendeur cloud.

## <a name="reporting-on-private-offers"></a>Création de rapports sur les offres privées

Le montant du paiement et les frais d’agence que Microsoft facture sont basés sur le tarif après l’application de la marge pour les articles avec une marge associée.

## <a name="next-steps"></a>Étapes suivantes

- [Questions fréquentes (FAQ) ](/azure/marketplace/isv-csp-faq) sur la configuration des offres privées ISV à revendeur cloud dans l’Espace partenaires
