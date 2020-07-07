---
title: Fournisseur de solutions Cloud - Place de marché commerciale Microsoft
description: Découvrez comment vendre vos offres via le réseau de partenaires du programme des fournisseurs de solutions Microsoft Cloud (CSP) de la place de marché commerciale.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: 633656af72790c7dfebeee567713875ce0d609b4
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964207"
---
# <a name="cloud-solution-provider-program"></a>Programme des fournisseurs de solution Cloud

Cet article explique comment configurer votre offre pour qu’elle soit disponible au sein du programme des fournisseurs de solutions Cloud (CSP). Outre la publication de vos offres via les [vitrines web de la place de la place de marché commerciale](comparing-appsource-azure-marketplace.md), vous pouvez également vendre par le biais du programme CSP pour atteindre les millions de clients Microsoft qualifiés faisant partie du programme.

Vous pouvez configurer des offres nouvelles ou existantes pour les rendre disponibles aux clients donnant leur consentement dans le programme CSP. Cela permet aux partenaires de vendre vos produits et de créer des solutions groupées pour les clients.

Les éditeurs sont chargés de fournir le support de garantie de réparation pour les clients finaux et pour fournir un mécanisme pour les partenaires du programme CSP et/ou les clients de vous contacter pour la prise en charge. Il est recommandé de fournir aux partenaires du programme CSP la documentation utilisateur, des formations et les notifications d’état ou de défaillance du service (le cas échéant), afin que les partenaires du programme CSP soient en mesure de traiter les demandes de support de niveau 1 des clients.  

Les offres suivantes peuvent être choisies pour être vendues par des partenaires du programme CSP :

- Offres de transaction SaaS (software as a service)
- Machines virtuelles (VM)
- Modèles de solution
- Applications managées

> [!NOTE]
> Par défaut, les références SKU de machines virtuelles de type BYOL (apportez votre propre licence) et Conteneurs peuvent être choisies pour être vendues par des partenaires du programme CSP.

## <a name="how-to-configure-an-offering"></a>Comment configurer une offre

Le paramètre d’acceptation du programme CSP est configuré dans l’expérience de création de l’offre de l’Espace partenaires ou du portail Cloud Partner. [En savoir plus sur l’évolution de l’expérience pour les éditeurs](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Abonnement à l’Espace partenaires

Dans l’Espace partenaires, vous trouverez l’expérience d’abonnement dans le module d’audience du revendeur CSP.

![Audience du revendeur CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Dans le module d’audience du revendeur CSP, vous avez le choix entre trois options :

- Option 1 : Tous les partenaires du programme CSP
- Option 2 : Partenaires spécifiques du programme CSP que je sélectionne
- Option 3 : Aucun partenaire du programme CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Option 1 : Tous les partenaires du programme CSP

![Tous les partenaires du programme CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 En choisissant cette option, tous les partenaires du programme CSP peuvent revendre votre offre à leurs clients.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Option 2 : Partenaires spécifiques du programme CSP que je sélectionne

![Partenaires spécifiques du programme CSP que je sélectionne](media/marketplace-publishers-guide/csp-reseller-option-two.png)

En choisissant cette option, vous choisissez les partenaires du programme CSP qui peuvent revendre votre offre.

Pour choisir les partenaires, cliquez sur **Sélectionner des partenaires CSP** et un menu s’affiche pour vous permettre d’effectuer une recherche par nom de partenaire ou ID de locataire CSP Azure Active Directory (AAD).

![Menu Sélectionner CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Vous pouvez appliquer des filtres de recherche, comme **Pays**, **Compétence** ou **Qualification**.

![Filtres Pays/région, Compétence et Qualification pour la recherche de partenaires](media/marketplace-publishers-guide/csp-add-resellers.png)

Une fois que vous avez choisi la liste des partenaires, sélectionnez **Ajouter**.

![Exemple de liste de partenaires autorisés dans le programme CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Un tableau affichant la liste des partenaires que vous avez sélectionnés apparaît sur la page d’audience du revendeur CSP.

![Tableau avec liste des partenaires sur la page d’audience du revendeur CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Sélectionnez **Enregistrer le brouillon** pour enregistrer vos modifications.

Si cette offre n’est pas publiée, vous devez publier votre offre afin de la rendre disponible pour vos partenaires sélectionnés.

>[!NOTE]
>Si vous autorisez un partenaire dans le programme CSP dans une région donnée, celui-ci peut vendre l’offre à n’importe quel client appartenant à cette région spécifique. Pour plus d’informations sur la façon dont les offres CSP sont classées par région, consultez [Marchés régionaux et devise du programme Fournisseur de solutions cloud (CSP)](https://docs.microsoft.com/partner-center/regional-authorization-overview).

Si vous mettez à jour la liste CSP pour une offre déjà publiée, ajoutez les partenaires supplémentaires et sélectionnez **Synchroniser l’audience CSP**.

Si vous disposez d’une offre qui possède déjà une liste de partenaires autorisés et que vous souhaitez utiliser la même liste pour une autre offre, utilisez **Importer/Exporter**. Accédez à l’offre qui contient la liste CSP, puis sélectionnez **Exporter les Fournisseurs de solutions cloud**. La fonction développe un fichier .csv qui peut être importé dans une autre offre.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Option 3 : Aucun partenaire du programme CSP

![Aucun partenaire du programme CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

En choisissant cette option, vous désactivez votre offre dans le programme CSP. Vous pouvez modifier cette sélection à tout moment.

### <a name="cloud-partner-portal-opt-in"></a>Abonnement au portail Microsoft Cloud Partner

Dans le portail Microsoft Cloud Partner, l’abonnement est défini dans l’onglet Place de marché ou Vitrine. La possibilité de choisir des partenaires spécifiques dans le programme CSP est uniquement disponible dans l’Espace partenaires.

![Expérience d’acceptation CSP dans le portail Cloud Partner](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Supprimer l’autorisation de partenaires du programme CSP

Si vous avez autorisé un partenaire dans le programme CSP et que ce partenaire a déjà revendu le produit à ses clients, vous ne pourrez pas supprimer l’autorisation de ce partenaire.

Si un partenaire du programme CSP n’a pas vendu votre produit à ses clients et que vous souhaitez supprimer le CSP après la publication de votre offre, suivez les instructions ci-dessous :

1. Accédez à la [page de demande de support](https://aka.ms/marketplacepublishersupport). Les premiers menus déroulants sont automatiquement remplis pour vous.

   > [!NOTE]
   > Ne changez pas les sélections préremplies des menus déroulants.

2. Pour **Sélectionner la version du produit**, sélectionnez **Gestion des offres en direct**.
3. Pour **Sélectionner une catégorie qui décrit le mieux le problème**, choisissez la catégorie qui fait référence à votre offre.
4. Pour **Sélectionner un problème décrivant le mieux le vôtre**, sélectionnez **Mettre à jour l’offre existante**.
5. Sélectionnez **Suivant** pour être redirigé vers la **page des détails du problème** pour entrer plus d’informations sur votre problème.
6. Utilisez **Supprimer l’autorisation CSP** comme titre du problème et renseignez le reste des sections requises.




## <a name="navigate-between-options"></a>Naviguer entre les options

Utilisez cette section pour naviguer entre les trois options pour le revendeur CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Naviguer à partir de l’option 1 : Tous les partenaires du programme CSP

Si votre offre est actuellement **Option 1 : Tous les partenaires du programme CSP** et que vous souhaitez accéder à l’une des deux autres options, suivez les instructions ci-dessous pour créer une demande :

1. Accédez à la [page de demande de support](https://aka.ms/marketplacepublishersupport). Les premiers menus déroulants sont automatiquement remplis pour vous.

   > [!NOTE]
   > Ne changez pas les sélections préremplies des menus déroulants.

2. Pour **Sélectionner la version du produit**, sélectionnez **Gestion des offres en direct**.
3. Pour **Sélectionner une catégorie qui décrit le mieux le problème**, choisissez la catégorie qui fait référence à votre offre.
4. Pour **Sélectionner un problème décrivant le mieux le vôtre**, sélectionnez **Mettre à jour l’offre existante**.
5. Sélectionnez **Suivant** pour être redirigé vers la **page des détails du problème** pour entrer plus d’informations sur votre problème.
6. Utilisez **Supprimer l’autorisation CSP** comme titre du problème et renseignez le reste des sections requises.

> [!NOTE]
> Si vous essayez d’accéder à l’option 2 et qu’un partenaire du programme CSP a déjà revendu l’offre à ses clients, ce partenaire est, par défaut, déjà inclus dans votre liste de partenaires autorisés.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Naviguer à partir de l’option 2 : Partenaires spécifiques du programme CSP que je sélectionne

Si votre offre est actuellement **Option 2 : Partenaires spécifiques du programme CSP que je sélectionne** et que vous souhaitez accéder à **Option 1 : Tous les partenaires du programme CSP**, suivez les instructions ci-dessous pour créer une demande :

1. Accédez à la [page de demande de support](https://aka.ms/marketplacepublishersupport). Les premiers menus déroulants sont automatiquement remplis pour vous.

   > [!NOTE]
   > Ne changez pas les sélections préremplies des menus déroulants.

2. Pour **Sélectionner la version du produit**, sélectionnez **Gestion des offres en direct**.
3. Pour **Sélectionner une catégorie qui décrit le mieux le problème**, choisissez la catégorie qui fait référence à votre offre.
4. Pour **Sélectionner un problème décrivant le mieux le vôtre**, sélectionnez **Mettre à jour l’offre existante**.
5. Sélectionnez **Suivant** pour être redirigé vers la **page des détails du problème** pour entrer plus d’informations sur votre problème.
6. Utilisez **Supprimer l’autorisation CSP** comme titre du problème et renseignez le reste des sections requises.

 Si votre offre est actuellement **Option 2 : Partenaires spécifiques du programme CSP que je sélectionne** et que vous souhaitez accéder à **Option 3 : Aucun partenaire du programme CSP**, vous ne pouvez accéder à cette option que si les partenaires du programme CSP que vous aviez précédemment autorisés n’ont pas revendu votre offre aux clients finaux. Utilisez les instructions suivantes pour créer une demande :

1. Accédez à la [page de demande de support](https://aka.ms/marketplacepublishersupport). Les premiers menus déroulants sont automatiquement remplis pour vous.

   > [!NOTE]
   > Ne changez pas les sélections préremplies des menus déroulants.

2. Pour **Sélectionner la version du produit**, sélectionnez **Gestion des offres en direct**.
3. Pour **Sélectionner une catégorie qui décrit le mieux le problème**, choisissez la catégorie qui fait référence à votre offre.
4. Pour **Sélectionner un problème décrivant le mieux le vôtre**, sélectionnez **Mettre à jour l’offre existante**.
5. Sélectionnez **Suivant** pour être redirigé vers la **page des détails du problème** pour entrer plus d’informations sur votre problème.
6. Utilisez **Supprimer l’autorisation CSP** comme titre du problème et renseignez le reste des sections requises.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Naviguer à partir de l’option 3 : Aucun partenaire du programme CSP

Si votre offre est actuellement **Option 3 : Aucun partenaire du programme CSP**, vous pouvez accéder à l’une des deux autres options à tout moment.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Partage de la documentation commerciale et de support avec les partenaires du programme CSP

Envoyez des documents relatifs aux ventes et au support qui seront mis à la disposition des revendeurs afin d’aider les partenaires du programme CSP à identifier votre offre et à collaborer avec votre organisation efficacement. Ces ressources ne seront pas exposées aux clients dans les vitrines de la place de marché.

### <a name="partner-center-csp-channel"></a>Canal CSP de l’Espace partenaires

Si vous vous êtes abonné au canal CSP dans l’Espace partenaires, les éditeurs doivent entrer une URL qui héberge des documents marketing pertinents et des informations de contact de canal relatives au canal CSP dans le module d’annonce d’offre :

![Informations collatérales CSP dans l’Espace partenaires](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canal CSP dans le portail Cloud Partner

Si vous vous êtes abonné au canal CSP dans le portail Cloud Partner, les éditeurs doivent entrer une URL hébergeant des documents marketing pertinents et des informations de contact de canal relatives au canal CSP :

![Informations collatérales CSP dans le portail Cloud Partner](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Guide de l’éditeur Place de marché Azure et AppSource](marketplace-publishers-guide.md).

Pour en savoir plus sur les services GTM de la place de marché, consultez [Services de commercialisation](https://partner.microsoft.com/reach-customers/gtm).

Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer et configurer votre offre.
