---
title: Cortana intelligence | Microsoft Docs
description: Publication d’une offre Cortana Intelligence.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: a6357640ceeb7c5f1bde4eea85aa5265990935f4
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157154"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Publier une offre Cortana Intelligence à l’aide du portail Cloud Partner

Cet article décrit comment publier une offre Cortana Intelligence à l’aide du portail Cloud Partner.

## <a name="prerequisites"></a>Prérequis

Le portail Cloud Partner prend en charge l’accès au portail en fonction des rôles, ce qui permet aux contributeurs de travailler ensemble à la publication d’une offre. Pour plus d’informations, consultez [Gérer les utilisateurs du portail Cloud](./cloud-partner-portal-manage-users.md).

Avant de pouvoir publier une offre pour le compte d’un compte de publication, une des personnes disposant du rôle \"propriétaire\" doit s’engager à respecter les [conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [déclaration de confidentialité de Microsoft](https://www.microsoft.com/privacystatement/default.aspx) et le [contrat du programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Pour commencer à créer une offre Cortana Inteligence

Une fois toutes les conditions préalables réunies, vous êtes prêt à commencer à créer votre offre Cortana Intelligence.

1.  Connectez-vous au [portail Cloud Partner](http://cloudpartner.azure.com/).
2.  Dans la barre de navigation de gauche, sélectionnez **+Nouvelle offre**.
3. Sélectionnez **Cortana Intelligence**.
4. Sélectionnez l’onglet **Éditeur** dans l’affichage **Nouvelle offre** vue pour configurer les options suivantes :
    -   Paramètres de l’offre
    -   Informations techniques
    -   Informations sur les vitrines
    -   Contacts

    Chacune de ces options précédentes affiche un formulaire que vous devez remplir.  Les champs obligatoires de chaque formulaire sont indiqués par un astérisque rouge (\*).

## <a name="to-configure-offer-settings"></a>Pour configurer les paramètres de l’offre

Les paramètres de l’offre contiennent les informations de base sur l’offre, par exemple l’ID de l’offre, l’ID de l’éditeur et le nom de l’offre.

### <a name="offer-id"></a>ID de l’offre

Il s’agit d’un identificateur unique pour l’offre au sein d’un profil d’éditeur.
Cet ID est visible dans les URL de produit. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comporter plus de 50 caractères. 
>[!Note]
>Ce champ est verrouillé une fois que l’offre est en ligne.

**Exemple :**

Si le serveur de publication **contoso** crée une offre avec l’ID *Exemple-Cortana Intelligence*, il s’affichera dans AppSource sous « https://appsource.microsoft.com/marketplace/apps/**contoso**.*Exemple-Cortana Intelligence*?tab=Overview ».

### <a name="publisher-id"></a>ID de l’éditeur

Liste déroulante du profil d’éditeur. Utilisez cette liste déroulante pour choisir le profil d’éditeur sous lequel vous voulez publier cette offre.

>[!Note]
>Ce champ est verrouillé une fois que l’offre est en ligne.

### <a name="name"></a>NOM

Nom d’affichage de votre offre. Ce nom s’affiche dans [AppSource](https://appsource.microsoft.com). Il ne peut pas comprendre plus de 50 caractères.

Lorsque vous avez fourni toutes les informations nécessaires dans les paramètres de l’offre, sélectionnez **Enregistrer** pour passer aux informations techniques de l’offre. option.

## <a name="to-configure-technical-info"></a>Pour configurer les informations techniques

Utilisez cet affichage pour fournir les informations techniques qui seront affichées dans la page de votre offre. Les champs suivants s’appliquent à cet affichage.

### <a name="partner-mpn-id"></a>ID partenaire MPN

Si vous êtes partenaire Microsoft, accédez au [site web des partenaires](https://partners.microsoft.com/) et connectez-vous pour obtenir votre ID d’organisation partenaire. Entrez cet ID comme **ID de partenaire MPN**.

### <a name="analytics-components-used"></a>Composants Analytics utilisés

Sélectionnez tous les composants qu’utilise votre offre. Sélectionnez au moins 1 composant. Sélectionnez **Microsoft R** uniquement si vous utilisez l’une des licences MRS des services SQL 2016 R, HDInsight Premium ou MRS sur machine virtuelle.

### <a name="additional-components-used"></a>Autres composants utilisés

Sélectionnez tous les autres composants qu’utilise votre solution.

### <a name="customer-name-using-solution"></a>Nom du client qui utilise la solution

Indiquez le nom du client qui utilise cette solution en production. 

>[!Note]
>Cette information n’est pas publiée sur AppSource. Elle est uniquement utilisée pour l’évaluation de la solution.

### <a name="azure-consumption-requirement-met"></a>Conditions de consommation Azure respectées ?

Indiquez si la solution génère ou non au moins 1000 $ par mois par client de composants de la Suite Azure, ou si la solution utilise Microsoft R.

>[!Note]
>Cette information n’est pas publiée sur AppSource. Elle est uniquement utilisée pour l’évaluation de la solution.

### <a name="demo-video-url"></a>URL de la vidéo de démonstration

Indiquez l’URL de la démonstration vidéo de la solution/application que votre équipe de vente peut présenter aux clients. 

>[!Note]
>Cette information n’est pas publiée sur AppSource. Elle est uniquement utilisée pour l’évaluation de la solution.

#### <a name="demo-video-guidelines"></a>Instructions concernant la vidéo de démonstration

- La durée de la vidéo doit être inférieure à 15 minutes.
- La vidéo doit modifier au minimum l’enregistrement du fonctionnement de la solution. 
- La vidéo met en évidence les principaux aspects des fonctionnalités orientées utilisateur et se concentre sur l’intégration avancée de l’analytique. 
- Les vidéos de démonstration **ne sont pas** disponibles publiquement pour les clients, mais doivent refléter la façon dont la solution *s’afficherait* à un client potentiel. Par conséquent, elles doivent être scriptées et reproductibles.
- Utilisez n’importe quel outil d’enregistrement d’écran capable d’exporter un format vidéo standard (par exemple, MPEG) pour créer votre vidéo. 

Les instructions suivantes vous expliquent comment créer une vidéo avec Skype Entreprise. 

1. [Commencer une réunion](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Partager votre bureau](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Commencer l’enregistrement](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. À la fin de l’enregistrement, [utiliser le gestionnaire d’enregistrements pour publier votre enregistrement](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Chargez la vidéo enregistrée sur un service qui vous permet de générer une URL partagée. Par exemple, un [lien Invité dans OneDrive ou SharePoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Régions prises en charge

Sélectionnez toutes les régions que prend en charge votre solution. Sélectionnez au moins 1 région.

### <a name="power-bi-desktop-file-pbix"></a>Fichier Power BI Desktop (.pbix)

Le cas échéant, chargez un fichier .pbix. Assurez-vous que les données sont importées dans le fichier, et non référencées en externe. Nous créerons le rapport incorporé pour vous.

### <a name="solution-architecture"></a>Architecture de solution

Chargez un document qui présente en détail l’architecture de votre solution. Pour obtenir des instructions sur le chargement d’un diagramme d’architecture de solution, voir le [modèle de flux de travail de solution analytique avancée](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Cette information n’est pas publiée sur AppSource. Elle est uniquement utilisée pour l’évaluation de la solution.

### <a name="select-segments"></a>Sélectionner des segments

Sélectionnez tous les segments relatifs au secteur. Si votre application correspond à un segment retiré de la liste, entrez le nom du segment dans le champ **Autres**. Vous êtes limité à trois mots pour le segment.

### <a name="select-business-processes"></a>Sélectionner des processus métier

Sélectionnez les processus métier qui décrivent le mieux votre solution. Si votre application correspond à un processus retiré de la liste, entrez le nom du processus dans le champ **Autres**. Vous êtes limité à trois mots pour le processus.

### <a name="trial-info"></a>Informations d’évaluation

-   **URL de la version d'évaluation SaaS :** entrez l’URL de l’expérience d’évaluation de votre application.
-   **URL de la version d'évaluation :** entrez l’URL de l’expérience d’évaluation de votre application.

Pour plus d’informations sur les versions d’évaluation, consultez **Type d’application** dans la section suivante de cet article.

Lorsque vous avez fourni toutes les informations nécessaires dans les informations techniques, sélectionnez **Enregistrer** pour passer à la partie vitrine de l’offre. 

## <a name="to-configure-storefront-details"></a>Pour configurer les détails de la vitrine

### <a name="offer-summary"></a>Résumé de l’offre

Résumé de la proposition de valeur de votre offre. Il figurera sur la page de recherche de votre offre. La longueur maximale de ce résumé est de 100 caractères.

### <a name="offer-description"></a>Description de l’offre

Description qui figurera sur la page des détails de votre application.
La longueur maximale de cette description est de 1300 caractères.

Notez que ce champ accepte le contenu html, avec des balises comme \<p\>, \<h1\>, \<h2\>, \<li\>, etc., ce qui vous permet de rendre le contenu beaucoup plus présentable. L’équipe du portail de publication ajoutera prochainement une fonctionnalité qui permettra à l’utilisateur d’afficher un aperçu des détails de sa vitrine de manière itérative, pour un contenu plus présentable. En attendant, vous pouvez utiliser un outil HTML en ligne et en temps réel tel que [http://htmledit.squarefree.com](http://htmledit.squarefree.com/) pour afficher un aperçu de votre description.

Pour la description, nous suggérons de décomposer le texte en sous-sections en fonction des propositions de valeur, chaque sous-section étant mise en avant grâce à un sous-titre. Les visiteurs jettent généralement un œil au champ « Résumé de l’offre » et aux sous-titres pour se faire une idée des objectifs de l’application et savoir en quelques secondes pourquoi ils devraient utiliser l’application. Par conséquent, il est important de retenir l’attention de l’utilisateur et de lui donner une raison de découvrir les spécificités de l’application.

#### <a name="partner-examples"></a>Exemples de partenaires

- [Optimisation des stocks de Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personnalisation de la vente au détail de Plexure](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Services citoyens d’AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Secteurs d’activité

Sélectionnez le secteur d’activité auquel s’adresse essentiellement votre application. Si votre application est liée à plusieurs secteurs d’activité, laissez ce champ vide.

### <a name="categories"></a>Catégories

Sélectionnez les catégories qui se rapportent à votre application. Sélectionnez un maximum de deux catégories.

### <a name="app-type"></a>Type d’application

Sélectionnez le type de version d’évaluation que votre application prendra en charge sur AppSource. Choisissez l’une des versions d’évaluation suivantes :
- **Gratuit** signifie que votre application est gratuite.
- **Version d’évaluation** signifie que les clients peuvent l’essayer votre pendant une période donnée.
- **Demande de version d’évaluation** signifie que les clients peuvent demander une version d’évaluation de l’application.

Les partenaires peuvent proposer deux types d’évaluation sur AppSource.

- L’option **version d’évaluation**, également appelée **essai par le client** peut être un des types suivants : 
    - Version d’essai SaaS
        - Le client peut accéder à une URL fournie par vous ou votre partenaire. Le client passe par un SSO AAD fédéré pour accéder à la version d’évaluation pendant une durée limitée.
        - Il s’agit d’une application SaaS avec une architecture mutualisée qui isole les données de configuration et les données utilisateur de celles des autres utilisateurs. Ou bien, cette expérience contient uniquement un jeu de données qui utilise des opérations en lecture seule.

        **Exemples :**

        - [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Cette application propose une expérience organisée, avec des chemins d’accès définis vers un ensemble de types d’utilisateurs.)

     - Version d’évaluation
        - Votre solution ou un sous-ensemble de votre solution (ou celle de votre partenaire) peut être placée dans un package avec des modèles Azure Resource Manager qu’AppSource peut instancier. AppSource peut gérer l’application dans un abonnement partenaire avec durée limitée et gérer des instances à chaud et à froid, etc.
        - Nous pouvons fournir des modèles et des exemples de code pour vous aider si vous choisissez cette option.

        **Exemples :**

        - [Optimisation des stocks de Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- L’option **Demande de version d’évaluation** (**évaluation menée par le partenaire**) nécessite que les clients remplissent un formulaire d’informations de contact pour le suivi. Le partenaire suit le contact et propose une démonstration ou une version d’évaluation de l’application. Pour plus d’informations, regardez la vidéo présentant la [procédure pas-à-pas d’expérience d’évaluation d’AppSource](https://aka.ms/trialexperienceforwebapps).

>[!Note]
>Les données indiquent que les **essais par les clients** présentent un meilleur potentiel de génération de prospects par rapport aux **versions d’évaluation menées par le partenaire**.


### <a name="help-link-for-your-app"></a>Lien d’aide pour votre application

Spécifiez l’URL d’une page qui contient des informations d’aide pour votre application.

### <a name="supported-countriesregions"></a>Pays/régions pris en charge

Ce champ détermine les pays/régions où votre offre sera disponible en version d’évaluation.

![Pays/régions pris en charge](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Langues prises en charge

Sélectionnez les langues prises en charge par votre application. Si votre application prend en charge des langues qui ne sont pas dans cette liste, publiez votre offre et envoyez-nous un message à <appsource@microsoft.com> nous en informer.

### <a name="app-version"></a>Version de l’application

Entrez le numéro de version de votre application.

### <a name="products-your-app-works-with"></a>Produits utilisés par votre application

Liste des produits spécifiques avec lesquels votre application fonctionne. Vous pouvez indiquer 3 produits au maximum. Pour indiquer un produit, sélectionnez le **signe plus** (à côté de nouveau) et un champ de texte ouvert se crée. Entrez le nom d’un produit avec lequel votre application fonctionne.

### <a name="hide-key"></a>Masquer la clé

Il s’agit d’une clé associée à l’URL d’aperçu de l’offre et qui permet de masquer l’affichage public de l’offre. Il ne s’agit pas d’un mot de passe. Vous pouvez entrer n’importe quelle chaîne de caractères alphanumériques.

### <a name="offer-logo-small"></a>Logo de l’offre (petit)

Ce logo figure sur la page de recherche de votre application. Le format d’image png est le seul format autorisé. La taille de l’image est de 48 x 48 pixels.

### <a name="offer-logo-large"></a>Logo de l’offre (grand)

Ce logo figure sur la page de détail de votre application. Le format d’image png est le seul format autorisé. La taille de l’image est de 48 x 48 pixels.

### <a name="video"></a>Vidéo

Vous pouvez charger un maximum de quatre vidéos. Pour chaque vidéo que vous souhaitez charger :
- Indiquez le nom de la vidéo
- Indiquez une URL (YouTube ou Vimeo uniquement)
- Chargez une miniature à associer à la vidéo. La miniature doit utiliser le format d’image png et sa taille doit être de 1280 pixels X 720 pixels. 

Pour ajouter de nouvelles vidéos, sélectionnez **+Nouveau**, comme indiqué dans la capture d’écran suivante.

![Ajouter une vidéo](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Document

Vous pouvez charger un maximum de trois documents. Tous les documents doivent être au format PDF. Pour ajouter un nouveau document :

- Sélectionnez **+Nouveau**.
- Entrez le nom du document
- Sélectionnez **Charger** pour charger un document.

![Ajouter un nouveau document](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Politique de confidentialité

Entrez l’URL de la politique de confidentialité de votre application

### <a name="terms-of-use"></a>Conditions d’utilisation

Entrez les conditions d’utilisation de votre application. Les clients AppSource doivent accepter ces conditions avant de pouvoir essayer de votre application.

>[!Note]
>Ce champ accepte le contenu des balises HTML, comme les balises de contenu html <p\>, <h1\> et <li\>. Vous pouvez utiliser ces balises pour mettre en forme votre contenu. 

### <a name="lead-destination"></a>Destination du prospect

Sélectionnez un système CRM dans lequel vos prospects seront stockés. 

Sélectionnez **Table Azure** si vous utilisez un des systèmes CRM suivants : Salesforce, Marketo ou Microsoft Dynamics CRM. 

Pour plus d’informations sur le système CRM que vous souhaitez utiliser, sélectionnez un des liens suivants pour les systèmes pris en charge.

-   [Table Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
