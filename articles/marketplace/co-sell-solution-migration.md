---
title: Migrer des solutions de co-vente de GTM OCP vers Espace partenaires pour Microsoft AppSource
description: Découvrez comment migrer des solutions de co-vente de GTM OCP vers Espace partenaires pour Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 05/18/2021
ms.openlocfilehash: d4eb677b0f55a8ccd2cda33e6026e01238deb9b3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971532"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migration de solutions de co-vente de GTM OCP vers le marketplace commercial

Microsoft déplace l’expérience de publication. Le [marketplace commercial](overview.md) propose une publication simplifiée des offres pour la co-vente via les trois canaux de Microsoft en centralisant la création et la gestion des offres dans Espace partenaires, où vous gérez déjà votre relation avec Microsoft.

En tant que partenaire Microsoft inscrit sur le marketplace commercial, vous pouvez :

- publier vos offres de manière centralisée et co-vendre sur les canaux de clients directs, de partenaires et de vendeurs de Microsoft ;
- vous assurer que vos offres se trouvent dans la bonne boutique en ligne ([Microsoft AppSource](https://appsource.microsoft.com) ou [Place de marché Azure](https://azure.microsoft.com)) pour atteindre les millions de clients du cloud qui s’alignent sur les capacités de votre offre ;
- mener votre propre expérience de publication pour co-vendre les offres qui correspondent à vos objectifs commerciaux ;
- aligner la publication de votre offre sur Espace partenaires, où vous gérez déjà votre relation avec Microsoft et les possibilités de co-ventes ;
- déverrouiller [Marketplace Rewards](marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Conditions préalables pour continuer à co-vendre avec Microsoft

Vérifiez que vous êtes un membre actif du programme Microsoft Partner Network et que vous êtes inscrit au marketplace commercial dans Espace partenaires.

- Rejoignez le programme Microsoft Partner Network [gratuitement](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership). En tant que partenaire, vous aurez accès à des ressources, programmes, outils et connexions exclusifs pour développer votre activité.
- Si vous n’avez pas de compte sur le marketplace commercial, [inscrivez-vous dès maintenant](create-account.md) pour continuer à co-vendre avec Microsoft et accéder à l’expérience complète de publication.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Publication de mises à jour pour obtenir l’état co-sell-ready

Pour que votre solution soit détectable par les vendeurs et les partenaires de Microsoft, elle doit respecter les [exigences de la co-vente](./co-sell-overview.md). Pour qu’un vendeur Microsoft soit encouragés, votre solution doit répondre aux [exigences d’incitation](./co-sell-overview.md). Remplissez ces conditions dans l’onglet Co-vente d’Espace partenaires (voir [cette image](#action-2-merge) plus loin dans cet article).

> [!NOTE]
> Sur le marketplace commercial, vos solutions sont appelées « offres » tout au long de l’expérience de publication.

Une fois inscrit sur le marketplace commercial, préparez-vous à migrer vos solutions à partir de GTM OCP. Nous avons créé une interface de migration numérique qui fournit une vue unique des solutions GTM OCP pour votre organisation. À partir de cette interface, vous serez en mesure de fusionner la solution avec une offre similaire déjà publiée et en ligne sur le marketplace commercial ou de la supprimer.

Procédez comme suit avant d’importer vos solutions à partir de GTM OCP :

1. Accédez à la [liste des éditeurs](https://partner.microsoft.com/dashboard/account/v3/publishers/list) de votre entreprise. Elle comprend le propriétaire du compte, les managers et les développeurs qui ont accès à la publication. En savoir plus sur les [rôles d’utilisateur d’Espace partenaires](user-roles.md).
2. Demandez à l’un des contacts de la liste d’[ajouter des utilisateurs](https://partner.microsoft.com/dashboard/account/usermanagement) au marketplace commercial en tant que *managers* ou *développeurs*, car seuls ces rôles peuvent modifier et publier des solutions.
3. Collaborez avec vos développeurs pour déplacer vos solutions de votre compte GTM OCP au marketplace commercial.
4. Choisissez l’une des opérations suivantes :
    1. Si vous avez une solution dans GTM OCP que vous souhaitez migrer vers Espace partenaires (*pour conserver le pipeline de référence, les garanties, l’état de co-vente et les primes d’encouragement*), vous avez le choix entre deux scénarios :
        - S’il n’existe aucune offre similaire dans le marketplace commercial
            - Étape 1. [Créer une offre](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)
            - Étape 2. [La publier sur le marketplace commercial](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)
            - Étape 3. [Demander la fusion de la solution dans GTM OCP à l’offre nouvellement publiée sur le marketplace](#begin-the-migration-of-your-solutions-from-ocp-gtm)
        - S’il existe déjà une offre similaire dans le marketplace commercial
            - Étape 1. [Demander la fusion de la solution dans GTM OCP à l’offre publiée sur le marketplace](#begin-the-migration-of-your-solutions-from-ocp-gtm)
    1. Si vous avez une solution dans GTM OCP qui n’est plus pertinente, abandonnez cette solution.

> [!IMPORTANT]
> Le 17 mai 2021, l’option de migration ci-dessous a été supprimée.  Si vous avez déjà utilisé l’option de migration et que vous disposez d’une offre qui a été migrée vers le marketplace commercial en tant qu’ébauche d’offre, il est important que vous terminiez le processus de publication sur le marketplace commercial.  
> - En savoir plus sur [la révision et la publication d’une offre sur le marketplace commercial de Microsoft | Microsoft Docs](./review-publish-offer.md)
> - [Publication dans le jeu de formation du marketplace commercial](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm)
:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="Je souhaite migrer cette solution vers le marketplace commercial":::

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>Commencer la migration de vos solutions à partir de GTM OCP

1. Commencez la migration [ici](https://partner.microsoft.com/solutions/migration#).
2. Sélectionnez la page **Vue d’ensemble**, puis **Cliquez ici pour commencer**.

    :::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="Affiche la page de présentation":::

3. Pour commencer la migration, sélectionnez l’onglet **Solutions**, qui affiche toutes les solutions associées à vos ID MPN.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Page Vue d’ensemble d’Espace partenaires, onglet Solutions.":::

    > [!NOTE]
    > Cet onglet indique si aucune solution n’est en attente de migration vers le marketplace commercial. Pour continuer à co-vendre avec Microsoft, assurez-vous que les solutions migrées sont publiées sur le marketplace commercial.

    Pour en savoir plus sur l’état de la solution, consultez les info-bulles. Toutes les solutions en attente d’action sont répertoriées sous **Action**.<a name="beginmigration"></a>

4. Sélectionnez **Commencer la migration** (voir l’image ci-dessus) pour la solution que vous souhaitez migrer, puis sélectionnez l’une des options suivantes :

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="Trois options pour la migration.":::

### <a name="merge-solution"></a>Fusionner la solution

Quel est l’intérêt de fusionner votre solution GTM OCP avec une offre publiée et en ligne sur le marketplace commercial ?

- Les partenaires qui co-vendent actuellement des offres dans GTM OCP sont encouragés à migrer leurs solutions vers le marketplace commercial pour tirer parti de ses capacités et simplifier l’expérience de publication.

Si vous avez une solution dans GTM OCP que vous souhaitez migrer vers Espace partenaires, vous avez le choix entre deux scénarios. À la fin de ce processus de fusion, votre offre conservera le pipeline de référence, les garanties, l’état de co-vente et les primes d’encouragement.

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>Étape 1 : Une offre similaire n’existe pas dans le marketplace commercial. Procédez comme suit

Si vous ne disposez pas déjà d’une offre dans le marketplace commercial avec laquelle fusionner une solution dans GTM OCP, vous devez d’abord créer ET PUBLIER une offre sur le marketplace commercial (celle-ci conservera son état de co-vente, ses primes d’encouragement et son pipeline de référence).

1. Créez votre ébauche d’offre dans le marketplace commercial.

   1. Sélectionnez **+Nouvelle offre**.

        :::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="Affichage de l’option Nouvelle offre":::

   2. Renseignez les informations requises dans chaque onglet. • Les liens **En savoir plus** et les info-bulles vous guideront tout au long des exigences et des détails.
         • Si vous le souhaitez, renseignez la page **Revendre via des CSP** (dans le menu de navigation de gauche ci-dessous) pour revendre par le biais du programme Fournisseur de solutions Cloud (CSP).

        :::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="Affiche la page de configuration de l’offre avec les options de vue d’ensemble mises en surbrillance.":::
   3. Sélectionnez **Enregistrer le brouillon**.
        - Pour obtenir des instructions détaillées sur les informations que vous devez fournir avant de pouvoir publier votre offre, lisez le [guide de publication](./publisher-guide-by-offer-type.md) approprié.
        - Passez en revue les conditions d’éligibilité dans l’article correspondant à votre type d’offre afin de finaliser la sélection et la configuration de votre offre.
        - Passez en revue les modèles de publication de chaque magasin en ligne pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
        - [Meilleures pratiques pour le référencement d’offres – Marketplace commercial de Microsoft | Microsoft Docs](./gtm-offer-listing-best-practices.md).

    > [!TIP]
    > Nous vous recommandons de ne *pas renseigner* les données de l’onglet **Co-vendre avec Microsoft**. Pour vous faire gagner du temps, nous nous chargerons de renseigner ces données avec vos garanties existantes dans GTM OCP pendant le processus de fusion.

    Une fois la fusion terminée, vous pouvez revenir à l’onglet Co-vendre avec Microsoft et effectuer des mises à jour le cas échéant. Pour plus d’informations, consultez [Configurer la co-vente pour une offre commerciale de la Place de marché](./co-sell-configure.md).
1. Lorsque vous avez terminé, sélectionnez **Vérifier et publier**.

     :::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="La page Co-vendre avec Microsoft s’affiche avec les options en surbrillance":::
1. Après avoir examiné toutes les informations envoyées, sélectionnez **Publier** pour soumettre votre ébauche d’offre à l’examen de certification. [En savoir plus sur la phase de certification](./review-publish-offer.md).:::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="Affiche la page Réviser et publier.":::
1. Suivez l’état de votre envoi dans l’onglet Vue d’ensemble.

    :::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="Affiche l’onglet Vue d’ensemble":::
1. Nous vous informerons lorsque l’examen de certification sera terminé. Si nous fournissons des commentaires actionnables, résolvez-les, puis sélectionnez **Publier** pour lancer une nouvelle certification.

1. Une fois que votre offre est certifiée, affichez un aperçu de l’offre à l’aide du lien fourni et effectuez vos ajustements finaux. Quand vous êtes prêt, sélectionnez **Mettre en ligne** (voir le bouton ci-dessus) pour publier votre offre sur la ou les vitrines pertinentes du marketplace commercial.

1. **Passez au scénario 2 ci-dessous pour terminer le processus de fusion.**

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>Scénario 2 : Une offre similaire existe dans le marketplace commercial. Procédez comme suit

Sélectionnez cette option si la solution est déjà publiée et en ligne sur le marketplace commercial et que la solution dans GTM OCP et l’offre dans le marketplace commercial doivent être fusionnées en une seule offre. Cela permet d’éviter de créer des offres en double.

1. Identifiez l’offre existante.
   1. Sélectionnez **Elle est similaire à une offre existante dans le marketplace commercial** (voir l’image **Commencer votre migration** [ci-dessus](#beginmigration)).
   1. L’onglet **Action 1** affiche les offres actives sur le marketplace commercial auxquelles votre solution GTM OCP peut être associée. Sélectionnez l’offre active dans la liste, si vous en avez une. S’il n’existe aucune liste d’offres dans laquelle choisir, entrez l’adresse orientée client (URL) de Microsoft AppSource ou de Place de marché Azure.
   1. Sélectionnez **Continuer**.
        [![Onglet Action 1 du processus de fusion.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
1. Demandez la fusion.
    1. L’onglet **Action 2** indique les instructions pour demander la fusion de votre solution GTM OCP avec celle que vous avez identifiée. Pour demander la fusion, sélectionnez **Enregistrer et contacter le support**, ce qui ouvre la page de support du partenaire dans un navigateur. 
    <a id="action-2-merge"></a>
        [![Onglet Action 2 du processus de fusion.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Sélectionnez **Fournir les détails du problème** et entrez les informations suivantes :
        1. Type d’offre
        1. Nom de l’offre
        1. ID de solution OCP
        1. URL de l’annonce existante
    1. Sélectionnez **Envoyer**. Le support technique du partenaire vous contactera dans les deux jours ouvrables.
    1. Le support du partenaire travaillera avec vous pour garantir la réussite de la fusion de cette offre, afin qu’elle soit publiée en tant qu’offre active.

### <a name="discard-this-solution"></a>Abandonner cette solution

Nous vous recommandons de prendre le temps d’évaluer la viabilité et la pertinence de vos solutions afin d’optimiser votre présence et votre visibilité sur le marketplace commercial avec des offres de haute qualité qui répondent à la demande des clients.

Sélectionnez cette option lorsqu’une solution parmi les solutions GTM OCP n’est plus pertinente. Il vous sera demandé de confirmer l’abandon, et vous pourrez également l’annuler ultérieurement.

1. Sélectionnez **Je souhaite abandonner cette solution** (voir l’image **Action requise** [ci-dessus](#beginmigration)), puis **Continuer**.
2. Sélectionnez **Abandonner**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Confirmer l’abandon.":::

3. Pour annuler l’abandon, sélectionnez **Annuler l’abandon**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="Lien Annuler l’abandon.":::

### <a name="additional-help"></a>Aide supplémentaire

 Si vous avez besoin d’une aide supplémentaire, sélectionnez l’onglet **Obtenir de l’aide** pour contacter le support technique du partenaire.

:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="Lien du support sous l’onglet Obtenir de l’aide.":::

## <a name="next-steps"></a>Étapes suivantes

- [Revendre via des partenaires CSP](cloud-solution-providers.md)
- [Configurer la co-vente pour une offre de la Place de marché commerciale](./co-sell-configure.md)
- Consulter ces [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)