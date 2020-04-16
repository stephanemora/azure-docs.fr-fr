---
title: Créer une offre d’application Power BI - Place de marché Microsoft Azure
description: Découvrez comment créer une offre d’application Power BI et la publier sur Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 776311d6e6395cbe462f958bd8685fa0259e1fc2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674856"
---
# <a name="create-a-power-bi-app"></a>Créer une offre d’application Power BI

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres d’application Power BI du Portail Cloud Partner vers l’Espace partenaires. Tant que vos offres ne sont pas migrées, suivez les instructions dans [Créer une offre d’application Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) pour que le Portail Cloud Partner gère vos offres.

Cet article explique comment créer une offre d’application Power BI et la publier sur Microsoft [AppSource](https://appsource.microsoft.com/).

Avant de pouvoir créer une offre d’application Power BI, vous devez disposer d’un compte de Place de marché commerciale dans l’Espace partenaires. Si vous n’en avez pas encore créé un, consultez [Créer un compte de Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="menu de vue d’ensemble de la Place de marché commerciale" border="false":::

3. Sélectionnez **+ Nouvelle offre** > **Application Power BI**. La boîte de dialogue **Nouvelle offre** s’affiche.

> [!IMPORTANT]
> Si l’option **Application Power BI** n’apparaît pas ou n’est pas activée, votre compte n’est pas autorisé à créer ce type d’offre. Vérifiez que vous remplissez toutes les [exigences](create-power-bi-app-overview.md) de ce type d’offre, notamment l’inscription d’un compte de développeur.

### <a name="offer-id-and-alias"></a>ID et alias de l’offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom utilisé pour faire référence à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
- Cet alias ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Après avoir entré ces deux valeurs, sélectionnez **Créer** pour accéder à la page Vue d’ensemble de l’offre.

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

La page **Vue d’ensemble de l’offre** affiche une représentation visuelle des étapes nécessaires à la publication de cette offre (à la fois terminées et à venir) et indique la durée d’exécution de chaque étape.

Elle contient des liens permettant d’effectuer des opérations sur cette offre en fonction de la sélection que vous effectuez. Par exemple :

- Si l’offre est un brouillon : [Supprimer le brouillon de l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Si l’offre est publiée : [Arrêter de vendre l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Si l’offre est disponible en préversion : [Mise en service](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Si vous n’avez pas complété Déconnexion de l’éditeur : [Annuler la publication](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuration de l’offre

Effectuez les étapes suivantes pour configurer votre offre.

### <a name="connect-lead-management"></a>Connecter la gestion des prospects

Lors de la publication de votre offre sur la Place de marché avec l’Espace partenaires, vous devez la connecter à votre système de gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit.

1. Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix. L’Espace partenaires prend en charge les systèmes CRM suivants :

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Si votre système de CRM n’est pas listé ci-dessus, utilisez une [table Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou un [point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) pour stocker les données des prospects. Ensuite, exportez les données vers votre système CRM.

2. Connectez votre offre à la destination de prospect tout en la publiant dans l’Espace partenaires.
3. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l’Espace partenaires, nous validons la connexion et nous vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement en version préliminaire.
4. Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

Voici des ressources de gestion des prospects supplémentaires :

- [Vue d’ensemble de la gestion des prospects](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Questions fréquentes sur la gestion des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erreurs courantes de configuration des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- PDF [Vue d’ensemble de la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Propriétés.

## <a name="properties"></a>Propriétés

C’est dans cette page que vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les Places de marché, la version de votre application et les contrats légaux associés.

### <a name="category"></a>Category

Sélectionnez au minimum une et au maximum trois catégories. Ces catégories servent à placer votre offre dans les zones de recherche appropriées sur la Place de marché et apparaissent dans la page des détails de votre offre. Dans la description de l’offre, expliquez comment votre offre prend en charge ces catégories.

### <a name="industry"></a>Secteur d’activité

Éventuellement, sélectionnez jusqu’à deux secteurs et deux marchés verticaux sous chaque secteur d’activité. Tandis que les catégories sont utilisées pour afficher votre offre, le secteur et les marchés verticaux sont utilisés dans les filtres de recherche et sont appliqués dans la vitrine. Si votre offre cible un secteur d’activité et/ou un marché vertical spécifiques, utilisez la description de l’offre pour expliquer la manière dont votre offre prend en charge les secteurs ou les marchés verticaux sélectionnés. Si votre offre n’est pas spécifique d’un secteur d’activité, laissez cette section vide.

> [!NOTE]
> À mesure que nous travaillons à l’introduction de nouveaux secteurs et marchés verticaux pour améliorer l’expérience de découverte de l’offre, certains secteurs ou marchés verticaux peuvent ne pas encore être visibles sur la vitrine. Les secteurs et marchés verticaux marqués d’un (*) seront disponibles à une date ultérieure. Toutes les offres publiées sont détectables par le biais d’une recherche par mot clé.
<p>&nbsp;

| **Secteur d’activité** | **Sous-secteur** |
| --- | --- |
| *Industrie automobile | *Industrie automobile |
| Agriculture | *Autre - Non segmenté |
| Distribution | *Vente en gros<br>Expédition de paquets et colis |
| Formation | *Enseignement supérieur<br>* Enseignement primaire et secondaire/K-12<br>*Bibliothèques et musées |
| Services financiers | *Banques et marchés financiers<br>* Assurance |
| Gouvernement américain | *Défense et renseignement (précédemment Sécurité nationale et publique)<br>* Sécurité publique et justice<br>*Gouvernement civil |
| Soins de santé (précédemment Santé) | *Organisme de sécurité sociale<br>* Professionnel de santé<br>*Produits pharmaceutiques |
| Fabrication et ressources (précédemment Fabrication) | *Produits chimiques et agrochimiques<br>* Fabrication discrète<br>*Énergie |
| Distribution et biens de consommation (précédemment Vente au détail) | *Produits de consommation courante<br>* Détaillants |
| *Médias et communications (précédemment Multimédia et divertissement) | *Multimédia et divertissement<br>* Télécommunications |
| Services professionnels | *Juridique<br>* Services professionnels partenaires |
| *Construction et architecture (précédemment Ingénierie d’architecture) | *Autre - Non segmenté |
| **Hôtellerie et voyage | *Hôtels et loisirs<br>* Transport et voyages<br>*Restaurants et services alimentaires |
| *Autres secteurs publics | *Foresterie et pêche<br>* Organisations à but non lucratif |
| *Immobilier | *Autre - Non segmenté |

### <a name="legal"></a>Informations juridiques

#### <a name="terms-and-conditions"></a>Conditions générales

Pour fournir vos propres conditions générales, entrez jusqu’à 10 000 caractères dans la zone **Conditions générales**. Si vos conditions générales nécessitent une description plus longue, entrez un lien web unique vers l’emplacement où elles se trouvent. Ceci affichera un lien actif aux clients.

Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Annonce de l’offre.

## <a name="offer-listing"></a>Annonce de l’offre

Ici, vous allez définir les détails de l’offre qui s’affichent dans la Place de marché. Cela comprend le nom, la description, les images, etc. de l’offre.

### <a name="language"></a>Langage

Sélectionnez la langue dans laquelle votre offre sera listée. Actuellement, **Anglais (États-Unis)** est la seule option disponible.

Définissez les informations de la Place de marché (telles que le nom de l’offre, la description et les images) pour chaque langue/marché. Sélectionnez le nom de la langue/du marché pour fournir ces informations.

> [!NOTE]
> Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par l’expression &quot;This application is available only in [langue non anglaise].&quot; Il est également possible de fournir un lien utile pour offrir du contenu dans une langue autre que celle utilisée dans l’annonce de l’offre.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche comme titre de votre offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright).
- Ne peut pas compter plus de 50 caractères.
- Ne peut pas inclure d’emojis.

### <a name="search-results-summary"></a>Résumé des résultats de recherche

Fournissez une brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre, jusqu’à 3 000 caractères. S’affichera pour les clients dans la vue d’ensemble de la Place de marché.

Incluez un ou plusieurs des éléments suivants dans votre description :

- La valeur et les avantages principaux de votre offre
- Catégorie ou associations de secteurs, ou les deux
- Opportunités d’achats dans l’application
- Toutes les informations requises

Voici quelques conseils pour rédiger votre description :

- Décrivez clairement la valeur de votre offre dans les premières phrases de votre description. Incorporez les éléments suivants :
  - Description de l’offre.
  - Le type d’utilisateur qui tire parti de l’offre.
  - Les besoins ou problèmes auxquels l’offre répond.
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application offre.
- Essayez d’utiliser le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés.

Pour que la **Description** de votre offre soit plus attrayante, utilisez l’éditeur de texte enrichi pour mettre en forme votre description. L’éditeur de texte enrichi vous permet d’ajouter des chiffres, des puces, des caractères gras/en italique et des mises en retrait pour rendre votre description plus lisible.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="Illustre l’éditeur de texte enrichi." border="false":::

### <a name="search-keywords"></a>Mots clés de recherche

Entrez jusqu’à trois mots clés de recherche facultatifs pour aider les clients à trouver votre offre sur la Place de marché. Pour obtenir des résultats optimaux, utilisez également ces mots clés dans votre description.

### <a name="helpprivacy-web-addresses"></a>Adresses web d’aide/confidentialité

Fournissez des liens pour aider les clients à mieux comprendre votre offre.

#### <a name="help-link"></a>Lien d'aide

Entrez l’adresse web à partir de laquelle les clients peuvent en savoir plus sur votre offre.

#### <a name="privacy-policy-url"></a>URL de la politique de confidentialité

Entrez l’adresse web de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre offre respecte les lois et les réglementations en matière de confidentialité. Vous devez également veiller à publier une politique de confidentialité conforme sur votre site web.

### <a name="contact-information"></a>Informations de contact

Entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact du support** et d’un **contact d’ingénierie**. Ces informations ne sont pas présentées aux clients. Elles sont disponibles pour Microsoft et peuvent être fournies aux partenaires fournisseurs de solutions Cloud (CSP).

- Contact du support (obligatoire) : Pour toute question de support générale.
- Contact ingénierie (obligatoire) : Pour les questions techniques et les problèmes de certification.
- Contact du programme du fournisseur de solutions Cloud (facultatif) : Pour toute question du revendeur relative au programme CSP.

Dans la section **Contact du support**, indiquez l’adresse du **site web du support** où les partenaires peuvent trouver l’assistance pour votre offre.

### <a name="supporting-documents"></a>Documents de soutien

Fournissez au moins un et au plus trois documents marketing associés au format PDF. Par exemple des livres blancs, des brochures, des check-lists ou des présentations.

### <a name="marketplace-images"></a>Images de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format .png. Les images floues seront rejetées.

#### <a name="store-logos"></a>Stocker des logos

Fournissez des fichiers .png du logo de votre offre dans deux tailles : **Petit** (48 x 48 pixels) et **Grand** (216 x 216 pixels).

Les deux logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez au moins une et au plus cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chacune doit avoir une taille de 1280 x 720 pixels et être au format .png.

#### <a name="videos-optional"></a>Vidéos (facultatif)

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Entrez le nom de la vidéo, son adresse web et une image miniature .png de 1280 x 720 pixels de la vidéo.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

Pour en savoir plus sur la création d’annonces d’offres, consultez [Bonnes pratiques pour le référencement des offres](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Configuration technique

Promouvez votre application dans le service Power BI en production et fournissez le lien du programme d’installation Power BI qui permet aux clients d’installer votre application. Pour plus d’informations, consultez [Publish apps with dashboards and reports in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps) (Publier des applications avec tableaux de bord et rapports dans Power BI).

## <a name="supplemental-content"></a>Contenu supplémentaire

Fournissez des informations supplémentaires sur votre offre pour que nous puissions la valider. Ces informations ne sont pas montrées aux clients ni publiées sur la Place de marché.

### <a name="validation-assets"></a>Ressources de validation

Éventuellement, ajoutez des instructions (jusqu’à 3 000 caractères) pour aider l’équipe de validation de Microsoft à configurer votre application, s’y connecter et la tester. Insérez des paramètres de configuration standard, des comptes, des paramètres ou d’autres informations qui peuvent être utilisés pour tester l’option Connecter des données. Ces informations sont uniquement visibles par l’équipe de validation et sont dédiées à la validation.

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez l’envoyer pour révision et publication.

Dans le coin supérieur droit du portail, sélectionnez **Vérifier et publier**.

Dans la page de révision, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre. Vous ne pouvez pas publier tant que toutes les sections de l’offre ne sont pas marquées comme étant complètes.
  - **Non démarrée** : la section n’a pas été démarrée et doit être complétée.
  - **Incomplète** : la section comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations de votre part. Pour obtenir de l’aide, consultez les sections plus haut dans ce document.
  - **Complète** : la section contient toutes les données nécessaires et ne comporte aucune erreur. Toutes les sections de l’offre doivent être complètes pour que vous puissiez envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour vous assurer que votre application est correctement testée. Vous pouvez également fournir des notes supplémentaires utiles pour comprendre votre offre.

Pour envoyer l’offre à des fins de publication, sélectionnez **Publier**.

Nous vous enverrons un e-mail pour vous faire savoir si une préversion de l’offre est disponible pour vérification et approbation. Pour que votre offre soit publiée pour le public (ou pour un public privé, si elle est privée), accédez à l’Espace partenaires et sélectionnez **Mise en service**.
