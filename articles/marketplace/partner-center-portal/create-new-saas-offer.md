---
title: Créer une offre SaaS, Place de marché Azure et Microsoft AppSource
description: Explique comment créer une nouvelle offre Software as a service (SaaS) pour lister ou vendre dans Microsoft AppSource, la Place de marché Azure, ou par le biais du programme de fournisseur de solutions Cloud (CSP) à l’aide du programme de la Place de marché commerciale Microsoft dans Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 08/07/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 6140d369c2dbc0968d2f28ff6b8cf447dd721d10
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166720"
---
# <a name="create-a-saas-offer-in-the-commercial-marketplace"></a>Créer une offre SaaS dans la Place de marché commerciale

Pour commencer à créer des offres SaaS (Software as a service) dans la place de marché commerciale, veillez à d’abord [Créer un compte Espace partenaires](./create-account.md), puis ouvrez le [tableau de bord de la place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) et sélectionnez l’onglet **Vue d’ensemble**.

> [!NOTE]
> Si vous créez une offre SaaS préconfigurable, veillez à implémenter l’intégration aux [API de traitement SaaS](./pc-saas-fulfillment-apis.md).  L’intégration avec les API est le seul moyen de garantir le bon fonctionnement de la fonction de traitement de la place de marché. Vous devez également vérifier que votre application utilise l’authentification Azure AD avec l’authentification unique (SSO). Consultez [Azure AD et offres SaaS pouvant faire l’objet d’une transaction dans la Place de marché commerciale](../azure-ad-saas.md).

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Sur la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Logiciel en tant que service**.

   ![Illustre le menu de navigation de gauche.](./media/new-offer-saas.png)

> [!NOTE]
> Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires apparaissent uniquement dans les vitrines après la republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1** ici, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
- L’alias d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

Le **statut de publication** affiche une représentation visuelle des étapes requises pour publier cette offre et le temps nécessaire à chaque étape. Les icônes des étapes de publication non complétées sont grisées.

Le menu **Présentation de l’offre** contient une liste de liens pour réaliser des opérations sur cette offre. Cette liste d’opérations change selon la sélection que vous faites pour votre offre.  

- Si l’offre est un brouillon : Supprimer le brouillon
- Si l’offre est publiée : Arrêter de vendre l’offre
- Si l’offre est disponible en préversion : Mettre en service
- Si vous n’avez pas terminé la déconnexion de l’éditeur : Annuler la publication

## <a name="offer-setup"></a>Configuration de l’offre

Cette page nécessite les informations suivantes.

- **Souhaitez-vous vendre via Microsoft ?** (Oui/Non)
  - **Oui**, je souhaite vendre via Microsoft et que Microsoft héberge les transactions en mon nom
  - **Non**, je préfère répertorier mon offre uniquement dans les Places de marché et traiter les transactions de manière indépendante.

### <a name="sell-through-microsoft"></a>Vendre via Microsoft

La vente via Microsoft fournit une meilleure découverte et acquisition de client, permet à Microsoft d’héberger des transactions de la Place de marché, et tire profit des capacités commerciales mondiales disponibles de Microsoft.

#### <a name="saas-offer-requirements"></a>Conditions des offres SaaS

Afin de répertorier des offres SaaS avec la Place de marché commerciale sur Partner Center, les critères suivants doivent être respectés :

- Votre offre doit utiliser [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification et la gestion d’identités.
- Votre offre doit utiliser des [API de traitement SaaS](pc-saas-fulfillment-api-v2.md) pour intégrer avec la Place de marché Azure.

#### <a name="saas-pricing-and-billing-options"></a>Tarifs SaaS et options de facturation

Lorsque des solutions SaaS sont exécutées dans l’abonnement Azure de l’éditeur, les frais de licence payés par les clients incluent le coût de l’infrastructure sur laquelle le logiciel est déployé. L’utilisation de l’infrastructure Azure est présentée et facturée directement à vous, le partenaire. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs doivent grouper les frais d’utilisation de l’infrastructure Azure avec les frais liés aux licences logicielles.

SaaS propose une facturation mensuelle ou annuelle basée sur un tarif fixe pour chaque utilisateur, ou une facturation à l’utilisation à l’aide du service de facturation limitée. La place de marché commerciale de Microsoft fonctionne selon un modèle d’agence. Les prix sont définis par l’éditeur, les factures sont établies par Microsoft et l’éditeur est payé par Microsoft qui prélève des frais d’agence.

Il s’agit d’un exemple de ventilation des coûts et des versements pour illustrer le modèle d’agence (les prix répertoriés sont fournis à titre d’exemple uniquement et ne sont pas destinés à refléter les coûts réels) :

|**Coût de votre licence**|**100 $/mois**|
|:---|:---|
|Coût d’utilisation Azure (D1/1 cœur)|Facturé directement à l’éditeur au lieu du client|
|Microsoft facture au client le montant suivant :|100 $/mois (l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)|

|**Microsoft facture**|**100 $/mois**|
|:---|:---|
|Microsoft vous verse 80 % des revenus générés par les licences <br>**Pour les applications SaaS qualifiées, Microsoft paie 90 % de vos coûts de licence*|80 $/mois <br>*$* 90 $/mois*|

- Dans cet exemple, Microsoft facture 100 $ au client pour votre licence logicielle et verse 80 $ à l’éditeur.

> [!NOTE]
> **Frais de service réduits sur le marketplace** : pour certaines offres SaaS que vous avez publiées sur notre marketplace commercial, Microsoft réduit ses frais liés au service Marketplace de 20 % (comme indiqué dans le Contrat d’éditeur Microsoft) à 10 %. Pour que votre offre soit qualifiée, elle doit avoir été désignée par Microsoft comme étant une offre de co-vente Azure IP incitative. L’éligibilité doit être respectée pendant au moins cinq (5) jours ouvrés avant la fin de chaque mois civil pour bénéficier des frais de service réduits sur le marketplace pour le mois en question. Les frais de service réduits du marketplace s’appliquent aux offres SaaS de co-vente Azure IP incitatives, aux machines virtuelles, aux applications managées et à toutes les autres offres IaaS payantes qualifiées qui sont mises à disposition sur le marketplace commercial.

### <a name="list-through-microsoft"></a>Répertorier via Microsoft

Promouvoir votre entreprise avec Microsoft en créant une liste de la Place de marché. Choisir de répertorier votre offre uniquement et de ne pas faire de transactions via Microsoft signifie que Microsoft ne participe pas directement dans les transactions de licences logicielles. Il n’y a aucun frais de transaction associé et l’éditeur récupère 100 % des frais de licence logicielle collectés auprès des clients. Toutefois, l’éditeur a la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte.

#### <a name="get-it-now-free"></a>Obtenez-en une maintenant (gratuit)

Répertoriez gratuitement votre offre aux clients en fournissant une adresse valide (qui commence par *http* ou *https*), où ils peuvent obtenir une version d’essai via l’[authentification en un clic à l’aide d’Azure Active Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Par exemple : `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Essai gratuit (annonce)

Répertoriez votre offre aux clients avec un lien vers un essai gratuit en fournissant une adresse valide (qui commence par *http* ou *https*), où ils peuvent obtenir une version d’essai via l’[authentification en un clic à l’aide d’Azure Active Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Par exemple : `https://contoso.com/trial/saas-app`. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

> [!NOTE]
> Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure AD pour automatiser la création de comptes dans votre application. Les comptes Microsoft (MSA) ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

#### <a name="contact-me"></a>Me contacter

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Prospects](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Exemple d’annonce d’offre sur Place de marché

Voici un exemple de la façon dont les informations de l’offre s’affichent dans Microsoft AppSource :

:::image type="content" source="media/example-appsource-saas.png" alt-text="Illustre la façon dont cette offre s’affiche dans Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo de grande taille
2. Catégories
3. Secteurs d’activité
4. Adresse du support technique (lien)
5. Conditions d’utilisation
6. Politique de confidentialité
7. Nom de l’offre
8. Résumé
9. Description
10. Captures d’écran/vidéos
11. Documents

<br>Voici un exemple de la façon dont les informations de l’offre s’affichent dans le portail Azure :

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Illustre la façon dont cette offre s’affiche dans le portail Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Intitulé
2. Description
3. Liens utiles
4. Captures d’écran.

## <a name="enable-a-test-drive"></a>Activer une version d’évaluation

Une version d’évaluation est un bon moyen de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l’« essayer avant de l’acheter », ce qui se traduit par une hausse du taux de conversion et par la génération de prospects de qualité. [Découvrez-en plus sur les versions d’évaluation](../what-is-test-drive.md).

Pour activer une version d’évaluation pour une durée déterminée, cochez la case **Activer une version d’évaluation**. Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Pour plus d’informations, consultez [Proposer une version d’évaluation de votre offre sur la place de marché commerciale](test-drive.md).

### <a name="test-drive-resources"></a>Ressources pour les versions d’évaluation

- [Qu’est-ce qu’une version d’évaluation ?](../what-is-test-drive.md)
- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Vue d’ensemble](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF ; veillez à désactiver votre bloqueur de fenêtres publicitaires)

### <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Ressources de gestion des prospects supplémentaires
- [Questions fréquentes sur la gestion des prospects](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Erreurs courantes de configuration des prospects](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Document d’une page présentant la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

Cette page vous invite à définir les catégories et secteurs utilisés pour grouper votre offre selon les Places de marché, les contrats légaux de votre offre et la version de votre application.

### <a name="category"></a>Category

Votre offre sera publiée sur AppSource ou Place de marché Azure en fonction des possibilités de transaction associées à votre offre et à votre choix de catégorie. Pour plus d’informations, consultez [Comparaison entre Microsoft AppSource et Place de marché Azure](../comparing-appsource-azure-marketplace.md). Sélectionnez les catégories et sous-catégories qui correspondent le mieux à votre offre et à votre public cible. Sélectionnez :

- Au moins une et jusqu’à deux catégories, y compris une catégorie principale et une catégorie secondaire (facultatif).
- Jusqu’à deux sous-catégories pour chaque catégorie principale et/ou secondaire. Si aucune sous-catégorie n’est applicable à votre offre, sélectionnez **Non applicable**.

Consultez la liste complète des catégories et sous-catégories applicables à chaque vitrine dans [Meilleures pratiques pour le référencement des offres](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Secteurs d’activité

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

La sélection du secteur d’activité s’applique uniquement aux offres publiées sur AppSource.

### <a name="app-version"></a>Version de l’application

Ce champ est facultatif et est utilisé dans la place de marché AppSource pour identifier le numéro de version de votre offre.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrat standard pour la place de marché commerciale de Microsoft

Microsoft fournit un modèle de contrat Standard.

- **Utiliser le contrat Standard pour la place de marché commercial Microsoft ?**

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard pour la place de marché commerciale de Microsoft afin de faciliter les transactions sur la place de marché. Plutôt que d’élaborer des conditions générales personnalisées, les éditeurs de la place de marché commerciale peuvent choisir de proposer leur logiciel dans le cadre du contrat Standard, que les clients ne doivent accepter qu’une seule fois. Le contrat Standard est disponible ici : https://go.microsoft.com/fwlink/?linkid=2041178.

Vous pouvez choisir d’utiliser le contrat Standard au lieu de fournir vos propres conditions générales en activant la case à cocher « Utiliser le contrat Standard pour la place de marché commerciale ».

![Utiliser la case à cocher Contrat Standard](./media/use-standard-contract.png)

> [!NOTE]
> Après avoir publié une offre à l’aide du contrat Standard pour la place de marché commerciale de Microsoft, vous ne pouvez pas utiliser vos propres conditions générales. Vous devez faire un choix entre les deux. Vous proposez votre solution avec le contrat Standard **ou** avec vos propres conditions générales. Si vous souhaitez modifier les conditions du contrat Standard, vous pouvez le faire par le biais des Modifications du contrat Standard.

#### <a name="standard-contract-amendments"></a>Contrat Standard | Modifications

Les modifications du contrat Standard permettent aux éditeurs de sélectionner le contrat Standard par souci de simplicité et de personnaliser les conditions pour leur produit ou entreprise. Les clients sont invités à examiner les modifications apportées au contrat, puis à accepter le contrat Standard de Microsoft.

Deux types de modifications sont proposées aux éditeurs de la place de marché commerciale :

- Modifications universelles : Ces modifications sont appliquées de manière universelle au contrat Standard de tous les clients. Les modifications universelles s’affichent pour chaque client de l’offre dans le flux d’achat. Les clients doivent accepter les conditions du contrat Standard et la modification avant de pouvoir utiliser votre offre.
- Modifications personnalisées : Il s’agit de modifications spéciales apportées au contrat Standard qui ciblent certains clients uniquement via les ID de locataires Azure. Les éditeurs peuvent choisir le locataire qu'ils souhaitent cibler. Seuls les clients du locataire recevront les conditions personnalisées dans le flux d’achat de l’offre.  Les clients doivent accepter les conditions du contrat Standard et la ou les modifications avant de pouvoir utiliser votre offre.

>[!NOTE]
> Ces deux types de modifications s’ajoutent l’un à l’autre. Les clients ciblés par les modifications personnalisées reçoivent aussi la modification universelle apportée au contrat Standard lors de l’achat.

**Universal amendment terms to the Standard Contract for Microsoft's commercial marketplace (Termes d’amendement universel au Contrat Standard pour la place de marché commerciale Microsoft)** : entrez les termes des modifications universelles dans cette zone. Vous pouvez fournir un seul amendement universel par offre. Vous pouvez entrer un nombre illimité de caractères dans cette zone. Ces termes sont présentés aux clients dans AppSource, la Place de marché Azure et/ou le portail Azure pendant le flux de découverte et d’achat.

**Custom amendment terms to the Standard Contract for Microsoft's commercial marketplace (Termes d’amendement personnalisé au contrat Standard pour la place de marché commerciale Microsoft)** : commencez par sélectionner **Add custom amendment terms (Ajouter des termes d’amendement personnalisé)** . Vous pouvez fournir jusqu’à 10 termes d’amendement personnalisés par offre.

- **Termes d’amendement personnalisés** : saisissez vos conditions de modifications personnalisées dans la zone Termes d’amendement personnalisés. Vous pouvez entrer un nombre illimité de caractères dans cette zone. Seuls les clients des ID de locataire que vous spécifiez pour ces conditions personnalisées seront présentés avec les termes de l’amendement personnalisé dans le flow d’achat de l’offre dans le Portail Azure.  
- **ID de locataire** (requis) : chaque amendement personnalisé peut être ciblé vers jusqu’à 20 ID de locataire. Si vous ajoutez un amendement personnalisé, vous devez fournir au moins un ID de locataire. L’ID de locataire identifie votre client dans Azure. Vous pouvez demander à votre client cet ID et il peut le trouver en accédant à portal.azure.com > Azure Active Directory > Propriétés. La valeur de l’ID de répertoire est l’ID de locataire (par exemple, 50c464d3-4930-494c-963C-1e951d15360e). Vous pouvez également rechercher l’ID de locataire de l’organisation de votre client à l’aide de son URL de nom de domaine sur le site [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com)
- **Description** (facultatif) : fournissez si vous le souhaitez une description claire de l’ID de locataire, qui vous aide à identifier le client que vous ciblez avec cette modification.

#### <a name="terms-and-conditions"></a>Conditions générales

Si vous souhaitez fournir vos propres conditions générales, vous pouvez les saisir dans le champ Conditions générales. Vous pouvez saisir jusqu’à 10 000 caractères dans ce champ. Si vos conditions générales nécessitent une description plus longue, saisissez un lien URL unique dans ce champ où trouver les conditions générales. Ceci affichera un lien actif aux clients.

Les clients doivent accepter ces conditions avant de pouvoir essayer de votre application.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

Cette page affiche les langues (et les marchés) disponibles pour votre offre. English (États-Unis) est actuellement le seul emplacement disponible. De plus, cette page affiche le statut de l’annonce spécifique à une langue et l’heure et la date à laquelle elle a été ajoutée. Vous devez définir les informations de la Place de marché (nom de l’offre, description, termes de recherche, etc.) pour chaque langue / marché.

> [!NOTE]
> Il n’est pas nécessaire que le contenu de l’offre (telle que la description de l’offre, les documents, les captures d’écran, les conditions d’utilisation et la politique de confidentialité) soit en anglais tant que la description de l’offre commence par l’expression : « Cette application est disponible uniquement en [langue qui n’est pas l’anglais] .» Il est également possible de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

### <a name="offer-listings"></a>Liste des offres

Renseignez les informations à afficher dans la place de marché, dont les descriptions des composants marketing et de votre offre.

- **Nom** (obligatoire) : le nom défini ici s’affiche comme titre de l’annonce de votre offre sur la ou les places de marché choisies. Le nom est prérempli selon votre précédente entrée **Nouvelle offre**. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résumé** (obligatoire) : renseignez une courte description de votre offre à utiliser dans les résultats de la recherche d’annonces dans une place de marché. Jusqu’à 100 caractères peuvent être entrés dans ce champ.
- **Description** (obligatoire) : renseignez une description de votre offre à afficher dans la présentation des annonces sur une place de marché. Vous pouvez inclure une proposition de valeur, des avantages principaux, des associations de catégorie ou de secteur, des opportunités d’achats dans une application, des publications requises et un lien pour en savoir plus. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ, balisage compris. Pour obtenir plus d’astuces, consultez [Rédiger une bonne description d’application](/windows/uwp/publish/write-a-great-app-description).
- **Search keywords (Mots clés de recherche)** : entrez un maximum de trois mots clés que les clients pourront utiliser pour rechercher votre offre sur les places de marché.
- **Instructions de prise en main** (obligatoire) : expliquez aux clients potentiels comment configurer et commencer à utiliser votre application.  Ce démarrage rapide peut contenir des liens vers des ressources en ligne plus détaillées. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ.

#### <a name="description"></a>Description

Ce champ est obligatoire.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Liens

- **Politique de confidentialité** (obligatoire) : lien vers la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.
- **Supports Marketing du programme du fournisseur de solutions Cloud** (facultatif) : vous devez fournir un lien vers les ressources marketing si vous choisissez d’étendre votre offre au programme [Fournisseur de solutions Cloud (CSP)](../cloud-solution-providers.md). Le programme CSP étend votre offre à un spectre plus large de clients qualifiés en permettant aux partenaires CSP de grouper votre offre, de la mettre sur le marché et de la revendre. Ces revendeurs doivent accéder à des ressources marketing de votre offre. Pour plus d’informations, consultez [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Liens utiles** (facultatif) : documents en ligne supplémentaires et facultatifs concernant votre application ou services connexes répertoriés en fournissant un **titre** et une **URL**. Ajoutez des liens utiles supplémentaires en cliquant sur **+ Ajouter une URL**.

#### <a name="contact-information"></a>Informations de contact

- **Contacts** : pour chaque contact client, renseignez le **Nom**, le **Numéro de téléphone** et l’**Adresse e-mail** d’un employé (ces informations *ne sont pas* affichées publiquement). Une **URL de support** est nécessaire pour le groupe **Contact support** (ceci *n’est pas* affiché publiquement).

  - **Contact du support** (obligatoire) : pour toute question de support générale.
  - **Contact en ingénierie** (obligatoire) : pour toute question technique.
  - **Channel Manager contact (Contact du responsable commercial)** (obligatoire) : pour toute question du revendeur relative au programme CSP.

#### <a name="files-and-images"></a>Fichiers et images

- **Documents** (obligatoire) : ajoutez des documents marketing connexes à votre offre, au format PDF, au minimum un et au maximum trois documents par offre.
- **Images** - Chargez des images de logo au format PNG :

  - **Petite** (48 x 48 pixels, requise)
  - **Moyenne** (90 x 90 pixels, requise)
  - **Grande** (216 x 216 pixels, requise)
  - **Immense** (255 x 115 pixels) (facultative)

   Ces trois tailles de logo sont utilisées dans des vitrines différentes :

   -   Le petit logo (48 x 48 pixels) s’affiche dans les résultats de recherche de la place de marché Azure et dans la page principale de Microsoft AppSource et les pages de résultats de recherche. 
   -   Le logo moyen (90 x 90 pixels) s’affiche lorsque vous créez une nouvelle ressource dans Microsoft Azure.
   -   Le grand logo (entre 216 x 216 et 350 x 350 pixels) apparaît sur la page de votre liste d’offres dans la Place de marché Azure et Microsoft AppSource.

- **Captures d’écran** (obligatoire) : ajoutez au maximum cinq captures d’écran illustrant votre offre, dimensionnées à 1280 x 720 pixels. Toutes les images doivent être au format .PNG.
- **Vidéos** (facultatif) : ajoutez des liens vers des vidéos présentant votre offre. Vous pouvez utiliser des liens de vidéos YouTube et/ou Vimeo, qui seront présentées aux clients en même temps que votre offre. Vous devez également inclure une miniature de la vidéo, au format PNG de 1280 x 720 pixels. Vous pouvez afficher un maximum de quatre vidéos par offre.

>[!NOTE]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

- [Meilleures pratiques pour les annonces d’offre d’une place de marché](../gtm-offer-listing-best-practices.md)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="preview-audience"></a>Public de la version préliminaire

Cette page vous permet de définir un **public limité pour la préversion** avant de publier votre offre à destination d’un public plus large dans la place de marché.

> [!IMPORTANT]
> Après avoir vérifié votre offre en Préversion, sélectionnez **Publier** pour que votre offre puisse être publiée publiquement sur la place de marché.

Ajoutez une seule adresse e-mail de compte AAD/MSA par ligne, avec une description facultative.

Ajoutez manuellement jusqu’à dix adresses e-mail, ou 20 en chargeant un fichier CSV, pour un compte Microsoft (MSA) ou des comptes Azure Active Directory (AAD) existants afin d’aider à la validation de votre offre avant sa publication. En ajoutant ces comptes, vous définissez un public qui sera autorisé à accéder à la préversion de votre offre avant sa publication sur les places de marché. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion est autorisé à accéder à votre offre _avant_ sa publication sur les places de marché. Vous pouvez aussi choisir de créer un plan et de le rendre disponible uniquement à un public privé. Dans l’onglet **Liste des plans**, vous pouvez définir un public privé en cochant **Il s’agit d’un plan privé**. Vous pouvez définir un public privé comprenant jusqu’à 20 000 clients à l’aide des ID de locataire Azure.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="technical-configuration"></a>Configuration technique

L’onglet **Configuration technique** définit les détails techniques utilisés par le marketplace pour communiquer avec votre service SaaS. Cette connexion permet d’approvisionner votre offre pour le client final, s’il choisit de l’acquérir et de la gérer.

>[!NOTE]
>Vous devez implémenter l’intégration avec des [API de traitement SaaS](./pc-saas-fulfillment-api-v2.md) avant de configurer ces détails dans les détails de l’offre. Vous devez créer une page d’accueil, et votre application doit utiliser l’authentification Azure AD avec l’authentification unique (SSO). Pour plus d’informations, consultez [Azure AD et offres SaaS pouvant faire l’objet d’une transaction dans la Place de marché commerciale](../azure-ad-saas.md).

Les diagrammes et les explications détaillées décrivant l’utilisation des champs collectés sont disponibles dans la documentation [des API](./pc-saas-fulfillment-api-v2.md).

- **URL de la page d’arrivée** (obligatoire) : définissez l’URL du site SaaS (par exemple : `https://contoso.com/signup`) sur laquelle les clients finaux arriveront après avoir acquis votre offre sur le marketplace et déclenché le processus de configuration à partir de l’abonnement SaaS nouvellement créé.  Cette URL sera appelée avec le paramètre de jeton d’identification d’achat sur le marketplace qui identifie de façon unique l’achat SaaS du client final spécifique.  Vous devez échanger ce jeton contre les détails de l’abonnement SaaS correspondant à l’aide de l’API [de résolution](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription).  Ces informations et toutes celles que vous souhaitez recueillir doivent être utilisées au sein d’une page web interactive pour le client, intégrée à votre expérience afin de terminer l’inscription du client final et d’activer son achat.  Sur cette page, l’utilisateur doit s’inscrire par le biais de l’authentification en un clic à l’aide d’Azure Active Directory (Azure AD). <br> <br> Cette URL avec le paramètre de jeton d’identification d’achat sur le marketplace est également appelée lorsque le client final lance l’expérience SaaS gérée à partir de Portail Azure ou du centre d’administration M365. Vous devez gérer les deux flux, lorsque le jeton est fourni pour la première fois après l’achat pour les nouveaux clients et lorsqu’il est fourni pour le client existant qui gère son SaaS. <br> <br> La page d’arrivée que vous configurez ici doit être opérationnelle 24 heures sur 24, 7 jours sur 7. C’est la seule façon pour vous d’être averti des nouveaux achats de vos offres SaaS effectuées sur le marketplace ou des demandes de configuration d’un abonnement actif à une offre.

- **Webhook de connexion** (obligatoire) : pour tous les événements asynchrones que Microsoft doit vous envoyer (par exemple, l’abonnement SaaS a été annulé), nous vous demandons de fournir une URL de Webhook de connexion. Nous appellerons cette URL pour vous informer de l’événement. <br> <br> Le Webhook que vous fournissez doit être opérationnel 24 heures sur 24, 7 jours sur 7, car c’est la seule façon dont vous serez informé des mises à jour concernant les abonnements SaaS de vos clients achetés via le marketplace.  Si vous ne disposez pas d’un système de Webhook, la configuration la plus simple consiste à disposer d’une application de logique de point de terminaison HTTP qui écoute les événements qui sont publiés et les traite de façon appropriée (par exemple, `https://prod-1westus.logic.azure.com:443/work`). Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques](../../logic-apps/logic-apps-http-endpoint.md).

- **ID de locataire Azure AD** (obligatoire) : dans le portail Azure, vous devez [créer une application Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) afin que nous puissions confirmer que la connexion entre nos deux services se fait bien dans le cadre d’une communication authentifiée. Pour trouver l’[ID du locataire](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), rendez-vous dans Azure Active Directory et sélectionnez **Propriétés**, puis cherchez le numéro de l’**ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID d’application Azure AD** (obligatoire) : vous avez également besoin de l’[ID de votre application](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Pour obtenir cette valeur, accédez à votre Azure Active Directory et sélectionnez **Inscriptions d’applications**, puis cherchez le numéro d’**ID d’application** affiché (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`).

>[!NOTE]
>L’ID d’application Azure AD est associé à votre ID d’éditeur dans votre compte Espace partenaires.  Assurez-vous que le même ID d’application est utilisé dans toutes vos offres.

>[!NOTE]
>Si l’éditeur possède deux comptes différents ou plus dans Espace partenaires, il doit utiliser autant d’ID d’application Azure AD différents, un pour chacun des comptes. Chaque compte partenaire dans Espace partenaires doit avoir recours à un ID d’application Azure AD unique pour toutes les offres SaaS publiées par son intermédiaire.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="plan-overview"></a>Vue d’ensemble du plan

Cette page vous permet de proposer différentes options de plan dans une même offre. Ces plans (parfois appelés références SKU) peuvent différer en termes de version, de monétisation ou de niveaux de service. Vous devez configurer au moins un plan avant de pouvoir vendre votre offre dans la place de marché.

Vous pouvez créer jusqu’à 100 plans pour chaque offre : jusqu’à 45 peuvent être privés. En savoir plus sur les offres privées dans les [offres du marketplace commercial de Microsoft](../private-offers.md).

Une fois créé, vous verrez vos noms de plan, ID, modèles de tarification, disponibilité (public ou privé), l’état de publication actuel et les actions disponibles.

Les **Actions** disponibles dans **Vue d’ensemble du plan** varient selon l’état actuel de votre plan et peuvent inclure :

- Si l’état du plan est **Brouillon** : supprimer le brouillon
- Si l’état du plan est **Publié** : arrêter de vendre le plan ou Synchroniser public privé

**Créer un plan** (au moins un plan pour ceux qui choisissent de vendre via Microsoft)

- **ID du plan :** Créez un ID de plan unique pour chaque plan dans cette offre. Cet ID sera visible par les clients dans l’adresse URL du produit et des modèles Resource Manager (si applicable). Utilisez uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. 50 caractères maximum autorisés pour cet ID de plan. L’ID ne peut pas être modifié après avoir sélectionné Créer.
- **Nom du plan :** Les clients verront ce nom lorsqu’ils décideront du plan à sélectionner dans votre offre. Créez un ID d’offre unique pour chaque plan dans cette offre. Le nom du plan sert à différencier les plans logiciels pouvant faire partie de la même offre (par exemple, nom de l’offre : Windows Server ; plans : Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Liste des plans

Cette page vous permet de définir le nom et la description du plan. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Nom** : ces informations sont préremplies en fonction de votre saisie **Nouveau plan** et serviront de titre du « plan logiciel » de votre offre, affiché sur la place de marché.
- **Description** : cette description permet d’expliquer ce qui rend ce plan logiciel unique et de pointer les différences par rapport aux autres plans logiciels dans votre offre. Peut contenir jusqu’à 500 caractères.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

#### <a name="pricing-and-availability"></a>Tarification et disponibilité

Cette page vous permet de configurer les marchés dans lesquels sera disponible ce plan, ainsi que le modèle de monétisation souhaité, le prix et la fréquence de facturation. De plus, vous pouvez indiquer si vous voulez que le plan soit visible à tous ou uniquement à des utilisateurs spécifiques (un public privé).

#### <a name="markets-optional"></a>Marchés (facultatif)

Chaque plan doit être disponible sur au moins un marché. Sélectionnez **Modifier les marchés** et cochez la case des emplacements dans lesquels vous souhaitez que ce plan soit disponible. Cette page inclut une zone de recherche et une option pour sélectionner les pays/régions « reversant les taxes », pour lesquels Microsoft reverse les ventes et utilise les taxes en votre nom.

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché sera calculé selon le taux de change en vigueur. Vérifiez le prix de chaque place de marché avant toute publication. Affichez les prix à l’aide du lien Exporter les prix (xlsx) après avoir enregistré vos changements.

Sélectionnez **Enregistrer** avant de continuer.

#### <a name="pricing"></a>Tarifs

##### <a name="pricing-model"></a>Modèle de tarification

**Taux forfaitaire** : autorisez l’accès à votre offre via un tarif unique et fixe mensuel ou annuel. Cela est parfois désigné sous le nom de « tarification en fonction du site ». Avec ce modèle de tarification, vous pouvez éventuellement définir des plans contrôlés qui utilisent l’API du service de contrôle de place de marché pour payer les clients en fonction d’unités non standard.  Pour plus d’informations sur la facturation limitée, consultez [facturation limitée à l’aide du service de contrôle de la place de marché](./saas-metered-billing.md).  Vous devez également utiliser cette option si le comportement d’utilisation est en rafales pour votre service SaaS.  Nous déconseillons au client de changer fréquemment de plan sur une base quotidienne ou horaire.

**Par utilisateur** : autorisez l’accès à votre offre via un prix basé sur le nombre d’utilisateurs ou de sièges occupés. Ce modèle basé sur l’utilisateur vous permet de définir le nombre minimal et maximal d’utilisateurs autorisés en fonction du prix. Ainsi, plusieurs gammes de prix peuvent être configurées en fonction du nombre d’utilisateurs en configurant plusieurs plans.  Ces champs sont facultatifs. Si vous ne sélectionnez pas cette option, le nombre d’utilisateurs sera considéré sans limite (avec un minimum de 1 et un maximum d’autant d’utilisateurs que peut prendre en charge le système). Ces champs peuvent être modifiés dans le cadre d’une mise à jour de votre plan.

Une fois publié, le choix du modèle de tarification ne peut plus être modifié. En outre, tous les plans d’une même offre doivent partager le même modèle de tarification.

##### <a name="user-limits"></a>Limites de l’utilisateur

Le cas échéant, sélectionnez et définissez le nombre minimal et maximal d’utilisateurs.

##### <a name="billing-term-and-price"></a>Tarifs et durée de facturation

Sélectionnez la **durée** et le **prix** que les clients doivent payer. Vous devez renseigner au moins un prix Mensuel ou Annuel, ou les deux options peuvent être disponibles pour les clients.

Les prix fixés en USD (USD = dollar américain) sont convertis dans la devise locale de tous les marchés sélectionnés selon les taux de change en vigueur disponibles lors de l’enregistrement. Validez ces prix avant la publication en exportant la feuille de calcul de tarification et en vérifiant le prix de chaque marché. Si vous souhaitez fixer des prix personnalisés dans un marché individuel, modifiez et importez la feuille de calcul de tarification. Vous êtes responsable de valider cette tarification et possédez ces paramètres.
*\*Vous devez d’abord enregistrer les modifications de votre tarification pour autoriser l’export des données de tarification.*

Vérifiez vos prix avec soin avant la publication, car il y a des restrictions sur ce qui peut être modifié après la publication d’un plan :

- Une fois qu’un plan est publié, le modèle de tarification ne peut pas être modifié.
- Une fois qu’une fréquence de facturation est publiée pour un plan, elle ne peut pas être supprimée ultérieurement.
- Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement.

#### <a name="free-trial"></a>Version d’évaluation gratuite

Les offres SaaS via le marketplace commercial vous permettent de fournir un essai gratuit d’un mois lors de la vente par le biais de Microsoft. Pour tous les modèles de facturation et toutes les conditions, à l’exception des forfaits avec compteur, des essais gratuits sont pris en charge. Cette option permet aux clients de bénéficier d’une faible barrière à l’entrée au bout d’un mois d’accès gratuit.  Si vous choisissez d’activer une version d’évaluation gratuite pour les plans de votre offre, le client ne pourra pas passer à un abonnement payant avant la fin de la période initiale d’un mois.  Pendant ce temps, les clients qui achètent votre offre peuvent essayer n’importe lequel des plans pris en charge ayant activé l’essai gratuit et les convertir entre eux.  La conversion en abonnement payant est effectuée automatiquement à la fin de la période.

>[!NOTE]
>Si le client choisit de passer à un plan sans essai gratuit, la conversion aura lieu, mais l’essai gratuit sera immédiatement perdue. En outre, une fois qu’un client a commencé à payer un plan, il ne peut plus obtenir d’essai gratuit sur le même abonnement, même s’il est converti en référence SKU qui prend en charge les essais gratuits.

Vous pouvez configurer un essai gratuit pour chaque plan de votre offre ici. Activez la case à cocher pour autoriser un essai d’un mois.

![Case à cocher d’essai gratuit d’un mois](./media/free-trial-enable.png)

>[!NOTE]
>Une fois que votre offre négociable a été publiée avec un essai gratuit, elle ne peut plus être désactivée pour ce plan. Vérifiez que ce paramètre est correct pour la première publication afin d’éviter d’avoir à recréer le plan.

Pour obtenir des informations sur les abonnements clients qui participent actuellement à un essai gratuit `isFreeTrial`, utilisez la nouvelle propriété API, qui sera marquée comme true ou false. Pour plus d’informations, consultez [SaaS Obtenir l’API d’abonnement](pc-saas-fulfillment-api-v2.md#get-subscription).

>[!NOTE]
>Les essais gratuits ne sont pas pris en charge pour les plans qui tirent parti du service de contrôle de la place de marché.

#### <a name="plan-visibility"></a>Visibilité du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible de tous ou d’un public spécifique. Vous pouvez attribuer l’appartenance dans ce public restreint à l’aide des ID locataire Azure AD.

##### <a name="privacy"></a>Confidentialité

Activez l’option **This is a private plan (Il s’agit d’un plan privé)** pour que votre plan ne soit visible que du public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois qu’un plan est publié comme visible de tous, il doit le rester. Il n’est pas possible de le reconfigurer en tant que plan privé.

##### <a name="restricted-audience-tenant-ids"></a>**Public restreint (ID locataire)**

Affectez le public qui aura accès à ce plan privé. L’accès est affecté à l’aide d’ID locataire avec l’option d’inclure une description de chaque ID locataire affecté. Un maximum de 10 ID locataire peut être ajouté, ou de 20 000 ID locataire client en important un fichier CSV.

Un locataire est une représentation d’une organisation, dont l’ID est représenté sous la forme d’un GUID (Identificateur Global Unique, un nombre entier de 128 bits utilisé pour identifier les ressources). Il s’agit d’une instance Azure AD dédiée, reçue par une organisation ou un développeur d’applications quand l’organisation ou le développeur d’applications crée une relation avec Microsoft (par exemple en cas d’inscription à Azure, Microsoft Intune ou Microsoft 365). Chaque client Azure AD est distinct et indépendant des autres clients Azure AD. Pour vérifier le locataire, connectez-vous au Portail Azure avec le compte que vous souhaitez utiliser pour gérer votre application. Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous pouvez voir son nom directement sous le nom de votre compte. Dans l’angle supérieur droit du Portail Azure, survolez le nom de votre compte avec la souris pour voir votre nom, votre adresse e-mail, votre répertoire et votre ID de locataire (un GUID), ainsi que votre domaine. Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID. Vous pouvez aussi rechercher l’ID de locataire de votre organisation à l’aide d’une URL de nom de domaine de type [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Tandis que les offres SaaS utilisent des ID de locataire pour définir un public privé, d’autres types d’offre peuvent utiliser des ID d’abonnement Azure (qui sont également représentés sous forme de GUID).

> [!NOTE]
> Le public privé (ou public restreint) n’est pas le même que le public pour la préversion. Dans la page **[Préversion](#preview-audience)** , vous pouvez définir un public pour la préversion. Un public de préversion est autorisé à accéder à votre offre *avant* sa publication sur la place de marché. Tandis que la désignation de public privé ne s’applique qu’à un plan spécifique, le public pour la préversion peut consulter tous les plans (privés ou non), mais seulement pendant la période limitée de la préversion, pendant le test et la validation du plan.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exemple de liste de plans au sein d’une offre de place de marché

:::image type="content" source="media/marketplace-plan.png" alt-text="Exemple de liste de plans de marketplace avec remarques":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom du plan
2. Description du plan

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Audience du revendeur CSP (fournisseur de solutions cloud)

Choisir de rendre votre offre disponible dans le programme CSP permet aux fournisseurs de solutions Cloud de vendre votre produit dans une offre groupée de solution à leurs clients. Pour plus d’informations, consultez [Fournisseur de solutions cloud](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Publish

Une fois que vous avez complété toutes les sections obligatoires de l’offre, sélectionnez **Vérifier et publier** en haut à droite du portail.

### <a name="submit-offer-to-preview"></a>Soumettre l’offre en préversion

S’il s’agit de votre première publication de cette offre, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre.
  - **Non démarrée** : la section n’a pas encore été modifiée et doit être complétée.
  - **Incomplète** : la section comporte des erreurs qui doivent être corrigées ou nécessite un complément d’informations. Vous devez revenir à la section et la mettre à jour.
  - **Complète** : la section est complète, toutes les données obligatoires ont été fournies et aucune erreur n’est présente. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres remarques supplémentaires utiles pour comprendre l’application.
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail pour vous faire savoir si une version d’essai de l’offre est disponible pour vérification et approbation. Retourner dans Espace partenaires et sélectionner **Publier** pour publier votre offre auprès du public (ou du public privé, si l’offre est privée).

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
