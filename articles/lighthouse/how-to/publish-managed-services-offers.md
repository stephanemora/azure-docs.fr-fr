---
title: Publier une offre de services gérés sur la place de marché Azure
description: Découvrez comment publier une offre de service géré qui intègre des clients à la gestion des ressources déléguées Azure.
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 247f711188fa10de19cece27f164fdfa71612d1b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991907"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publier une offre de services gérés sur la place de marché Azure

> [!IMPORTANT]
> À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres de services gérés vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Créer une nouvelles offres de services gérés](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) pour gérer vos offres migrées.

Dans cet article, vous allez découvrir comment publier une offre de service managé publique ou privée sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) à l’aide du [portail Cloud Partner](https://cloudpartner.azure.com/). Les clients qui achètent l’offre peuvent alors intégrer des abonnements et des groupes de ressources pour la [gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Exigences de publication

Pour créer et publier des offres, vous devez disposer d’un [compte valide dans l’Espace partenaires](../../marketplace/partner-center-portal/create-account.md). Si vous n’avez pas encore de compte, le [processus d’inscription](https://aka.ms/joinmarketplace) vous guide lors des étapes de création de compte dans l’Espace partenaires et d’inscription au programme de la Place de marché commerciale.

Conformément aux [exigences de certification des offres de services managés](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements), vous devez disposer d’un [niveau de compétence de plateforme cloud Silver ou Gold](https://docs.microsoft.com/partner-center/learn-about-competencies) ou être [Fournisseur de services managés Azure Expert](https://partner.microsoft.com/membership/azure-expert-msp) pour publier une offre de services managés.

Votre ID Microsoft Partner Network (MPN) est [automatiquement associé](../../billing/billing-partner-admin-link-started.md) aux offres que vous publiez pour suivre votre impact sur les engagements client.

> [!NOTE]
> Si vous ne souhaitez pas publier d’offre sur la Place de marché Azure, vous pouvez intégrer des clients manuellement à l’aide de modèles Azure Resource Manager. Pour plus d’informations, voir [Intégrer un client dans la gestion des ressources déléguées Azure](onboard-customer.md).

La publication d’une offre de services gérés est similaire à la publication d’un autre type d’offre sur la Place de marché Azure. Pour en savoir plus sur le processus général de publication, consultez [Guide de l’éditeur Place de marché Microsoft Azure et AppSource](../../marketplace/marketplace-publishers-guide.md). Vous devez également examiner les [stratégies de certification de la Place de marché commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies), en particulier la section [Managed Services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Une fois qu’un client a ajouté votre offre, il est en mesure de déléguer un ou plusieurs abonnements ou groupes de ressources, qui seront ensuite [intégrés pour la gestion des ressources déléguées Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Chaque plan d’une offre de services managés comprend une section **Détails du manifeste** dans laquelle vous définissez les entités Azure Active Directory (Azure AD) de votre locataire qui ont accès aux groupes de ressources ou aux abonnements délégués pour les clients qui achètent ce plan. Il est important de savoir que tout groupe (ou utilisateur ou principal de service) que vous incluez aura les mêmes autorisations pour chaque client achetant le plan. Pour affecter différents groupes à chaque client, vous devez publier un [plan privé](../../marketplace/private-offers.md) distinct exclusif pour chaque client.

## <a name="create-your-offer"></a>Créer votre offre

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2. Dans le menu de navigation de gauche, sélectionnez **Nouvelle offre**, puis **Services managés**.
3. Une fenêtre **Éditeur** s’affiche pour votre offre, comportant quatre sections à remplir : **Paramètres de l’offre**, **Plans**, **Place de marché** et **Support**. Lisez la suite pour obtenir des instructions sur la façon de compléter ces sections.

### <a name="enter-offer-settings"></a>Entrer les paramètres de l’offre

Dans la section **Paramètres de l’offre**, entrez les informations suivantes :

|Champ  |Description  |
|---------|---------|
|**ID de l’offre**     | Identificateur unique de votre offre au sein de votre profil d’éditeur. Cet ID ne peut contenir que des caractères alphanumériques minuscules, des tirets et des traits de soulignement, dont le nombre ne peut pas dépasser 50 signes. N’oubliez pas que l’ID de l’offre peut être visible pour les clients dans des emplacements tels que des URL et des états de facturation de produit. Une fois l’offre publiée, vous ne pouvez plus modifier cette valeur.        |
|**ID de l’éditeur**     | ID de l’éditeur qui sera associé à l’offre. Si vous avez plus d’un ID d’éditeur, vous pouvez sélectionner celui que vous souhaitez utiliser pour cette offre.       |
|**Nom**     | Nom (jusqu’à 50 caractères) que les clients verront pour votre offre sur la Place de marché et le portail Azure. Utilisez un nom de marque reconnaissable que les clients comprendront. Si vous promouvez cette offre via votre propre site web, veillez à utiliser exactement le même nom ici.        |

Quand vous avez terminé, sélectionnez **Enregistrer**. Vous êtes désormais prêt à passer à la section **Plans**.

### <a name="create-plans"></a>Créer des plans

Chaque offre doit comprendre un ou plusieurs plans (parfois appelés références SKU). Vous pouvez ajouter plusieurs plans correspondant à différents ensembles de fonctionnalités proposés à différents prix, ou personnaliser un plan spécifique pour un public limité de clients spécifiques. Les clients peuvent afficher les plans qui sont à leur disposition sous l’offre parente.

Dans la section Plans, sélectionnez **Nouveau plan**. Entrer ensuite un **ID de plan**. Cet ID ne peut contenir que des caractères alphanumériques minuscules, des tirets et des traits de soulignement, dont le nombre ne peut pas dépasser 50 signes. L’ID de plan peut être visible pour les clients dans des emplacements tels que les URL et états de facturation de produit. Une fois l’offre publiée, vous ne pouvez plus modifier cette valeur.

#### <a name="plan-details"></a>Détails du plan

Complétez les sections suivantes dans **Détails du plan** :

|Champ  |Description  |
|---------|---------|
|**Titre**     | Nom convivial du plan à afficher. Longueur maximale de 50 caractères.        |
|**Résumé**     | Description succincte du plan à afficher sous le titre. Longueur maximale de 100 caractères.        |
|**Description**     | Texte de description fournissant davantage de détails sur le plan.         |
|**Modèle de facturation**     | Deux modèles de facturation sont présentés ici, mais vous devez choisir **BYOL (apportez votre propre licence)** pour les offres de services managés. Cela signifie que vous facturez directement à vos clients les coûts liés à l’offre, et que Microsoft ne vous facture aucuns frais.   |
|**Ce plan est-il privé ?**     | Indique si la référence SKU est privée ou publique. La valeur par défaut est **Non** (publique). Si vous conservez cette sélection, votre plan ne sera pas limité à des clients spécifiques (ou à un certain nombre de clients). Une fois que vous avez publié un plan public, vous ne pouvez plus le modifier en plan privé par la suite. Pour que ce plan soit disponible uniquement pour des clients spécifiques, sélectionnez **Oui**. Dans ce cas, vous devez identifier les clients en fournissant leurs ID d’abonnement. Vous pouvez entrer ceux-ci un par un (pour jusqu’à 10 abonnements) ou en chargeant un fichier .csv (pour jusqu’à 20 000 abonnements). Veillez à inclure vos propres abonnements ici afin de pouvoir tester et valider l’offre. Pour plus d’informations, voir [Références SKU et plans privés](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Une fois que vous avez publié un plan public, vous ne pouvez plus le changer en plan privé. Pour contrôler les clients qui peuvent accepter votre offre et déléguer des ressources, utilisez un plan privé. Avec un plan public, vous ne pouvez pas limiter la disponibilité à des clients spécifiques ou à un certain nombre de clients (en revanche, vous pouvez arrêter complètement la vente du plan si vous le souhaitez). Vous pouvez [supprimer l’accès à une délégation](onboard-customer.md#remove-access-to-a-delegation) après qu’un client a accepté une offre uniquement si vous avez inclus une **Autorisation** avec la **Définition de rôle** définie sur [Inscription des services managés, attribution Supprimer le rôle](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) lors de la publication de l’offre. Vous pouvez également contacter le client et lui demander de [supprimer votre accès](view-manage-service-providers.md#add-or-remove-service-provider-offers).

#### <a name="manifest-details"></a>Détails du manifeste

Complétez la section **Détails du manifeste** pour votre plan. Cela a pour effet de créer un manifeste contenant des informations d’autorisation pour la gestion des ressources du client. Ces informations sont nécessaires pour activer la gestion des ressources déléguées Azure.

> [!NOTE]
> Comme indiqué ci-dessus, les utilisateurs et rôles de vos entrées **Autorisation** s’appliquent à tous les clients qui achètent le plan. Si vous souhaitez limiter l’accès à un client spécifique, vous devez publier un plan privé pour son usage exclusif.

Tout d’abord, fournissez une **Version** pour le manifeste. Utilisez le format *n.n.n* (par exemple, 1.2.5).

Ensuite, entrez votre **ID de locataire**. Il s’agit d’un GUID associé à l’ID de locataire Azure Active Directory de votre organisation (par exemple, le locataire dans lequel vous allez travailler pour gérer les ressources de vos clients). Si vous ne l’avez pas sous la main, vous pouvez le trouver en pointant sur le nom de votre compte dans l’angle supérieur droit du portail Azure ou en sélectionnant **Changer de répertoire**.

Enfin, ajoutez une ou plusieurs entrées **Autorisation** à votre plan. Les autorisations définissent les entités qui peuvent accéder aux ressources et aux abonnements pour les clients qui achètent le plan, et affectent des rôles qui accordent des niveaux spécifiques d’accès.

> [!TIP]
> Dans la plupart des cas, vous affectez des autorisations à un groupe d’utilisateurs ou à un principal de service Azure AD, plutôt qu’à une série de comptes d’utilisateur individuels. Cela vous permet d’ajouter ou de supprimer l’accès d’utilisateurs individuels sans devoir mettre à jour et republier le plan lorsque vos conditions d’accès changent. Pour obtenir des recommandations supplémentaires, consultez [Locataires, rôles et utilisateurs dans les scénarios Azure Lighthouse](../concepts/tenants-users-roles.md).

Pour chaque **autorisation**, vous devez fournir les informations suivantes. Vous pouvez ensuite sélectionner **Nouvelle autorisation** autant de fois que nécessaire pour ajouter des utilisateurs et définitions de rôles.

- **ID d’objet Azure AD** : identificateur Azure AD d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auxquels certaines autorisations seront accordées (comme indiqué dans la définition de rôle) sur les ressources de votre client.
- **Nom d’affichage d’objet Azure AD** : nom convivial destiné à aider le client à comprendre l’objectif de cette autorisation. Le client verra ce nom lors de la délégation de ressources.
- **Définition de rôle** : sélectionnez l’un des rôles intégrés Azure AD disponibles dans la liste. Ce rôle détermine les autorisations sur les ressources de vos clients dont disposera l’utilisateur spécifié dans le champ **ID d’objet Azure AD**. Pour obtenir une description de ces rôles, consultez les [Rôles intégrés](../../role-based-access-control/built-in-roles.md) et [Prise en charge des rôles pour la gestion des ressources déléguées Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Comme les nouveaux rôles intégrés applicables sont ajoutés à Azure, ils sont disponibles ici, même si un certain temps puis s’écouler avant qu’ils n’apparaissent.
- **Rôles attribuables** : cela est nécessaire uniquement si vous avez sélectionné Administrateur de l’accès utilisateur dans la **Définition de rôle** pour cette autorisation. Si tel est le cas, vous devez ajouter un ou plusieurs rôles attribuables ici. L’utilisateur indiqué dans le champ **ID d’objet Azure AD** sera en mesure d’attribuer ces **Rôles attribuables** à des [identités managées](../../active-directory/managed-identities-azure-resources/overview.md), ce qui est nécessaire pour [déployer des stratégies qui peuvent être corrigées](deploy-policy-remediation.md). Notez qu’aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’appliquera à cet utilisateur. Si vous ne sélectionnez pas un ou plusieurs rôles ici, votre envoi n’obtiendra pas la certification. (Si vous n’avez pas sélectionné Administrateur de l’accès utilisateur pour la définition de rôle de cet utilisateur, ce champ est sans effet.)

> [!TIP]
> Pour être sûr de pouvoir [supprimer l’accès à une délégation](onboard-customer.md#remove-access-to-a-delegation) en cas de nécessité, incluez une **Autorisation** avec la **Définition de rôle** définie sur [Inscription des services managés, attribution Supprimer le rôle](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Si ce rôle n’est pas attribué, les ressources déléguées ne peuvent être supprimées que par un utilisateur dans le locataire du client.

Une fois que vous avez renseigné ces informations, vous pouvez sélectionner **Nouveau plan** autant de fois que nécessaire pour créer des plans supplémentaires. Lorsque vous avez terminé, sélectionnez **Enregistrer**, puis passez à la section **Place de marché**.

### <a name="provide-marketplace-text-and-images"></a>Fournir du texte et des images à la Place de marché

La section **Place de marché** est l’endroit où vous fournissez le texte et les images que les clients verront sur la Place de marché et le portail Azure.

Renseignez les champs suivants dans la section **Vue d’ensemble** :

|Champ  |Description  |
|---------|---------|
|**Titre**     |  Titre de l’offre. Il s’agit souvent du nom formel long. Il est affiché de façon visible dans la Place de marché. Longueur maximale de 50 caractères. Dans la plupart des cas, il doit s’agir du **Nom** que vous avez entré dans la section **Paramètres de l’offre**.       |
|**Résumé**     | Brève description de la finalité ou de la fonction de votre offre. Généralement affiché sous le titre. Longueur maximale de 100 caractères.        |
|**Long Summary** (Résumé long)     | Récapitulatif plus long de la finalité ou de la fonction de votre offre. Longueur maximale de 256 caractères.        |
|**Description**     | Plus d’informations sur votre offre. Ce champ peut compter jusqu’à 3 000 caractères et prend en charge la mise en forme HTML simple. Vous devez inclure les mots « service managé » ou « services managés » quelque part dans votre description.       |
|**Marketing Identifier** (Identificateur marketing)     | Identificateur unique compatible avec les URL. Cet identificateur peut uniquement contenir des caractères alphanumériques en minuscules et les tirets. Il sera utilisé dans les URL de la Place de marché pour cette offre. Par exemple, si votre ID d’éditeur est *contoso* et votre identificateur marketing *sampleApp*, l’URL de votre offre sur la Place de marché Azure sera *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp* .        |
|**ID d’abonnement pour préversion**     | Ajoutez entre 1 et 100 identificateurs d’abonnement. Les clients associés à ces abonnements pourront afficher l’offre sur la Place de marché Azure avant sa mise en ligne. Nous vous suggérons d’inclure ici vos propres abonnements afin de pouvoir prévisualiser l’apparence de votre offre sur la Place de marché Azure avant de la mettre à la disposition des clients  (le support technique Microsoft et les équipes d’ingénierie pourront également voir votre offre pendant cette période de préversion).   |
|**Liens utiles**     | URL associées à votre offre, telles celles de la documentation, des notes de publication, du FAQ, etc.        |
|**Catégories suggérées (cinq au maximum)**     | Une ou plusieurs catégories (jusqu’à cinq) s’appliquant à votre offre. Ces catégories aident les clients à découvrir votre offre sur la Place de marché et le portail Azure.        |

Dans la **Marketing Artifacts** (Artefacts marketing), vous pouvez charger des logos et d’autres ressources à afficher avec votre offre. Vous pouvez éventuellement charger des captures d’écran ou des liens vers des vidéos susceptibles d’aider les clients à comprendre votre offre.

Quatre tailles de logo sont requises : **Petite (40 x 40)** , **Moyenne (90 x 90)** , **Grande (115 x 115)** et **Large (255 x 115)** . Suivez ces instructions pour vos logos:

- Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
- Les couleurs de thème du portail sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour votre logo. Utilisez une couleur qui donne à votre logo plus de visibilité dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples.
- Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.
- L'apparence de votre logo doit être « plate » et éviter les dégradés. N'utilisez pas d’arrière-plan dégradé sur le logo.
- Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise.
- Assurez-vous que le logo n’est pas étiré.

Le logo **Bannière (815 x 290**) est facultatif mais recommandé. Si vous incluez un logo bannière, suivez les instructions ci-dessous :

- N’incluez pas de texte dans le logo bannière et veillez à conserver 415 pixels d’espace vide sur le côté droit du logo. Cela est nécessaire pour ménager de la place pour les éléments de texte qui seront incorporés par programme, à savoir votre nom d’affichage d’éditeur, le titre du plan, le résumé long de l’offre.
- L’arrière-plan de votre logo bannière peut ne pas être noir, blanc ou transparent. Assurez-vous que la couleur d’arrière-plan n’est pas trop claire, car le texte incorporé s’affichera en blanc.
- Une fois que vous avez publié votre offre avec une icône de bannière, vous ne pouvez plus la supprimer (même si vous pouvez la mettre à jour avec une autre version si vous le souhaitez).

Dans la section **Lead Management** (Gestion des prospects), vous pouvez sélectionner le système CRM dans lequel vos prospects seront stockés. Notez que, selon [les stratégies de certification des services managés](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), une **destination de prospect** est requise.

Enfin, entrez votre **Privacy Policy URL** (URL de la politique de confidentialité) et vos **Terms of use** (Conditions d’utilisation) dans la section **Legal** (Légal). Vous pouvez également spécifier ici s’il faut ou non utiliser le [contrat standard](../../marketplace/standard-contract.md) pour cette offre.

Veillez à enregistrer vos modifications avant de passer à la section **Support**.

### <a name="add-support-info"></a>Ajouter des informations de support

Dans la section **Support**, entrez le nom, l’adresse e-mail et le numéro de téléphone d’un contact d’ingénierie et d’un contact du service clientèle. Vous devez également fournir des URL de support. Microsoft peut utiliser ces informations en cas de nécessité de vous contacter à propos d’éventuels problèmes d’exploitation et de support.

Une fois que vous avez ajouté ces informations, sélectionnez **Enregistrer**.

## <a name="publish-your-offer"></a>Publier votre offre

Lorsque vous avez terminé chacune des sections, l’étape suivante consiste à publier l’offre sur la Place de marché Azure. Sélectionnez le bouton **Publier** pour démarrer le processus de mise en ligne de votre offre. Pour plus d’informations sur ce processus, voir [Publier des offres sur la Place de marché Microsoft Azure et AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

Vous pouvez [publier une version mise à jour de votre offre](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) à tout moment. Par exemple, vous pouvez souhaiter ajouter une nouvelle définition de rôle à une offre publiée précédemment. Dans ce cas, les clients qui ont déjà ajouté l’offre voient s’afficher une icône sur la page [**Fournisseurs de services**](view-manage-service-providers.md) du Portail Azure, ce qui leur permet de savoir qu’une mise à jour est disponible. Chaque client pourra [passer en revue les modifications](view-manage-service-providers.md#update-service-provider-offers) et décider s’il souhaite effectuer la mise à jour vers la nouvelle version. 

## <a name="the-customer-onboarding-process"></a>Processus d’intégration des clients

Une fois qu’un client a ajouté votre offre, il est en mesure de [déléguer un ou plusieurs abonnements ou groupes de ressources spécifiques](view-manage-service-providers.md#delegate-resources), qui seront ensuite intégrés pour la gestion des ressources déléguées Azure. Si un client a accepté une offre mais n’a pas encore délégué de ressources, il voit une note s’afficher en haut de la section **Offres de fournisseur** de la page [**Fournisseurs de services**](view-manage-service-providers.md) du portail Azure.

> [!IMPORTANT]
> La délégation doit être effectuée par un compte non invité dans le locataire client qui a le [rôle intégré Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pour l’abonnement en cours d’intégration (ou qui contient les groupes de ressources en cours d’intégration). Pour voir tous les utilisateurs qui peuvent déléguer l’abonnement, un utilisateur du locataire du client peut sélectionner l’abonnement dans le portail Azure, ouvrir **Contrôle d’accès (IAM)** et [afficher tous les utilisateurs ayant le rôle Propriétaire](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Une fois que le client aura délégué un abonnement (ou un ou plusieurs groupes de ressources d’un abonnement), le fournisseur de ressources **Microsoft.ManagedServices** sera inscrit pour cet abonnement, et les utilisateurs de votre locataire pourront accéder aux ressources déléguées conformément aux autorisations de votre offre.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Place de marché commerciale](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
