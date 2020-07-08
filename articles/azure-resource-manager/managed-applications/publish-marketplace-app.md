---
title: Applications managées dans la Place de marché
description: Décrit les applications gérées Azure disponibles via la Place de marché.
author: tfitzmac
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: dcd036f09982cba8271ed6057a167eb7440303a9
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054489"
---
# <a name="tutorial-publish-azure-managed-applications-in-the-marketplace"></a>Tutoriel : Publier des applications managées Azure dans la Place de marché

Les fournisseurs peuvent utiliser les applications managées Azure pour offrir leurs solutions à tous les clients de la Place de marché Azure. Il peut s’agir de fournisseurs de services managés, d’éditeurs de logiciels indépendants et d’intégrateurs de système. Les applications managées réduisent les opérations de maintenance et de mise à jour des clients. Les fournisseurs vendent des infrastructures et des logiciels sur la Place de marché. Ils peuvent joindre des services et le support opérationnel pour les applications gérées. Pour plus d’informations, consultez [Vue d’ensemble des applications gérées](overview.md).

Cet article explique comment publier une application sur la Place de marché et la mettre à la disposition des clients.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Conditions préalables à la publication d’une application gérée

Pour suivre cet article, vous devez déjà disposer du fichier .zip pour votre définition d’application managée. Pour plus d’informations, consultez [Créer l’application de catalogue de services](publish-service-catalog-app.md).

Il existe plusieurs prérequis métier. Il s'agit de :

* Votre entreprise ou sa filiale doit être située dans un pays ou une région où les ventes sont prises en charge par la Place de marché.
* Votre produit doit être concédé sous licence de manière à être compatible avec les modèles de facturation pris en charge par la Place de marché.
* Consentez tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
* Gérez les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
* Fournissez du contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de Marché et sur le portail Microsoft Azure.
* Acceptez les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de Marché Microsoft Azure.
* Engagez-vous à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

Vous devez également disposer d’un compte Place de marché. Pour créer un compte, consultez [Guide pratique pour créer un compte Place de marché commerciale dans l’Espace partenaires](../../marketplace/partner-center-portal/create-account.md).

## <a name="create-a-new-azure-application-offer"></a>Création d’une offre d’application Azure

Une fois que vous avez créé votre compte du portail de partenaire, vous êtes prêt à créer votre offre d’application managée.

### <a name="set-up-an-offer"></a>Configuration d’une offre

Une offre d’application gérée correspond à une classe d’offre de produit d’un éditeur. Si vous avez un nouveau type d’application à mettre à disposition sur la Place de marché, vous pouvez en faire une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché.

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

1. Dans le volet de navigation de gauche, sélectionnez **+ Nouvelle offre** > **Applications Azure**.

1. Les formulaires nécessaires sont affichés dans la vue **Éditeur**. Chaque formulaire est décrit plus loin dans cet article.

## <a name="offer-settings-form"></a>Formulaire des paramètres de l’offre

Le formulaire **Paramètres de l’offre** contient les champs suivants :

* **ID de l’offre** : il s’agit d’un identificateur unique qui identifie l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret. Il est limité à un maximum de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.
* **ID de l’éditeur** : utilisez cette liste déroulante pour choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.
* **Name** : dans la Place de marché et dans le portail, ce nom d’affichage s’affiche pour votre offre. Il ne peut pas comprendre plus de 50 caractères. Incluez un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

Une fois terminé, sélectionnez **Enregistrer** pour enregistrer votre progression.

## <a name="skus-form"></a>Formulaire de références (SKU)

L’étape suivante consiste à ajouter des références (SKU) pour votre offre.

Une référence (SKU) est la plus petite unité achetable d’une offre. Vous pouvez utiliser une référence (SKU) dans la même classe de produit (offre) à des fins de différentiation :

* Les différentes fonctionnalités prises en charge
* Si l’offre est gérée ou non
* Les modèles de facturation pris en charge

Une référence (SKU) s’affiche sous l’offre parente dans la Place de marché. Elle s’affiche comme une entité distincte pouvant être achetée dans le portail Azure.

1. Sélectionnez **Références** > **Nouvelle référence**.

1. Saisissez un **ID de référence**. Un SKU ID (ID de référence) est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

1. Remplissez la section **Détails de la référence** sur le formulaire suivant :

   Renseignez les champs suivants :

   * **Titre** : Entrez un titre pour cette référence SKU. Ce titre s’affiche dans la galerie pour cet élément.
   * **Résumé**: Entrez un résumé décrivant brièvement cette référence SKU. Ce texte s’affiche en dessous du titre.
   * **Description** : Entrez une description détaillée de la référence SKU.
   * **Type de référence SKU** : Les valeurs autorisées sont *Application managée* et *Modèles de solution*. Dans le cas présent, sélectionnez *Managed Application* (Application gérée).
   * **Disponibilité par pays/région** : Sélectionnez les pays/régions où l’application managée est disponible.
   * **Prix** : Spécifiez un prix pour la gestion de l’application. Sélectionnez les pays/régions disponibles avant de définir le prix.

1. Ajoutez un nouveau package. Remplissez la section **Détails du package** sur le formulaire suivant :

   Renseignez les champs suivants :

   * **Version** : Entrez la version du package chargé. Il doit respecter le format `{number}.{number}.{number}{number}`.
   * **Fichier de package (.zip)**  : Ce package contient deux fichiers nécessaires qui sont compressés dans un package .zip. L’un des fichiers est un modèle Resource Manager, qui définit les ressources à déployer pour l’application gérée. L’autre fichier définit [l’interface utilisateur](create-uidefinition-overview.md) pour les consommateurs qui déploient l’application gérée via le portail. Dans l’interface utilisateur, vous spécifiez les éléments qui permettent aux consommateurs de fournir des valeurs de paramètre.
   * **ID du locataire** : ID du locataire du compte pour lequel obtenir un accès.
   * **Activer l’accès JIT** : Sélectionnez **Oui** afin d’activer le [contrôle d’accès juste-à-temps](request-just-in-time-access.md) pour le compte. Lorsqu’il est activé, vous demandez à accéder au compte de l’utilisateur pendant une période spécifiée. Pour exiger que les utilisateurs de votre application managée accordent à votre compte un accès permanent, sélectionnez **Non**.
   * **Personnaliser les actions des clients autorisées ?**  : sélectionnez **Oui** pour spécifier les actions que les consommateurs peuvent effectuer sur les ressources managées.
   * **Actions des clients autorisées** : si vous sélectionnez **oui** pour le paramètre précédent, vous pouvez spécifier les actions que les consommateurs sont autorisés à effectuer à l’aide d’[affectations de refus relatives aux ressources Azure](../../role-based-access-control/deny-assignments.md).

     Pour obtenir la liste des actions disponibles, consultez [Opérations du fournisseur de ressources Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Par exemple, pour permettre aux consommateurs de redémarrer des machines virtuelles, ajoutez `Microsoft.Compute/virtualMachines/restart/action` aux actions autorisées. L’action `*/read` est automatiquement autorisée ; vous n’avez donc pas besoin d’inclure ce paramètre.
   * **PrincipalId** : Cette propriété représente l’identificateur Azure Active Directory (Azure AD) d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auxquels l’accès aux ressources de l’abonnement du client a été accordé. La définition de rôle décrit les autorisations.
   * **Définition de rôle** : Cette propriété dresse une liste de tous les rôles RBAC intégrés qui sont fournis par Azure AD. Vous pouvez sélectionner le rôle le mieux adapté pour gérer les ressources pour le compte du client.
   * **Paramètres de stratégie** : Appliquez une stratégie [Azure Policy](../../governance/policy/overview.md) à votre application managée afin de spécifier des exigences de conformité pour les solutions déployées. Parmi les options disponibles, sélectionnez les stratégies à appliquer. Pour **Paramètres de stratégie**, indiquez une chaîne JSON avec les valeurs de paramètre. Pour les définitions de stratégie et le format des valeurs de paramètre, consultez [Exemples Azure Policy](../../governance/policy/samples/index.md).

Vous pouvez ajouter plusieurs autorisations. Nous vous recommandons de créer un groupe d’utilisateurs AD et de spécifier son ID dans **PrincipalId**. De cette manière, vous pouvez ajouter plus d’utilisateurs au groupe d’utilisateurs sans avoir à mettre à jour la référence (SKU).

Pour plus d’informations sur le sujet, consultez [Bien démarrer avec RBAC dans le portail Azure](../../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formulaire Marketplace

Le formulaire Marketplace (Place de marché) permet de définir les champs qui s’affichent dans la [Place de marché Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Entrez une liste d’ID d’abonnement Azure qui peuvent accéder à l’offre après sa publication. Vous pouvez utiliser ces abonnements autorisés pour tester l’offre préliminaire avant sa publication. Vous pouvez compiler une liste verte de 100 abonnements maximum dans le portail partenaire.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste jusqu’à cinq catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Place de marché Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur la place de marché](./media/publish-marketplace-app/publishvm10.png)

Les champs suivants s’affichent dans l’onglet **Vue d’ensemble** de votre application gérée :

![Présentation de la Place de marché](./media/publish-marketplace-app/publishvm11.png)

Les champs suivants s’affichent dans l’onglet **Abonnements + Tarification** de votre application gérée :

![Plans sur la place de marché](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Portail Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur le portail](./media/publish-marketplace-app/publishvm12.png)

Les champs suivants s’affichent dans la vue d’ensemble de votre application gérée :

![Présentation du portail](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez ces instructions pour tout logo que vous téléchargez dans le portail partenaire cloud :

*   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
*   Les couleurs de thème du portail sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour votre logo. Utilisez une couleur qui donne à votre logo plus de visibilité dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples. *Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.*
*   N'utilisez pas d’arrière-plan dégradé sur le logo.
*   Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise. L'apparence de votre logo doit être « plate » et éviter les dégradés.
*   Assurez-vous que le logo n’est pas étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. Une fois l’icône de bannière téléchargée, elle ne peut pas être supprimée. À ce moment, le partenaire doit suivre les instructions relatives à la Place de marché pour les icônes.

Suivez ces recommandations concernant l’icône de bannière :

*   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en blanc. Ainsi, n’utilisez pas de couleur claire pour l’arrière-plan de l’icône de bannière. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
*   Une fois l’offre répertoriée, les éléments sont incorporés par programmation à l’intérieur de la bannière. Les éléments incorporés incluent le nom d’affichage de l’éditeur, le titre du plan, le résumé de l’offre et le bouton **Créer**. Par conséquent, ne saisissez pas de texte lorsque vous concevez le l’icône de bannière. Laissez un espace vide à droite, car le texte est inclus par programme dans cet espace. L’espace vide pour le texte doit être de 415 x 100 pixels sur la droite. Il est décalé de 370 pixels à partir de la gauche.

    ![Exemple d’icône de bannière](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formulaire de prise en charge

Remplissez le formulaire **Support** avec les contacts de support de votre entreprise. Ces informations peuvent être les contacts de support d’ingénierie et client.

## <a name="publish-an-offer"></a>Publication d’une offre

Après avoir rempli toutes les sections, sélectionnez **Publish** (Publier) pour mettre votre offre à disposition des clients.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur ce qui se passe une fois que vous avez cliqué sur **Publier**, consultez [Publier une offre d’application Azure](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md).
* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](overview.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](publish-service-catalog-app.md).
