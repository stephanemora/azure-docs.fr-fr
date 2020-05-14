---
title: Créer une offre de conteneur Azure – Place de marché Azure
description: Cet article explique comment créer et publier une offre de conteneur sur la Place de marché Azure.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: f2a3b6355fdaa8cd90525fac9d29324270dfad02
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791933"
---
# <a name="create-an-azure-container-offer"></a>Créer une offre de conteneur Azure

> [!IMPORTANT]
> Nous déplaçons la gestion de vos offres de conteneur Azure du portail Cloud Partner vers l’Espace partenaires. Tant que vous n’avez pas effectué la migration de vos offres, suivez les instructions de [Conteneurs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) dans le portail Cloud Partner pour la gestion de vos offres.

Cet article explique comment créer et publier une offre de conteneur sur la Place de marché Azure. Avant de commencer, si ce n’est déjà fait, [créez un compte de la Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Vérifiez que votre compte est inscrit dans le programme du marketplace commercial.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page de présentation, sélectionnez **+ Nouvelle offre** > **Conteneur Azure**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-azure-container.png)

> [!TIP]
> Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires apparaissent uniquement dans les vitrines après la republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

### <a name="offer-id-and-alias"></a>ID et alias de l’offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
- Cet alias ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

La page **Vue d’ensemble de l’offre** montre une représentation visuelle des étapes nécessaires à la publication de cette offre (à la fois terminées et à venir) et indique la durée d’exécution de chaque étape.

Cette page montre des liens différents en fonction de l’état actuel de l’offre. Par exemple :

- Si l’offre est un brouillon : [Supprimer le brouillon de l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Si l’offre est publiée : [Arrêter de vendre l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Si l’offre est disponible en préversion : [Mise en service](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Si vous n’avez pas complété Déconnexion de l’éditeur : [Annuler la publication](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuration de l’offre

Effectuez les étapes suivantes pour configurer votre offre.

### <a name="connect-lead-management--optional"></a>Connecter la gestion des prospects - Facultatif

Lors de la publication de votre offre sur la Place de marché commerciale avec l’Espace partenaires, vous pouvez la connecter à votre système de Gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit.

1. **Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix**. L’Espace partenaires prend en charge les systèmes CRM suivants :

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Si votre système de CRM n’est pas listé ci-dessus, utilisez une [table Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou un [point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) pour stocker les données des prospects, puis exportez les données vers votre système CRM.

2. Connectez votre offre à la destination de prospect tout en la publiant dans l’Espace partenaires.
3. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l’Espace partenaires, nous validons la connexion et nous vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement en version préliminaire.
4. Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

Voici des ressources de gestion des prospects supplémentaires :

- [Vue d’ensemble de la gestion des prospects](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Questions fréquentes sur la gestion des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erreurs courantes de configuration des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- PDF [Vue d’ensemble de la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (Assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="properties"></a>Propriétés

C’est dans cette page que vous définissez les catégories utilisées pour grouper votre offre selon les Places de marché et les contrats légaux associés.

#### <a name="category"></a>Category

Sélectionnez entre 1 et 5 catégories. Ces catégories servent à placer votre offre dans les zones de recherche appropriées sur la Place de marché, et elles s’affichent dans la page des détails de votre offre. Dans la description de l’offre, expliquez comment votre offre prend en charge ces catégories. Les conteneurs s’affichent sous **Conteneurs**, puis la catégorie **Images conteneur** s’affiche à son tour.

#### <a name="legal"></a>Informations juridiques

Vous devez fournir les conditions générales de l’offre. Nous avons deux options :

- Utilisez le contrat Standard pour la Place de marché commerciale Microsoft.
- Fournissez vos propres conditions générales.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrat Standard pour la Place de marché commerciale Microsoft

Nous proposons un modèle de contrat Standard pour faciliter les transactions dans la Place de marché commerciale. Vous pouvez choisir de proposer votre solution dans le cadre du contrat Standard, que les clients ne doivent vérifier et accepter qu’une seule fois. Il s’agit d’une bonne option si vous ne souhaitez pas créer des conditions générales personnalisées.

Pour plus d’informations sur le contrat Standard, consultez [Contrat standard pour la Place de marché commerciale de Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Vous pouvez également télécharger le fichier PDF [Contrat Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assurez-vous que le bloqueur de fenêtres publicitaires est désactivé).

Pour utiliser le contrat Standard, cochez la case **Utiliser le Contrat Standard pour la Place de marché commerciale Microsoft**, puis cliquez sur **Accepter**.

> [!NOTE]
> Après avoir publié une offre à l’aide du contrat Standard pour la Place de marché commerciale de Microsoft, vous ne pouvez pas utiliser vos propres conditions générales. Proposez votre solution avec le contrat Standard ou dans le cadre de vos propres conditions générales.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustration de la case à cocher concernant l’utilisation du contrat Standard pour la Place de marché commerciale Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Vos propres conditions générales

Pour fournir vos propres conditions générales, entrez-les dans la zone **Conditions générales**. Vous pouvez entrer un nombre illimité de caractères de texte dans cette zone. Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Annonce de l’offre.

## <a name="offer-listing"></a>Annonce de l’offre

Dans cette page, vous pouvez définir les détails de l’offre qui doivent s’afficher dans la Place de marché. Cela comprend le nom, la description et les images de l’offre.

> [!NOTE]
> Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par « This application is available only in [langue non anglaise] ». Il est également possible de fournir un lien utile pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de l’annonce de l’offre.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche comme titre de votre offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque et de copyright).
- Ne peut pas compter plus de 50 caractères.
- Ne peut pas inclure d’emojis.

### <a name="search-results-summary"></a>Résumé des résultats de recherche

Brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="long-summary"></a>Résumé long

Description plus détaillée de votre offre. Ce champ peut comporter jusqu’à 256 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre, jusqu’à 3 000 caractères. S’affichera pour les clients dans la vue d’ensemble de la Place de marché.

Incluez un ou plusieurs des éléments suivants dans votre description :

- La valeur et les avantages principaux de votre offre
- Catégorie ou associations de secteurs, ou les deux
- Opportunités d’achats dans l’application
- Toutes les informations requises

Voici quelques conseils pour rédiger votre description :

- Décrivez clairement la valeur de votre offre dans les premières phrases de votre description. Incorporez les éléments suivants :
  - Description de l’offre.
  - Le type d’utilisateur qui tire parti de l’offre
  - Les besoins ou problèmes auxquels l’offre répond.
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application offre.
- Essayez d’utiliser le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés.

Pour que la **description** de votre offre soit plus accrocheuse, utilisez l’éditeur de texte enrichi pour mettre en forme votre description à l’aide d’une numérotation, de puces, de caractères gras ou italiques, ainsi que de mises en retrait.

:::image type="content" source="media/text-editor2.png" alt-text="Illustre l’éditeur de texte enrichi." border="false" :::

- Utilisez cette liste déroulante pour appliquer un style de paragraphe au texte.

    :::image type="content" source="media/text-editor3.png" alt-text="Illustre le contrôle de style de texte dans l’éditeur de texte enrichi." border="false":::

- Utilisez ces icônes pour appliquer une numérotation ou des puces au texte.

     :::image type="content" source="media/text-editor4.png" alt-text="Illustre les contrôles de liste à puces et de liste numérotée dans l’éditeur de texte enrichi." border="false":::

- Utilisez ces icônes pour ajouter ou supprimer une mise en retrait dans le texte.

    :::image type="content" source="media/text-editor5.png" alt-text="Illustre les contrôles de mise en retrait du texte dans l’éditeur de texte enrichi." border="false":::

#### <a name="privacy-policy-link"></a>Lien de la politique de confidentialité

Entrez l’adresse web de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre offre respecte la loi et les réglementations en matière de confidentialité. Vous devez également veiller à publier une politique de confidentialité conforme sur votre site web.

#### <a name="useful-links"></a>Liens utiles

Fournissez des documents en ligne complémentaires à propos de votre offre. Vous pouvez ajouter jusqu’à 25 liens. Pour ajouter un lien, sélectionnez **+ Ajouter un lien**, puis complétez les champs suivants :

- **Titre** : les clients verront le titre dans la page de détails de votre offre.
- **Lien (URL)**  : entrez un lien pour que les clients puissent voir votre document en ligne. Le lien doit démarrer par http:// ou https://.

### <a name="contact-information"></a>Informations de contact

Entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact du support** et d’un **contact en ingénierie**. Ces informations ne sont pas lisibles par les clients, mais elles le sont pour Microsoft. Elles peuvent également être fournies aux partenaires fournisseurs de solutions cloud (CSP).

- Contact du support (obligatoire) : Pour toute question de support générale.
- Contact en ingénierie (obligatoire) : Pour les questions techniques et les problèmes de certification.
- Contact du programme du fournisseur de solutions cloud (facultatif) : Pour toute question du revendeur relative au programme CSP.

Dans la section **Contact du support**, indiquez le **site web de support** où les partenaires peuvent trouver un support pour votre offre selon que celle-ci est disponible dans Azure international, Azure Government ou les deux.

Dans la section **Contact du programme du fournisseur de solutions Cloud**, indiquez le lien (**Ressources Marketing du programme CSP**) où les partenaires CSP peuvent trouver des ressources marketing pour votre offre.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

Pour plus d’informations sur la création d’annonces d’offres, consultez [Bonnes pratiques pour le référencement des offres](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

### <a name="marketplace-images"></a>Images de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format .png. Les images floues seront rejetées.

>[!Note]
>Si vous rencontrez un problème lors du chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="store-logos"></a>Stocker des logos

 Fournissez des fichiers .png du logo de votre offre dans chacune des quatre tailles de pixel suivantes :

- **Petit** (48 x 48)
- **Moyen** (90 x 90)
- **Grand** (216 x 216)
- **Large** (255 x 115)

Les quatre logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

#### <a name="screenshots-optional"></a>Captures d’écran (facultatif)

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chacune doit avoir une taille de 1280 x 720 pixels et être au format .png.

#### <a name="videos-optional"></a>Vidéos (facultatif)

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Entrez le nom de la vidéo, son adresse web et une image miniature .png de 1280 x 720 pixels de la vidéo.

#### <a name="offer-examples"></a>Exemples d’offre

Les exemples suivants montrent où sont positionnés les champs de l’annonce de l’offre au sein de celle-ci.

Voici la page **Description de l’offre** sur la Place de marché Azure :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustre la page Description de l’offre dans la Place de marché Azure." :::

Voici les résultats de la recherche dans la Place de marché Azure :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustre les résultats de la recherche dans la Place de marché Azure.":::

Voici la page **Description de l’offre** dans le portail Azure :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustre la page Description de l’offre dans le portail Azure.":::

Voici les résultats de la recherche dans le portail Azure :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustre les résultats de la recherche dans le portail Azure.":::

## <a name="preview"></a>PRÉVERSION

Sous l’onglet Préversion, vous pouvez choisir de restreindre le **public de la préversion** en vue de valider votre offre avant de la publier en ligne.

> [!IMPORTANT]
> Une fois que vous avez affiché votre offre en **préversion**, vous devez sélectionner **Mise en service** afin de publier votre offre pour le public.

Spécifiez votre public de préversion en utilisant des GUID d’ID d’abonnement Azure et une description facultative pour chacun. Aucun de ces champs ne peut être vu par les clients.

> [!NOTE]
> Vous trouverez votre ID d’abonnement Azure dans la page Abonnements du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément (jusqu’à 10), soit en chargeant un fichier CSV (jusqu’à 100). En ajoutant ces ID d’abonnement, vous définissez qui peut voir une préversion de votre offre avant qu’elle ne soit publiée en ligne. Si votre offre est déjà publiée, vous pouvez choisir un public de préversion afin de tester des changements ou des mises à jour futurs.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de **préversion** peut voir et vérifier tous les plans d’offre avant qu’ils ne soient publiés sur la Place de marché, y compris ceux destinés à n’être publiés que pour un public **privé** (défini sous l’onglet Disponibilité).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="plan-overview"></a>Vue d’ensemble du plan

Cet onglet vous permet de proposer différentes options de plan dans une même offre. Avant, ces plans étaient appelés références SKU (Stock Keeping Units). Les plans peuvent varier selon les clouds disponibles, tels que les clouds mondiaux et les clouds gouvernementaux, et l’image qu’ils référencent. Pour référencer votre offre dans la Place de marché commerciale, vous devez configurer au moins un plan.

Une fois que vous avez créé vos plans, l’onglet **Vue d’ensemble du plan** affiche les informations suivantes :

- Noms des plans
- Modèle de tarification
- Disponibilité du cloud (mondial ou gouvernemental)
- État actuel de la publication
- Toutes les actions disponibles

Les actions disponibles dans Vue d’ensemble du plan varient selon l’état actuel de votre plan. Ils comprennent :

- **Supprimer le brouillon** : si l’état du plan est Brouillon.
- **Arrêter la vente du plan** : si l’état du plan est « publié en ligne ».

#### <a name="create-new-plan"></a>Créer un plan

Sélectionnez **Créer un plan**. La boîte de dialogue **Nouveau plan** s’affiche.

Dans la zone **ID du plan**, créez un identificateur de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères.

> [!NOTE]
> L’ID de plan ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Dans la zone **Nom du plan**, entrez un nom pour ce plan. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Créez un nom unique pour chaque plan dans cette offre. Par exemple, vous pouvez utiliser le nom d’offre **Windows Server** avec les plans **Windows Server 2016** et **Windows Server 2019**.

### <a name="plan-setup"></a>Configuration du plan

Cet onglet vous permet de choisir les clouds dans lesquels le plan est disponible. Les choix effectués sous cet onglet déterminent les champs affichés sous les autres onglets.

#### <a name="cloud-availability"></a>Disponibilité dans le cloud

Votre plan doit être disponible dans au moins un cloud.

Sélectionnez l’option **Azure international** pour que votre plan puisse être utilisé par les clients de toutes les régions Azure du monde qui recourent à la Place de marché commerciale. Pour plus d’informations, consultez [Prise en charge de la disponibilité géographique et des devises](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Sélectionnez l’option [**Cloud Azure Government**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) pour que votre solution y apparaisse. Il s’agit d’un cloud communautaire pour le secteur public, accessible à des clients d’agences gouvernementales fédérales, étatiques, locales ou tribales des États-Unis ainsi qu’à des partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires pour le cloud de cette communauté. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).

Avant d’effectuer une [publication](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) sur Azure Government, testez et vérifiez votre solution dans cette zone, car les résultats peuvent être différents. Pour créer et tester votre solution, demandez un compte d’essai dans la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans un cloud spécifique, vous ne pouvez pas supprimer ce cloud.

#### <a name="azure-government-cloud-certifications"></a>Certifications du cloud Azure Government

Cette option est visible uniquement si **Cloud Azure Government** est sélectionné sous **Disponibilité dans le cloud**.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS.

Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens décrivant ces certifications. Il peut s’agir de liens vers vos annonces sur le programme directement ou vers votre propre site web. Ces liens sont visibles uniquement par les clients Azure Government.

## <a name="plan-listing"></a>Liste des plans

Cet onglet affiche des informations spécifiques pour chaque plan de l’offre actuelle.

### <a name="plan-name"></a>Nom du plan

Ce champ est pré-rempli avec le nom que vous avez donné à votre plan quand vous l’avez créé. Vous pouvez changer ce nom, si nécessaire. Il peut comporter jusqu’à 50 caractères. Ce nom s’affiche comme titre de ce plan dans la Place de marché Azure et dans le portail Azure. Il est utilisé comme nom de module par défaut une fois que le plan est utilisable.

### <a name="plan-summary"></a>Résumé du plan

Bref résumé de votre plan logiciel (et non de l’offre). Ce résumé s’affiche dans les résultats de la recherche de la Place de marché Azure et peut contenir jusqu’à 100 caractères.

### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan logiciel unique, ainsi que les différences entre les plans de votre offre. Ne décrivez pas l’offre, mais simplement le plan. Cette description s’affiche dans la Place de marché Azure et sur le portail Azure dans la page **Description de l’offre**. Son contenu peut être le même que celui que vous avez fourni dans le résumé du plan et comporter jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer** après avoir renseigné ces champs.

#### <a name="plan-examples"></a>Exemples de plans

Les exemples suivants montrent où sont positionnés les champs de la liste des plans dans les différents affichages.

Voici les champs dans la Place de marché Azure lors de l’affichage des détails du plan :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustre les champs que vous voyez quand vous affichez les détails du plan dans la Place de marché Azure.":::

Voici les détails du plan sur le portail Azure :

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Illustre les détails du plan sur le portail Azure.":::

## <a name="plan-availability"></a>Disponibilité du plan

Si vous souhaitez masquer votre offre publiée afin que les clients ne puissent pas l’explorer, la parcourir ou l’acheter sur la Place de marché, cochez la case **Masquer le plan** sous l’onglet **Disponibilité**.

Ce champ est utilisé dans les cas suivants :

- L’offre est destinée à être utilisée indirectement quand elle est référencée par le biais d’une autre application.
- L’offre ne doit pas être achetée individuellement.
- Le plan a été utilisé pour des tests initiaux et n’est plus pertinent.
- Le plan a été utilisé pour des offres temporaires ou saisonnières et ne doit plus être proposé.

## <a name="technical-configuration"></a>Configuration technique

Les images conteneur doivent être hébergées dans une instance privée d’[Azure Container Registry](https://azure.microsoft.com/services/container-registry/). Sous l’onglet **Configuration technique**, fournissez des informations de référence pour le dépôt d’images conteneur situé dans Azure Container Registry.

Une fois l’offre publiée, votre image conteneur est copiée sur la Place de marché Azure dans un registre de conteneurs public. Toutes les demandes effectuées pour utiliser votre image conteneur sont traitées à partir du registre de conteneurs public de la Place de marché Azure, et non pas à partir de votre registre de conteneurs privé. Pour plus d’informations, consultez [Préparer vos ressources techniques de conteneur Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

### <a name="image-repository-details"></a>Détails du référentiel d’images

Fournissez les informations suivantes sous l’onglet **Détails du référentiel d’images**.

**ID d’abonnement Azure** : indiquez l’ID d’abonnement dans lequel l’utilisation est signalée et les services sont facturés pour le registre de conteneurs Azure qui contient votre image conteneur. Vous pouvez trouver cet ID dans la [page Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

**Nom du groupe de ressources Azure** : indiquez le nom du [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) qui contient le registre de conteneurs Azure abritant votre image conteneur. Le groupe de ressources doit être accessible dans l’ID d’abonnement (ci-dessus). Vous trouverez le nom dans la page [Groupes de ressources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) du portail Azure.

**Nom du registre de conteneurs Azure** : indiquez le nom du [registre de conteneurs Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) contenant votre image conteneur. Le registre de conteneurs doit se trouver dans le groupe de ressources Azure que vous avez fourni. Indiquez uniquement le nom du registre, et non le nom complet du serveur de connexion. Veillez à omettre **azurecr.io** dans le nom. Vous trouverez le nom du registre dans la [page Registres de conteneurs](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) du portail Azure.

**Nom d’utilisateur de l’administrateur pour le registre de conteneurs Azure** : indiquez le [nom d’utilisateur de l’administrateur](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) qui est associé au registre de conteneurs Azure contenant votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Pour obtenir le nom de l’utilisateur administrateur et le mot de passe, définissez la propriété **admin-enabled** sur **True** à l’aide de l’interface de ligne de commande Azure (CLI). Vous pouvez éventuellement définir **Utilisateur administrateur** sur **Activer** dans le portail Azure.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Illustre la boîte de dialogue Mettre à jour le registre de conteneurs.":::

**Mot de passe du registre de conteneurs Azure** : indiquez le mot de passe de l’utilisateur administrateur qui est associé au registre de conteneurs Azure et qui a votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Vous pouvez récupérer le mot de passe à partir du portail Azure en accédant à **Registre de conteneurs** > **Clés d’accès** ou avec Azure CLI à l’aide de la [commande show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Illustration du menu Clé d’accès.":::

**Nom du dépôt au sein du registre de conteneurs Azure**. Indiquez le nom du dépôt Azure Container Registry contenant votre image. Spécifiez le nom du dépôt quand vous envoyez (push) l’image vers le registre. Vous trouverez le nom du dépôt en accédant à [Registre de conteneurs](https://azure.microsoft.com/services/container-registry/) > **Page Dépôts**. Pour plus d’informations, consultez [Afficher les dépôts de registre de conteneurs dans le portail Azure](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> Une fois le nom défini, il ne peut plus être modifié. Utilisez un nom unique pour chaque offre dans votre compte.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Étiquettes d’image pour les nouvelles versions de votre offre

Quand vous publiez une mise à jour, les clients doivent être en mesure de la récupérer automatiquement de la Place de marché Azure. S’ils ne souhaitent pas effectuer de mise à jour, ils doivent être en mesure de rester sur une version spécifique de votre image. Pour ce faire, vous pouvez ajouter de nouvelles étiquettes d’image chaque fois que vous effectuez une mise à jour de l’image.

### <a name="image-tag"></a>Balise d'image

Ce champ doit inclure une étiquette **latest** qui pointe vers la dernière version de votre image sur toutes les plateformes prises en charge. Il doit également comprendre une étiquette de version (par exemple, commençant par xx.xx.xx, où xx est un nombre). Les clients doivent utiliser des [étiquettes de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger.

Toutes les étiquettes de manifeste (sauf l’étiquette « latest ») doivent commencer par X.Y **-** ou X.Y.Z-, où X, Y et Z sont des entiers. Par exemple, si une étiquette **latest** pointe sur 1.0.1-linux-x64, 1.0.1-linux-arm32 et 1.0.1-windows-arm32, ces six étiquettes doivent être ajoutées à ce champ. Pour plus d’informations, consultez [Préparer vos ressources techniques de conteneur Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Pensez à ajouter une étiquette de test à votre image afin de l’identifier plus facilement pendant les tests.

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez l’envoyer pour révision et publication.

Dans le coin supérieur droit du portail, sélectionnez **Vérifier et** **publier**.

Dans la page de révision, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre. Vous ne pouvez pas publier tant que toutes les sections de l’offre ne sont pas marquées comme étant complètes.
  - **Non démarrée** : n’a pas été démarrée et doit être complétée.
  - **Incomplète** : comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations de votre part. Pour obtenir de l’aide, consultez les sections plus haut dans ce document.
  - **Complète** : comprend toutes les données nécessaires sans erreurs. Toutes les sections de l’offre doivent être complètes pour que vous puissiez envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour vous assurer que votre offre est correctement testée. Vous pouvez également fournir des notes supplémentaires utiles pour comprendre votre offre.

Pour envoyer l’offre à des fins de publication, sélectionnez **Publier**.

Nous vous enverrons un e-mail pour vous faire savoir si une préversion de l’offre est disponible pour vérification et approbation.

Pour que votre offre soit publiée pour le public (ou pour un public privé, si elle est privée), accédez à l’Espace partenaires et sélectionnez **Mise en service**.

## <a name="next-step"></a>Étape suivante

- [Mettre à jour une offre existante dans la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
