---
title: Créer une offre de module Azure IoT Edge avec l’Espace partenaires dans la Place de marché Azure
description: Découvrez comment créer, configurer et publier une offre de module IoT Edge dans la Place de marché Azure à l’aide de l’Espace partenaires.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: d69090eb07159c2c188c54499a167f127269df24
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857656"
---
# <a name="create-configure-and-publish-an-iot-edge-module-offer-in-azure-marketplace"></a>Créer, configurer et publier une offre de module IoT Edge dans la Place de marché Azure

Cet article explique comment créer et publier une offre de module IoT (Internet of Things) Edge pour la Place de marché Azure. Avant de commencer, si ce n’est déjà fait, [créez un compte de la Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Vérifiez que votre compte est inscrit dans le programme du marketplace commercial.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page de présentation, sélectionnez **+ Nouvelle offre** > **Module IoT Edge**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
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

La page **Vue d’ensemble de l’offre** affiche une représentation visuelle des étapes nécessaires à la publication de cette offre (à la fois terminées et à venir) et indique la durée d’exécution de chaque étape.

Cette page contient des liens permettant d’effectuer des opérations sur cette offre en fonction de la sélection que vous effectuez. Par exemple :

- Si l’offre est un brouillon : [Supprimer le brouillon de l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Si l’offre est publiée : [Arrêter de vendre l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Si l’offre est disponible en préversion : [Mise en service](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Si vous n’avez pas complété Déconnexion de l’éditeur : [Annuler la publication](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuration de l’offre

Effectuez les étapes suivantes pour configurer votre offre.

### <a name="connect-lead-management"></a>Connecter la gestion des prospects

Lors de la publication de votre offre sur la Place de marché avec l’Espace partenaires, vous pouvez la connecter à votre système de gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit.

1. Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix. L’Espace partenaires prend en charge les systèmes CRM suivants :

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
- PDF [Vue d’ensemble de la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="properties"></a>Propriétés

C’est dans cette page que vous définissez les catégories utilisées pour grouper votre offre selon les Places de marché et les contrats légaux associés.

#### <a name="category"></a>Category

Sélectionnez au minimum une et au maximum cinq catégories. Ces catégories servent à placer votre offre dans les zones de recherche appropriées sur la Place de marché et apparaissent dans la page des détails de votre offre. Dans la description de l’offre, expliquez comment votre offre prend en charge ces catégories. Dans les pages de navigation, tous les modules IoT Edge sont répertoriés dans la catégorie  **Internet des objets > Module IoT Edge** .

#### <a name="legal"></a>Informations juridiques

Vous devez fournir les conditions générales de l’offre. Deux options s'offrent à vous :

- Utilisez le contrat Standard pour la Place de marché commerciale Microsoft.
- Fournissez vos propres conditions générales.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrat Standard pour la Place de marché commerciale Microsoft

Nous proposons un modèle de contrat Standard pour faciliter les transactions dans la Place de marché commerciale. Vous pouvez choisir de proposer votre solution dans le cadre du contrat Standard, que les clients ne doivent vérifier et accepter qu’une seule fois. Il s’agit d’une bonne option si vous ne souhaitez pas créer des conditions générales personnalisées.

Pour en savoir plus sur le contrat Standard, consultez [Contrat standard pour la Place de marché commerciale de Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Vous pouvez également télécharger le fichier PDF [Contrat Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assurez-vous que le bloqueur de fenêtres publicitaires est désactivé).

Pour utiliser le contrat Standard, cochez la case **Utiliser le Contrat Standard pour la Place de marché commerciale Microsoft**, puis cliquez sur **Accepter**.

> [!NOTE]
> Après avoir publié une offre à l’aide du contrat Standard pour la Place de marché commerciale de Microsoft, vous ne pouvez pas utiliser vos propres conditions générales. Proposez votre solution avec le contrat Standard ou dans le cadre de vos propres conditions générales.

![Illustre l’utilisation de la case à cocher Utiliser le Contrat Standard pour la Place de marché commerciale Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Vos propres conditions générales

Pour fournir vos propres conditions générales, entrez-les dans la zone **Conditions générales**. Vous pouvez entrer un nombre illimité de caractères de texte dans cette zone. Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Annonce de l’offre.

## <a name="offer-listing"></a>Annonce de l’offre

Ici, vous allez définir les détails de l’offre qui s’affichent dans la Place de marché. Cela comprend le nom, la description, les images, etc. de l’offre. Veillez à suivre les stratégies détaillées dans la page de stratégie Microsoft lors de la configuration de cette offre.

> [!NOTE]
> Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par l’expression « This application is available only in [langue non anglaise] ». Il est également possible de fournir un lien utile pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de l’annonce de l’offre.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche comme titre de votre offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright).
- Ne peut pas compter plus de 50 caractères.
- Ne peut pas inclure d’emojis.

### <a name="search-results-summary"></a>Résumé des résultats de recherche

Fournissez une brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="long-summary"></a>Résumé long

Fournissez une description plus détaillée de votre offre. Ce champ peut comporter jusqu’à 256 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre, jusqu’à 3 000 caractères. S’affichera pour les clients dans la vue d’ensemble de la Place de marché.

Incluez un ou plusieurs des éléments suivants dans votre description :

- La valeur et les avantages principaux de votre offre
- Catégorie ou associations de secteurs, ou les deux
- Opportunités d’achats dans l’application
- Toutes les informations requises

La partie inférieure de la description des offres de module IoT Edge doit inclure un paragraphe sur les conditions matérielles minimales requises. Par exemple :

*Conditions matérielles minimales requises : Système d’exploitation Linux x64 et arm32, 1 Go de RAM, 500 Mo de stockage*

Voici quelques conseils pour rédiger votre description :

- Décrivez clairement la valeur de votre offre dans les premières phrases de votre description. Incorporez les éléments suivants :
    - Description de l’offre.
    - Le type d’utilisateur qui tire parti de l’offre.
    - Les besoins ou problèmes auxquels l’offre répond.
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application offre.
- Essayez d’utiliser le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés.

Pour que la **Description** de votre offre soit plus attrayante, utilisez l’éditeur de texte enrichi pour mettre en forme votre description. L’éditeur de texte enrichi vous permet d’ajouter des chiffres, des puces, des caractères gras/en italique et des mises en retrait pour rendre votre description plus lisible.

:::image type="content" source="media/text-editor2.png" alt-text="Illustre l’éditeur de texte enrichi." border="false":::

- Pour changer la mise en forme de votre contenu, mettez en surbrillance le texte que vous souhaitez mettre en forme, puis sélectionnez un style de texte, comme indiqué dans la capture d’écran suivante :

     :::image type="content" source="media/text-editor3.png" alt-text="Illustre le contrôle de style de texte dans l’éditeur de texte enrichi." border="false":::

- Pour ajouter une liste à puces ou une liste numérotée au texte, utilisez les options illustrées dans la capture d’écran suivante :
  
    :::image type="content" source="media/text-editor4.png" alt-text="Illustre les contrôles de liste à puces et de liste numérotée dans l’éditeur de texte enrichi." border="false":::

- Pour ajouter ou supprimer une mise en retrait du texte, utilisez les options illustrées dans la capture d’écran suivante :

    :::image type="content" source="media/text-editor5.png" alt-text="Illustre les contrôles de mise en retrait du texte dans l’éditeur de texte enrichi." border="false":::

#### <a name="privacy-policy-url"></a>URL de la politique de confidentialité

Entrez l’adresse web de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre offre respecte la loi et les réglementations en matière de confidentialité. Vous devez également veiller à publier une politique de confidentialité conforme sur votre site web.

#### <a name="useful-links"></a>Liens utiles

Fournissez des documents en ligne complémentaires à propos de votre offre. Vous pouvez ajouter jusqu’à 25 liens. Pour ajouter un lien, sélectionnez **+ Ajouter un lien**, puis complétez les champs suivants :

- **Titre** : les clients verront le titre dans la page de détails de votre offre.
- **Lien (URL)**  : entrez un lien pour que les clients voient votre document en ligne. Le lien doit démarrer par http:// ou https://.

Veillez à ajouter au moins un lien vers votre documentation et un lien vers les appareils IoT Edge compatibles du  [catalogue d’appareils Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informations de contact

Entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact du support** et d’un **contact d’ingénierie**. Ces informations ne sont pas présentées aux clients. Elles sont disponibles pour Microsoft et peuvent être fournies aux partenaires fournisseurs de solutions Cloud (CSP).

- Contact du support (obligatoire) : Pour toute question de support générale.
- Contact en ingénierie (obligatoire) : Pour les questions techniques et les problèmes de certification.
- Contact du programme du fournisseur de solutions cloud (facultatif) : Pour toute question du revendeur relative au programme CSP.

Dans la section **Contact du support**, fournissez l’adresse web du **site web de support** où les partenaires peuvent trouver un support pour votre offre selon que celle-ci est disponible dans Azure international, Azure Government ou les deux.

Dans la section **Contact du programme du fournisseur de solutions Cloud**, fournissez le lien (**Ressources Marketing du programme CSP**) où les partenaires CSP peuvent trouver des ressources marketing pour votre offre.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

Pour en savoir plus sur la création d’annonces d’offres, consultez [Bonnes pratiques pour le référencement des offres](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

### <a name="marketplace-images"></a>Images de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format .png. Les images floues seront rejetées.

>[!Note]
>Si vous rencontrez un problème lors du chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="store-logos"></a>Stocker des logos

Fournissez des fichiers .png du logo de votre offre dans chacune des quatre tailles en pixels suivantes :

- **Petit (48 x 48)**
- **Moyen (90 x 90)**
- **Grand (216 x 216)**
- **Large (255 x 115)**

Les quatre logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

#### <a name="screenshots-optional"></a>Captures d’écran (facultatif)

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chacune doit avoir une taille de 1280 x 720 pixels et être au format .png.

#### <a name="videos-optional"></a>Vidéos (facultatif)

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Entrez le nom de la vidéo, son adresse web et une image miniature .png de 1280 x 720 pixels de la vidéo.

#### <a name="offer-examples"></a>Exemples d’offre

Les exemples suivants montrent où sont positionnés les champs de l’annonce de l’offre au sein de celle-ci.

Cette capture d’écran montre la page **Description de l’offre** dans la Place de marché Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Illustre la page Description de l’offre dans la Place de marché Azure.":::

Cette capture d’écran montre les résultats de la recherche dans la Place de marché Azure :

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Illustre les résultats de la recherche dans la Place de marché Azure.":::

Cette capture d’écran montre la page **Description de l’offre** dans le portail Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Illustre la page Description de l’offre dans le portail Azure.":::

Cette capture d’écran montre les résultats de la recherche dans le portail Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Illustre la page Description de l’offre dans le portail Azure.":::

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Préversion.

## <a name="preview"></a>PRÉVERSION

Sous l’onglet **Préversion**, vous pouvez choisir de restreindre le **public de préversion** en vue de valider votre offre avant de la publier plus largement en ligne sur la Place de marché.

> [!IMPORTANT]
> Une fois que vous avez affiché votre offre en préversion, vous devez sélectionner **Mise en service** afin de publier votre offre pour le public.

Spécifiez votre public de préversion à l’aide d’ID (GUID) d’abonnements Azure ainsi que d’une description facultative de chacun. Aucun de ces champs ne peut être vu par les clients.

> [!NOTE]
> Vous pouvez trouver votre ID d’abonnement Azure dans la page Abonnements du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément (jusqu’à 10), soit en chargeant un fichier CSV (jusqu’à 100). En ajoutant ces ID d’abonnement, vous définissez qui peut afficher un aperçu de votre offre avant qu’elle ne soit publiée en ligne. Si votre offre est déjà publiée, vous pouvez définir un public de préversion afin de tester des changements ou des mises à jour futurs.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de **préversion** peut voir et vérifier tous les plans d’offre avant qu’ils ne soient publiés sur la Place de marché, y compris ceux destinés à n’être publiés que pour un public **privé** (défini sous l’onglet Disponibilité).

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Vue d’ensemble du plan.

### <a name="plan-overview"></a>Vue d’ensemble du plan

Cet onglet vous permet de proposer différentes options de plan dans la même offre au sein de l’Espace partenaires. Ces plans étaient auparavant appelés références SKU (Stock Keeping Units). Les plans peuvent varier selon les clouds disponibles, tels que les clouds mondiaux et les clouds gouvernementaux, et l’image qu’ils référencent. Pour référencer votre offre dans la Place de marché, vous devez configurer au moins un plan.

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

Dans la zone **ID du plan**, créez un ID de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères.

Dans la zone **Nom du plan**, entrez un nom pour ce plan. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Créez un nom unique pour chaque plan dans cette offre. Par exemple, vous pouvez utiliser le nom d’offre **Windows Server** avec les plans **Windows Server 2016** et **Windows Server 2019**.

> [!NOTE]
> L’ID de plan ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Sélectionnez **Create** (Créer).

### <a name="plan-setup"></a>Configuration du plan

Cet onglet vous permet de configurer les clouds dans lesquels le plan est disponible. Les choix effectués sous cet onglet déterminent les champs affichés sous les autres onglets.

#### <a name="cloud-availability"></a>Disponibilité dans le cloud

Votre plan doit être disponible dans au moins un cloud à l’aide d’IoT Hub.

Sélectionnez l’option **Azure international** pour que votre plan puisse être utilisé par les clients de toutes les régions Azure du monde qui recourent à la Place de marché. Pour plus d’informations, consultez [Prise en charge de la disponibilité géographique et des devises](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Sélectionnez l’option [Cloud Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) pour que votre solution y apparaisse. Il s’agit d’un cloud communautaire pour le secteur public, accessible à des clients d’agences gouvernementales fédérales, étatiques, locales ou tribales des États-Unis ainsi qu’à des partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires pour le cloud de cette communauté. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement). Avant d’effectuer une [publication](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) sur Azure Government, testez et vérifiez votre solution dans cette zone, car les résultats peuvent être différents. Pour tester votre solution dans un environnement de préproduction, demandez un compte d’essai depuis la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans un cloud spécifique, vous ne pouvez pas supprimer ce cloud.

#### <a name="azure-government-cloud-certifications"></a>Certifications du cloud Azure Government

Cette option est visible uniquement si **Cloud Azure Government** est sélectionné sous **Disponibilité dans le cloud**.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens vers les détails de vos certifications. Il peut s’agir de liens vers vos annonces sur le programme directement ou vers votre propre site web. Ces liens sont visibles uniquement par les clients Azure Government.

## <a name="plan-listing"></a>Liste des plans

Cet onglet affiche des informations spécifiques pour chaque plan au sein de la même offre.

### <a name="plan-name"></a>Nom du plan

Ce champ est pré-rempli avec le nom que vous avez donné à votre plan quand vous l’avez créé. Vous pouvez changer ce nom, si nécessaire. Il peut comporter jusqu’à 50 caractères. Ce nom s’affiche comme titre de ce plan dans la Place de marché Azure et dans le portail Azure. Il est utilisé comme nom de module par défaut une fois que le plan est utilisable.

### <a name="plan-summary"></a>Résumé du plan

Fournissez un bref résumé de votre plan (pas de l’offre). Ce résumé s’affiche dans les résultats de la recherche de la Place de marché Azure et peut contenir jusqu’à 100 caractères.

### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan unique ainsi que les différences entre les plans de votre offre. Ne décrivez pas l’offre, mais simplement le plan. Cette description apparaît dans la Place de marché Azure et dans le portail Azure dans la page Description de l’offre. Son contenu peut être le même que celui que vous avez fourni dans le résumé du plan et comporter jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer le brouillon** après avoir renseigné ces champs.

#### <a name="plan-examples"></a>Exemples de plans

Les exemples suivants montrent où sont positionnés les champs de la liste des plans dans les différents affichages.

Voici les champs dans la Place de marché Azure lors de l’affichage des détails du plan :

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Illustre les champs que vous voyez quand vous affichez les détails du plan dans la Place de marché Azure.":::

Voici les détails du plan sur le portail Azure :

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Illustre les détails du plan sur le portail Azure.":::

## <a name="availability"></a>Disponibilité

Si vous souhaitez masquer votre offre publiée afin que les clients ne puissent pas l’explorer, la parcourir ou l’acheter sur la Place de marché, cochez la case **Masquer le plan** sous l’onglet Disponibilité.

Ce champ est couramment utilisé dans les cas suivants :

- L’offre est destinée à être utilisée uniquement indirectement quand elle est référencée par le biais d’une autre application.
- L’offre ne doit pas être achetée individuellement.
- Le plan a été utilisé pour des tests initiaux et n’est plus pertinent.
- Le plan a été utilisé pour des offres temporaires ou saisonnières et ne doit plus être proposé.

## <a name="technical-configuration"></a>Configuration technique

Le type d’offre **Module IoT Edge** est un type spécifique de conteneur qui s’exécute sur un appareil IoT Edge. Sous l’onglet **Configuration technique**, vous fournissez des informations de référence pour le dépôt d’images conteneur à l’intérieur du [registre de conteneurs Azure](https://azure.microsoft.com/services/container-registry/) ainsi que des paramètres de configuration qui permettent aux clients d’utiliser le module facilement.

Une fois l’offre publiée, votre image conteneur IoT Edge est copiée sur la Place de marché Azure dans un registre de conteneurs public spécifique. Toutes les demandes des utilisateurs Azure pour utiliser votre module sont traitées à partir du registre de conteneurs publics de la Place de marché Azure, et non pas depuis votre registre de conteneurs privé.

Vous pouvez cibler plusieurs plateformes et fournir plusieurs versions de l’image conteneur du module par le biais d’étiquettes. Pour en apprendre davantage sur les étiquettes et le versioning, consultez [Préparer vos ressources techniques de module IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset).

### <a name="image-repository-details"></a>Détails du référentiel d’images

Vous devez fournir les informations suivantes sous l’onglet **Détails du référentiel d’images**.

**Sélectionner la source de l’image** : sélectionnez l’option **Registre de conteneurs Azure**.

**ID d’abonnement Azure** : indiquez l’ID d’abonnement dans lequel l’utilisation des ressources est signalée et les services sont facturés pour le registre de conteneurs Azure qui contient votre image conteneur. Vous pouvez trouver cet ID dans la [page Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

**Nom du groupe de ressources Azure** : indiquez le nom du [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) qui contient le registre de conteneurs Azure abritant votre image conteneur. Le groupe de ressources doit être accessible dans l’ID d’abonnement (ci-dessus). Vous trouverez le nom dans la page [Groupes de ressources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) du portail Azure.

**Nom du registre de conteneurs Azure** : indiquez le nom du [registre de conteneurs Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) contenant votre image conteneur. Le registre de conteneurs doit être présent dans le groupe de ressources Azure que vous avez fourni. Indiquez uniquement le nom du registre, et non le nom complet du serveur de connexion. Veillez à omettre **azurecr.io** dans le nom. Vous trouverez le nom du registre dans la [page Registres de conteneurs](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) du portail Azure.

**Nom d’utilisateur administrateur pour le registre de conteneurs Azure** : indiquez le [nom de l’utilisateur administrateur](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) associé au registre de conteneurs Azure contenant votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Pour obtenir le nom de l’utilisateur administrateur et le mot de passe, définissez la propriété **admin-enabled** sur **True** à l’aide de l’interface de ligne de commande Azure (CLI). Vous pouvez éventuellement définir **Utilisateur administrateur** sur **Activer** dans le portail Azure.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Illustre la boîte de dialogue Mettre à jour le registre de conteneurs.":::

**Mot de passe pour le registre de conteneurs Azure** : indiquez le mot de passe de l’utilisateur administrateur qui est associé au registre de conteneurs Azure et qui a votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Vous pouvez récupérer le mot de passe à partir du portail Azure en accédant à **Registre de conteneurs** > **Clés d’accès** ou avec Azure CLI à l’aide de la [commande show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Illustre l’écran des clés d’accès dans le portail Azure.":::

**Nom du dépôt au sein du registre de conteneurs Azure**. Indiquez le nom du dépôt Azure Container Registry contenant votre image. Vous spécifiez le nom du dépôt quand vous envoyez (push) l’image vers le registre. Vous trouverez le nom du dépôt en accédant à [Registre de conteneurs](https://azure.microsoft.com/services/container-registry/) > **Page Dépôts**. Pour plus d’informations, consultez [Afficher les dépôts de registre de conteneurs dans le portail Azure](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Notez qu’une fois le nom défini, il ne peut pas être changé. Utilisez un nom unique pour chaque offre dans votre compte.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Étiquettes d’image pour les nouvelles versions de votre offre

Quand vous publiez une mise à jour, les clients doivent être en mesure de la récupérer automatiquement de la Place de marché Azure. S’ils ne souhaitent pas effectuer de mise à jour, ils doivent être en mesure de rester sur une version spécifique de votre image. Pour ce faire, vous pouvez ajouter de nouvelles étiquettes d’image chaque fois que vous effectuez une mise à jour de l’image.

**Étiquette d’image**. Ce champ doit inclure une étiquette **latest** qui pointe vers la dernière version de votre image sur toutes les plateformes prises en charge. Il doit également comprendre une étiquette de version (par exemple, commençant par xx.xx.xx, où xx est un nombre). Les clients doivent utiliser des [étiquettes de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger. Toutes les étiquettes de manifeste (sauf l’étiquette « latest ») doivent commencer par X.Y- ou X.Y.Z, où X, Y et Z sont des entiers. Par exemple, si une étiquette « latest » pointe sur 1.0.1-linux-x64, 1.0.1-linux-arm32 et 1.0.1-windows-arm32, ces six étiquettes doivent être ajoutées à ce champ. Pour plus d’informations sur les étiquettes et le versioning, consultez [Préparer vos ressources techniques de module IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).

### <a name="default-deployment-settings-optional"></a>Paramètres de déploiement par défaut (facultatif)

Définissez les paramètres les plus courants pour déployer votre module IoT Edge. Optimisez les déploiements des clients en les autorisant à lancer votre module IoT Edge prêt à l’emploi avec ces paramètres par défaut.

**Itinéraires par défaut**. Le hub IoT Edge gère la communication entre les modules, le hub IoT et les appareils. Vous pouvez définir des routes pour l’entrée et la sortie de données entre les modules et le hub IoT afin d’envoyer des messages au bon destinataire sans avoir recours à d’autres services pour traiter les messages ou écrire du code supplémentaire. Les routes sont construites à l’aide de paires nom/valeur. Vous pouvez définir jusqu’à cinq noms de routes par défaut, chacun d’une longueur maximale de 512 caractères.

Veillez à utiliser la [syntaxe de route](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) correcte dans la valeur de la route (généralement définie sous la forme FROM/message/* INTO $upstream). Cela signifie que tous les messages envoyés par les modules sont dirigés vers votre hub IoT. Pour faire référence à votre module, utilisez son nom de module par défaut, qui est votre **Nom d’offre** sans espaces ou caractères spéciaux. Pour faire référence à d’autres modules qui ne sont pas encore connus, utilisez la convention <NOM_MODULE_SOURCE> pour informer vos clients qu’ils doivent mettre à jour ces informations. Pour plus d’informations sur les routes IoT Edge, consultez [Déclarer des routes](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes).

Par exemple, si le module ContosoModule écoute les entrées sur ContosoInput et les données de sortie au niveau de ContosoOutput, il est judicieux de définir les deux routes par défaut suivantes :

- Nom 1 : ToContosoModule
- Valeur 1 : FROM /messages/modules/<NOM_MODULE_SOURCE>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nom 2 : FromContosoModuleToCloud
- Valeur 2 : FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Propriétés souhaitées du jumeau de module par défaut**. Un jumeau de module est un document JSON dans le hub IoT qui stocke les informations d’état d’une instance de module, notamment les propriétés souhaitées. Les propriétés souhaitées sont utilisées en même temps que les propriétés signalées pour synchroniser une configuration ou une condition de module. Le back-end de solution peut définir les propriétés souhaitées et le module peut les lire. Le module peut également recevoir des notifications sur les changements des propriétés souhaitées. Les propriétés souhaitées sont créées à l’aide de cinq paires nom/valeur, et chaque valeur par défaut doit contenir moins de 512 caractères. Vous pouvez définir jusqu’à cinq noms/valeurs de propriétés souhaitées du jumeau. Les valeurs des propriétés souhaitées du jumeau doivent être un code JSON valide, sans séquence d’échappement ni tableaux, ayant une hiérarchie imbriquée maximale de quatre niveaux. Dans un scénario où un paramètre requis pour une valeur par défaut n’est pas judicieux (par exemple, l’adresse IP du serveur d’un client), vous pouvez ajouter un paramètre comme valeur par défaut. Pour plus d’informations sur les propriétés souhaitées du jumeau, consultez [Définir ou mettre à jour les propriétés souhaitées](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

Par exemple, si un module prend en charge une fréquence d’actualisation configurable de manière dynamique à l’aide de ses propriétés souhaitées du jumeau, il semble judicieux de définir la propriété souhaitée du jumeau par défaut suivante :

- Nom 1 : RefreshRate
- Valeur 1 : 60

**Variables d’environnement par défaut**. Les variables d’environnement fournissent des informations complémentaires à un module, qui aident le processus de configuration. Les variables d’environnement sont créées à l’aide de paires nom/valeur. Chaque nom et valeur de variable d’environnement par défaut doit contenir moins de 512 caractères, et vous pouvez définir jusqu’à cinq paires. Quand un paramètre requis pour une valeur par défaut n’est pas judicieux (par exemple, l’adresse IP du serveur d’un client), vous pouvez ajouter un paramètre comme valeur par défaut.

Par exemple, si un module a besoin d’accepter des conditions d’utilisation avant d’être démarré, vous pouvez définir la variable d’environnement suivante :

- Nom 1 : ACCEPT_EULA
- Valeur 1: O

**Options de création de conteneur par défaut**. Les options de création de conteneur dirigent la création du conteneur Docker du module IoT Edge. IoT Edge prend en charge les options de création de conteneur de l’API du moteur Docker. Vous trouverez toutes les options dans la page [List containers](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList). Le champ des options de création doit être un code JSON valide, sans séquence d’échappement et contenant moins de 512 caractères.

Par exemple, si un module nécessite une liaison de port, définissez les options de création suivantes :

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez l’envoyer pour révision et publication.

Dans le coin supérieur droit du portail, sélectionnez **Vérifier et publier**.

Dans la page de révision, vous pouvez voir l’état de la publication :

- Consultez l’état d’achèvement de chaque section de l’offre. Vous ne pouvez pas publier tant que toutes les sections de l’offre ne sont pas marquées comme étant complètes.
    - **Non démarrée** : la section n’a pas été démarrée et doit être complétée.
    - **Incomplète** : la section comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations de votre part. Pour obtenir de l’aide, consultez les sections plus haut dans ce document.
    - **Complète** : la section contient toutes les données nécessaires et ne comporte aucune erreur. Toutes les sections de l’offre doivent être complètes pour que vous puissiez envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour vous assurer que votre offre est correctement testée. Vous pouvez également fournir des notes supplémentaires utiles pour comprendre votre offre.

Pour envoyer l’offre à des fins de publication, sélectionnez **Publier**.

Nous vous enverrons un e-mail pour vous faire savoir si une préversion de l’offre est disponible pour vérification et approbation. Pour que votre offre soit publiée pour le public (ou pour un public privé, si elle est privée), accédez à l’Espace partenaires et sélectionnez **Mise en service**.

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)