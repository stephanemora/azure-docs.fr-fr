---
title: Créer une nouvelle offre SaaS dans la Place de marché commerciale existante
description: Comment créer une nouvelle offre Software as a service (SaaS) pour répertorier ou vendre dans la Place de marché Azure, AppSource ou via le programme de fournisseur de solutions Cloud (CSP) avec le portail de la Place de marché commerciale sur Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: dc086bc1252c084b717807213b5ba4c7f9d7bb97
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514051"
---
# <a name="create-a-new-saas-offer"></a>Créer une nouvelle offre SaaS

Pour commencer à créer des offres Software as a service (SaaS), veillez à d’abord [Créer un compte Partner Center](./create-account.md) et ouvrez le [tableau de bord de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), et sélectionnez l’onglet **Présentation**.

![Tableau de bord de la Place de marché commerciale sur Partner Center](./media/new-offer-overview.png)

Sélectionnez le bouton + **Créer nouveau...** , puis sélectionnez l’élément de menu **Software as a service**. 

Si vous sélectionnez l’un des autres types d’offre, vous êtes redirigé vers l’ancien [Portail Cloud Partner](https://cloudpartner.azure.com/).  À l’heure actuelle, seules les offres SaaS sont disponibles dans le portail de la Place de marché commerciale sur Partner Center. 

![Créer une fenêtre d’offre sur Partner Center](./media/new-offer-click.png)

La boîte de dialogue **Nouvelle offre** s’affiche. 

![Boîte de dialogue Nouvelle offre](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>ID et nom de l’offre

- **ID de l’offre** : Créez un identificateur unique par offre dans votre compte. Cet ID sera visible par les clients dans l’adresse URL de l’offre de la Place de marché et des modèles Resource Manager (si applicable). L’ID de l’offre doit être en minuscules, alphanumérique (dont traits d’union, tirets du bas, mais pas d’espace blanc). Il est limité à 50 caractères et ne peut pas être mis à jour après sa création.  
Exemple : test-offer-1
<br>Voici l’URL obtenu : `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Nom de l’offre** : Nom officiel de votre offre d’application SaaS, cohérent sur plusieurs publications, annonces et sites web.  Ce nom peut être une marque.  Le nom de l’offre ne doit pas contenir d’espace blanc, d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
<br>Exemple : Offre test 1&#8482;

Sélectionnez **Créer**.  Une page **Présentation de l’offre** est créée.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

La page **Présentation de l’offre** comprend les informations suivantes : 

- Le **statut de publication**, qui affiche une représentation visuelle des étapes requises pour publier cette offre et le temps nécessaire à chaque étape. Les icônes des étapes de publication non complétées sont grisées. 

- Le menu **Présentation de l’offre** contient une liste de liens pour réaliser des opérations sur cette offre. Cette liste d’opérations change selon la sélection que vous faites pour votre offre.  
    - Si l’offre est un brouillon : Supprimer le brouillon 
    - Si l’offre est publiée : Arrêter de vendre l’offre 
    - Si l’offre est disponible en version préliminaire : Mettre en service 
    - Si vous n’avez pas complété Déconnexion de l’éditeur : Annuler la publication

## <a name="offer-setup"></a>Configuration de l’offre

L’onglet **Configuration de l’offre** nécessite les informations suivantes : Sélectionnez **Enregistrer** après avoir renseigné ces champs.

- **Souhaitez-vous vendre via Microsoft ?** (Oui/Non)
    - **Oui**, vous souhaitez vendre votre offre via Microsoft, avec des transactions sur la place de marché hébergée par Microsoft en votre nom, ou 
    - **Non**, vous préférez simplement répertorier votre offre via les places de marché, et traiter les transactions monétaires indépendamment de Microsoft.    

### <a name="sell-through-microsoft"></a>Vendre via Microsoft

La vente via Microsoft fournit une meilleure découverte et acquisition de client, permet à Microsoft d’héberger des transactions de la place de marché, et tire profit des capacités commerciales mondiales disponibles de Microsoft.

#### <a name="saas-offer-requirements"></a>Exigences de l’offre SaaS

Afin de répertorier des offres SaaS avec la Place de marché commerciale sur Partner Center, les critères suivants doivent être respectés :

- Votre offre doit utiliser [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification et la gestion d’identités.
- Votre offre doit utiliser des [API de traitement SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) pour intégrer avec la Place de marché Azure.
- Pour des exigences plus complètes, consultez le [Guide de publication des offres SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure SaaS sur Azure
Si l’offre SaaS est hébergée sur Azure, vous, l’éditeur, devez regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût. Ce coût est facturé au client selon un tarif mensuel fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement à vous, le partenaire. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle. 

Les frais de licence logicielle sont présentés comme des frais mensuels et à taux fixe, basés sur un site récurrent, et ne sont pas basés sur la consommation ou le compteur.

|**Coût de votre licence**|**100 $/mois**|
|:---|:---|
|Coût d’utilisation Azure (D1/1 cœur)|Facturé directement à l’éditeur au lieu du client|
|Microsoft facture au client le montant suivant :|100 $/mois (l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)|

- Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.
- Les partenaires ayant été qualifiés pour les **Frais liés au service Place de marché réduits** verront des frais de transaction réduits sur les offres SaaS de mai 2019 à juin 2020. Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 90 $ à l’éditeur.

> [!NOTE]
> **Frais liés au service Place de marché réduits** : Pour certaines offres SaaS que vous avez publiées sur notre Place de marché commercial, Microsoft réduit ses frais liés au service Place de marché de 20 % (comme décrit dans le Contrat d’éditeur Microsoft) à 10 %. Afin que votre offre soit qualifiée, au moins une de vos offres doit avoir été désignée par Microsoft comme étant Prête à la co-vente IP ou Priorité à la co-vente IP.  L’éligibilité doit être respectée au moins cinq (5) jours ouvrés avant la fin de chaque mois afin de recevoir ces frais liés au service Place de marché réduits pour le mois.  Les frais liés au service Place de marché réduits ne s’appliquent pas aux machines virtuelles, aux applications managées ou tout autre produit, disponible via notre Place de marché commerciale.  Les frais liés au service Place de marché réduits ne sont disponibles qu’aux offres qualifiées pour les frais de licence collectés par Microsoft entre le 1er mai 2019 et le 30 juin 2020.  Au-delà de cette date, les frais liés au service Place de marché réduits retournent à leur montant initial. 

|**Microsoft facture**|**100 $/mois**|
|:---|:---|
|Microsoft vous verse 80 % des revenus générés par les licences <br>**Pour les applications SaaS qualifiées, Microsoft paie 90 % de vos coûts de licence*|80 $/mois <br>*$* 90 $/mois*|


#### <a name="csp-program-opt-in"></a>Abonnement au programme CSP
Le programme [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) permet aux offres d’être diffusées auprès de millions de clients Microsoft qualifiés avec un minimum d’investissement dans la vente et le marketing.

- **Canaux : Rendre mon offre disponible dans le programme CSP** (Cocher la case)

Choisir de rendre votre offre disponible dans le programme CSP permet aux fournisseurs de solutions Cloud de vendre votre produit dans une offre groupée de solution à leurs clients. 

### <a name="list-through-microsoft"></a>Répertorier via Microsoft

Promouvoir votre entreprise avec Microsoft en créant une liste de la Place de marché. Choisir de répertorier votre offre uniquement et de ne pas faire de transactions via Microsoft signifie que Microsoft ne participe pas directement dans les transactions de licences logicielles. Il n’y a aucun frais de transaction associé et l’éditeur récupère 100 % des frais de licence logicielle collectés auprès des clients. Toutefois, l’éditeur a la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte. 

- **Comment voulez-vous qu’interagissent les clients avec l’offre de liste ?**

##### <a name="get-it-now-free"></a>Obtenez-en une maintenant (gratuit)
Répertoriez votre offre gratuitement pour les clients en fournissant une URL valide (qui commence par http ou https) où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Essai gratuit
Répertoriez votre offre en tant qu’essai gratuit pour les clients en fournissant une URL valide (qui commence par http ou https) où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Me contacter
Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la Place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Connecter la gestion des prospects](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Exemple d’annonce d’offre sur Place de marché

![Exemple d’annonce d’offre sur Place de marché avec remarques](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Activer une version d’évaluation

Une version d’évaluation vous permet de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l’« essayer avant de l’acheter », ce qui se traduit par une hausse du taux de conversion et par la génération de prospects hautement qualifiés. [Découvrez-en plus sur les versions d’évaluation.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Activer une version d’évaluation** (Cocher la case) 

En activant une version d’évaluation, vous devrez configurer un environnement de démonstration pour que les clients puissent essayer votre offre pendant une période donnée. 

### <a name="type-of-test-drive"></a>Type de version d’évaluation

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure.
- **[Dynamics 365 pour Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources Business Central (finance, opérations, chaîne d’approvisionnement, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système Customer Engagement (ventes, service, service de projet, service après-vente, etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources d’une entreprise Finance et opérations (finance, opérations, manufacture, chaîne d’approvisionnement, etc.). 
- **[Application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Un modèle de déploiement englobant toutes les architectures de solution complexe. Tous les produits personnalisés doivent tous utiliser ce type de version d’évaluation.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Un lien hypertexte vers un tableau de bord personnalisé. Les produits dotés d’un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

#### <a name="additional-test-drive-resources"></a>Ressources supplémentaires pour version d’évaluation
- [Meilleures pratiques techniques pour version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Meilleures pratiques marketing pour version d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Document d’une page pour présenter une version d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Connecter la gestion des prospects

Connectez-vous avec les clients directement en répertoriant votre offre dans les Places de marché et en introduisant votre système CRM afin de recevoir les informations de contact des clients immédiatement après qu’un client ait exprimé son intérêt ou déployé votre produit.

- **Choisir une destination de prospect** (menu déroulant) : Renseignez les informations de connexion au système CRM sur lequel vous souhaitez que nous envoyions des prospects de client. 

Partner Center prend en charge les systèmes CRM suivant pour la gestion des prospects. Sélectionnez le lien pour obtenir des instructions de configuration.

- Blob Azure : renseignez l’adresse e-mail, le nom du conteneur et la chaîne de connexion du compte de stockage. 
- [Table Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) : renseignez l’adresse e-mail et la chaîne de connexion du compte de stockage. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) : renseignez l’adresse e-mail, l’URL et le mode d’authentification (Office 365 ou Azure Active Directory).
- [Point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) : renseignez l’adresse e-mail et l’URL du point de terminaison HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) : renseignez l’adresse e-mail, l’ID du formulaire, l’ID du compte Munchkin et l’ID du serveur.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) : renseignez l’adresse e-mail et l’ID de l’organisation. 

#### <a name="additional-lead-management-resources"></a>Ressources de gestion des prospects supplémentaires
- [Questions fréquentes sur la gestion des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erreurs courantes de configuration des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Document d’une page présentant la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

## <a name="properties"></a>properties
L’onglet **Propriétés** vous invite à définir les catégories et secteurs utilisés pour grouper votre offre selon les Places de marché, les contrats légaux de votre offre, et la version de votre application. 

Sélectionnez **Enregistrer** après avoir renseigné ces champs. 

### <a name="category"></a>Catégorie
Sélectionnez un minimum d’une (1) catégorie et un maximum de trois (3) catégories utilisées pour grouper votre offre dans les zones de recherche appropriées sur la Place de marché. Expliquez dans la description comment votre offre prend en charge ces catégories dans la description de l’offre. 

### <a name="industry"></a>Secteur d’activité
Sélectionnez jusqu’à deux (2) secteurs utilisés pour grouper votre offre dans les zones de recherche appropriées sur la Place de marché. Si votre offre n’est pas spécifique à un secteur, ne sélectionnez rien. Expliquez dans la description comment votre offre prend en charge les secteurs sélectionnés dans la description de l’offre. 

### <a name="app-version"></a>Version de l’application
Il s’agit d’un champ facultatif utilisé dans la Place de marché AppSource pour identifier le numéro de version de votre offre. 

### <a name="standard-contract"></a>Contrat standard

- **Utiliser le contrat standard ?**

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un modèle de contrat Standard afin de faciliter les transactions sur la place de marché. 

Plutôt que d’élaborer des conditions générales personnalisées, les éditeurs de la Place de marché Azure peuvent choisir de proposer leur logiciel dans le cadre du contrat Standard, que les clients ne doivent accepter qu’une seule fois. 

Le contrat Standard est disponible ici : https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Conditions d’utilisation

Si les termes du contrat de licence sont différents du contrat Standard, vous pouvez choisir d’entrer vos propres conditions d’utilisation ici. Vous pouvez également saisir jusqu’à 10 000 caractères dans ce champ. Si vos conditions d’utilisation nécessitent une description plus longue, saisissez un lien URL unique dans ce champ où trouver les conditions d’utilisation supplémentaires. Ceci affichera un lien actif aux clients.

Les clients doivent accepter ces conditions avant de pouvoir essayer de votre application. 

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

## <a name="offer-listing"></a>Annonce de l’offre

L’onglet Annonce de l’offre affiche les langues (et les marchés) disponibles pour votre offre. English (États-Unis) est actuellement le seul emplacement disponible. De plus, cette page affiche le statut de l’annonce spécifique à une langue et l’heure et la date à laquelle elle a été ajoutée. Vous devez définir les informations de la Place de marché (nom de l’offre, description, termes de recherche, etc.) pour chaque langue / marché.

### <a name="offer-listings"></a>Liste des offres

Renseignez les informations à afficher dans la place de marché, dont les descriptions des composants marketing et de votre offre.

- **Name** [obligatoire] : Le nom défini ici s’affiche comme titre de l’annonce de votre offre sur le(s) place(s) de marché choisie(s). Le nom est prérempli selon votre précédente entrée **Nouvelle offre**.  Ce nom peut être une marque.  Il ne doit pas contenir d’espace blanc, d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Summary** (obligatoire) : Renseignez une courte description de votre offre à utiliser dans les résultats de la recherche d’annonces dans une place de marché. Jusqu’à 100 caractères peuvent être entrés dans ce champ.
- **Description** (obligatoire) : Renseignez une description de votre offre à afficher dans la présentation des annonces sur une place de marché. Vous pouvez inclure une proposition de valeur, des avantages principaux, des associations de catégorie ou de secteur, des opportunités d’achats dans une application, des publications requises et un lien pour en savoir plus.
Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. Pour obtenir plus d’astuces, consultez l’article [Rédiger une bonne description d’application](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Mots clés de recherche** : Entrez un maximum de trois mots clés que les clients pourront utiliser pour rechercher votre offre sur les Places de marché.
- **Instructions de prise en main** (obligatoire) : Expliquez comment configurer et démarrer votre application à des clients potentiels.  Ce démarrage rapide peut contenir des liens vers des ressources en ligne plus détaillées. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 

#### <a name="links"></a>Liens

- **Politique de confidentialité** (obligatoire) : Lien vers la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.
- **Ressources Marketing du programme CSP** (facultatif) : Vous devez fournir un lien vers les ressources marketing si vous choisissez d’étendre votre offre au programme [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers). Le programme CSP étend votre offre à un spectre plus large de clients qualifiés en permettant aux partenaires CSP de grouper votre offre, de la mettre sur le marché et de la revendre. Ces revendeurs doivent accéder à des ressources marketing de votre offre. Pour plus d’informations, consultez [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Liens utiles** (facultatif) : Documents en ligne supplémentaires et facultatifs concernant votre application ou services connexes répertoriés en fournissant un **titre** et une **URL**. Ajoutez des liens utiles supplémentaires en cliquant sur **+ Ajouter une URL**.

#### <a name="contact-information"></a>Informations de contact

- **Contacts** : Pour chaque contact client, renseignez le **Nom**, **Numéro de téléphone** et **Adresse e-mail** d’un employé.  Ces informations *ne seront pas* visibles publiquement. Une **URL de support** est aussi nécessaire pour le groupe **Contact support**.  Ces informations *seront* visibles publiquement.

**Contact support** (obligatoire) : Pour toute question de support générale.

**Contact ingénierie** (obligatoire) : Pour toute question technique.

**Contact responsable canal** (obligatoire) : Pour toute question du revendeur relative au programme CSP.

#### <a name="files-and-images"></a>Fichiers et images

- **Documents** (obligatoire) : Ajoutez des documents marketing connexes à votre offre, au format PDF, en renseignant au minimum un (1) et au maximum trois (3) documents par offre.
- **Images** (facultatif) : Il existe plusieurs lieux où les images du logo de votre offre peuvent apparaître sur les places de marché, nécessitant les tailles suivantes, Petite : 48 x 48 pixels _(obligatoire),_ Moyenne : 90 x 90 pixels, Grande : 216 x 216 pixels _(obligatoire),_ Large : 255 x 115 pixels, et Héros : 815 x 290 pixels. Toutes les images doivent être au format .PNG.
- **Captures d’écran** (obligatoire) : Ajoutez des captures d’écran illustrant votre offre. Un maximum de cinq (5) captures d’écran peut-être ajouté. Ces captures doivent être dimensionnées à 1280 x 720 pixels. Toutes les images doivent être au format .PNG.
- **Vidéos** (facultatif) : Ajoutez des liens vers des vidéos présentant votre offre. Vous pouvez utiliser des liens de vidéos YouTube et/ou Vimeo, qui seront présentées aux clients en même temps que votre offre. Vous devez également inclure une miniature de la vidéo, au format PNG de 1280 x 720 pixels. Vous pouvez afficher un maximum de quatre vidéos par offre.

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

- [Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>PRÉVERSION

L’onglet **Préversion** vous permet de définir un **Public de préversion** limité avant de publier votre offre à un public plus important.

> [!IMPORTANT]
> Vous devez sélectionner **Publier** avant pour que votre offre soit publiée publiquement sur la place de marché après l’avoir vérifiée en Préversion.

- **Définir un public pour la Préversion : Ajoutez une seule adresse e-mail de compte AAD/MSA par ligne, avec une description facultative.**

Ajoutez manuellement jusqu’à dix (10) adresses e-mail ou vingt (20) en chargeant un fichier CSV, pour un compte Microsoft (MSA) ou des comptes Azure Active Directory (AAD) existants afin d’aider à la validation de votre offre avant sa publication. En ajoutant ces comptes, vous définissez un public qui sera autorisé à accéder à la préversion de votre offre avant sa publication sur les places de marché. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion est autorisé à accéder à votre offre _avant_ sa publication sur les places de marché. Vous pouvez aussi choisir de créer un plan et de le rendre disponible uniquement à un public privé. Dans l’onglet **Liste des plans**, vous pouvez définir un public privé en cochant **Il s’agit d’un plan privé**. Vous pouvez définir un public privé comprenant jusqu’à 20 000 clients à l’aide des ID de locataire Azure.

## <a name="technical-configuration"></a>Configuration technique

L’onglet **Configuration technique** définit les informations techniques (chemin URL, webhook, ID locataire et ID de l’application) utilisées pour se connecter à votre offre. Cette connexion permet d’approvisionner votre offre pour le client final, s’il choisit de l’acquérir. Les diagrammes décrivant l’utilisation des champs collectés sont disponibles dans la documentation des [API de traitement SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL de la page d’accueil** (obligatoire) : Définissez l’URL du site auquel les clients accéderont à l’acquisition de votre offre à partir de la place de marché. Cette URL constitue le point de terminaison recevant un jeton lorsqu’un client est redirigé vers la page. Ce jeton peut être échangé contre des informations sur l’approvisionnement à l’aide de la résolution dans les API de traitement. Ces informations et toutes celles que vous collectez peuvent être utilisées au sein d’une page web interactive pour le client, intégrée à votre expérience afin de compléter l’inscription et d’activer leur achat.

- **Webhook de connexion** (obligatoire) : Pour tous les événements asynchrones que Microsoft doit vous envoyer au nom du client (exemple : un abonnement SaaS devenu non valide), nous vous demandons de fournir un webhook de connexion. Si vous ne disposez pas d’un système de webhook, la configuration la plus simple consiste à disposer d’une application de logique de point de terminaison HTTP qui écoute les événements qui sont publiés et les traite de façon appropriée (par exemple, https:\//prod-1westus.logic.azure.com:443/work). Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID du locataire Azure AD** (obligatoire) : Dans le portail Azure, vous devez [créer une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) afin que nous puissions confirmer que la connexion entre nos deux services se fait bien dans le cadre d’une communication authentifiée. Pour trouver l’[ID du locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), rendez-vous dans Azure Active Directory et sélectionnez **Propriétés**, puis cherchez le numéro de l’**ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID de l’application Azure AD** (obligatoire) : Vous avez également besoin de l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) et d’une clé d’authentification. Pour obtenir ces valeurs, rendez-vous dans Azure Active Directory et sélectionnez **Inscriptions d’applications**, puis cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Pour trouver la clé d’authentification, accédez à **Paramètres** et sélectionnez **Clés**. Vous devez fournir une description et une durée, et vous obtiendrez ensuite une valeur numérique.

 Remarque : l’ID de l’application Azure est associé à votre ID d’éditeur. Veillez donc à utiliser le même ID d’application dans toutes les offres.

## <a name="plan-overview"></a>Vue d’ensemble du plan

L’onglet **Vue d’ensemble du plan** vous permet de fournir une variété d’options de plan dans la même offre. Ces plans (parfois appelés références SKU) peuvent différer en termes de version, de monétisation ou de niveaux de service. Vous devez configurer au moins un plan avant de pouvoir vendre votre offre dans la place de marché.

Une fois créé, vous verrez vos noms de plan, ID, modèles de tarification, disponibilité (public ou privé), l’état de publication actuel et les actions disponibles.

Les **Actions** disponibles dans **Vue d’ensemble du plan** varient selon l’état actuel de votre plan et peuvent inclure :

- Si l’état du plan est **Brouillon** : supprimer le brouillon
- Si l’état du plan est **Publié** : arrêter de vendre le plan ou Synchroniser public privé

**Créer un plan** (au moins un plan pour ceux qui choisissent de vendre via Microsoft)

- **ID du plan :** Créez un ID de plan unique pour chaque plan dans cette offre. Cet ID sera visible par les clients dans l’adresse URL du produit et des modèles Resource Manager (si applicable). Utilisez uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. 50 caractères maximum autorisés pour cet ID de plan. Remarque : l’ID ne peut être modifié après avoir sélectionné Créer.
- **Nom du plan :** Les clients verront ce nom lorsqu’ils décideront du plan à sélectionner dans votre offre. Créez un ID d’offre unique pour chaque plan dans cette offre. Le nom du plan sert à différencier les plans logiciels pouvant faire partie de la même offre (par exemple, Nom de l’offre : Windows Server ; plans : Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Liste des plans

L’onglet **Liste des plans** affiche les langues (et les marchés) disponibles pour votre plan. English (États-Unis) est actuellement le seul emplacement disponible. De plus, cette page affiche le statut de l’annonce spécifique à une langue et l’heure et la date à laquelle elle a été ajoutée. Vous devez définir les informations de la Place de marché (nom de l’offre, description, termes de recherche, etc.) pour chaque langue / marché.

#### <a name="plan-listing-details"></a>Informations relatives à la liste des plans

Sélectionnez l’une des langues de plan pour afficher les informations relatives à la **Liste des plans**, dont le **Nom** et la **Description**.

- **Nom** : Ces informations sont préremplies en fonction de votre saisie **Nouveau plan** et serviront de titre du « plan logiciel » de votre offre, affichée sur la place de marché.
- **Description :** Cette description permet d’expliquer ce qui rend ce plan logiciel unique et de pointer les différences par rapport aux autres plans logiciels dans votre offre. Peut contenir jusqu’à 500 caractères.

Sélectionnez **Enregistrer** après avoir renseigné ces champs.

#### <a name="plan-pricing-and-availability"></a>Tarification et disponibilité du plan

L’onglet **Tarification et disponibilité** vous permet de configurer les marchés dans lesquels sera disponible ce plan, ainsi que le modèle de monétisation souhaité, le prix et la fréquence de facturation. De plus, vous pouvez indiquer si vous voulez que le plan soit visible à tous ou uniquement à des utilisateurs spécifiques (un public privé).

#### <a name="markets"></a>Marchés

- **Modifier les marchés** (facultatif)

Chaque plan doit être disponible sur au moins un marché. Cochez la case des emplacements dans lesquels vous souhaitez que ce plan soit disponible. Une zone de recherche et un bouton pour sélectionner les pays « reversant les taxes », pour lesquels Microsoft reverse les ventes et utilise les taxes en votre nom, sont inclus pour vous aider. 


Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché sera calculé selon le taux de change en vigueur. Vous devez toujours vérifier le prix de chaque marché avant toute publication. La tarification peut être vérifiée à l’aide du lien « Exporter les prix (xlsx) », après avoir enregistré vos changements.

#### <a name="pricing"></a>Tarifs

- **Modèle de tarification** : Taux fixe ou basé sur l’occupation

**Taux fixe :** Autorisez l’accès à votre offre via un tarif unique et fixe mensuel ou annuel. Cela est parfois désigné sous le nom de « tarification en fonction du site ».

**Selon l’occupation :** Autorisez l’accès à votre offre via un prix basé sur le nombre d’utilisateurs ou de *sièges* occupés. Ce modèle basé sur l’occupation vous permet de définir le nombre minimum et maximum de d’utilisateurs autorisés en fonction du prix. Ainsi, plusieurs gammes de prix peuvent être configurées en fonction du nombre d’utilisateurs en configurant plusieurs plans.  Ces champs sont facultatifs. Si vides, le nombre d’utilisateurs sera considéré sans limite (avec un minimum de 1 et un maximum d’autant d’utilisateurs que peut prendre en charge le système). Ces champs peuvent être modifiés dans le cadre d’une mise à jour de votre plan.

Une fois publié, le choix du modèle de tarification ne peut plus être modifié. En outre, tous les plans d’une même offre doivent partager le même modèle de tarification.

- **Fréquence de facturation** : Mensuelle ou annuelle

Sélectionnez la fréquence de paiement du prix indiqué. Vous devez renseigner au moins un prix Mensuel ou Annuel, ou les deux options peuvent être disponibles pour les clients.

- **Prix** : USD par mois ou USD par an

Les prix fixés dans une devise locale (USD = dollar américain) sont convertis dans la devise locale de tous les marchés sélectionnés selon les taux de change en vigueur disponibles lors de la configuration. Validez ces prix avant la publication en exportant la feuille de calcul de tarification et en vérifiant le prix de chaque marché. Si vous souhaitez fixer des prix personnalisés dans un marché individuel, modifiez et importez la feuille de calcul de tarification. Vous êtes responsable de valider cette tarification et possédez ces paramètres.
**Vous devez d’abord enregistrer les modifications de votre tarification pour autoriser l’export des données de tarification.*

Vérifiez vos prix avec soin avant la publication, car il y a des restrictions sur ce qui peut être modifié après la publication d’un plan :

- Une fois qu’un plan est publié, le modèle de tarification ne peut pas être modifié.
- Une fois qu’une fréquence de facturation est publiée pour un plan, elle ne peut pas être supprimée ultérieurement.
- Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement.

### <a name="plan-audience"></a>Public du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible pour tous ou pour un public spécifique. Vous pouvez attribuer l’appartenance dans ce public restreint à l’aide des ID locataire Azure AD.

#### <a name="privacy"></a>Confidentialité

- **Il s’agit d’un plan privé** (case optionnelle)

Cochez cette case pour rendre votre plan privé et visible uniquement pour le public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois un plan publié comme visible pour tous, il doit le rester pour tout le monde. (Vous ne pouvez plus configurer ce plan comme plan privé).

- **Public restreint (ID locataire)**

Affectez le public qui aura accès à ce plan privé. L’accès est affecté à l’aide d’ID locataire avec l’option d’inclure une description de chaque ID locataire affecté. Un maximum de 10 ID locataire peut être ajouté, ou de 20 000 ID locataire client en important un fichier CSV.

Un locataire est une représentation d’une organisation, dont l’ID est représenté sous la forme d’un GUID (Identificateur Global Unique, un nombre entier de 128 bits utilisé pour identifier les ressources). Il s’agit d’une instance Azure AD dédiée, reçue par une organisation ou un développeur d’applications lorsque l’organisation ou le développeur d’applications crée une relation avec Microsoft (l’inscription à Azure, Microsoft Intune ou Microsoft 365, par exemple). Chaque client Azure AD est distinct et indépendant des autres clients Azure AD. Pour vérifier le locataire, connectez-vous au Portail Azure avec le compte que vous souhaitez utiliser pour gérer votre application. Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous pouvez voir son nom directement sous le nom de votre compte. Dans l’angle supérieur droit du Portail Azure, survolez le nom de votre compte avec la souris pour voir votre nom, votre adresse e-mail, votre répertoire et votre ID de locataire (un GUID), ainsi que votre domaine. Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID. Vous pouvez aussi rechercher l’ID locataire de votre organisation à l’aide d’une URL de nom de domaine : [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Tandis que les offres SaaS utilisent des ID de locataire pour définir un public privé, d’autres types d’offre peuvent utiliser des ID d’abonnement Azure (qui sont également représentés sous forme de GUID).

> [!NOTE]
> Le public privé (ou public restreint) n’est pas le même que le public pour la préversion. Dans l’onglet **[Préversion](#preview)** , vous pouvez définir un public pour la préversion. Un public de préversion est autorisé à accéder à votre offre *avant* sa publication sur la place de marché. Tandis que la désignation de public privé ne s’applique qu’à un plan spécifique, le public pour la préversion peut consulter tous les plans (privés ou non), mais seulement pendant la période limitée de la préversion, pendant le test et la validation du plan.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exemple de liste de plans au sein d’une offre de place de marché

![Exemple de liste de plans de place de marché avec remarques](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

L’onglet **Version d’évaluation** vous permet de configurer une démonstration (ou « version d’évaluation ») qui permettra à des clients d’essayer votre offre avant de s’engager et l’acheter. Pour en savoir, consultez l’article [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Si vous ne souhaitez plus fournir une version d’évaluation de votre offre, retournez sur la page **[Configuration de l’offre](#offer-setup)** et décochez **Activer la version d’évaluation**.

### <a name="technical-configuration"></a>Configuration technique
Les types de versions d’évaluation suivants sont disponibles, chacun avec leurs propres configurations techniques requises.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Application logique](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuration technique non requise)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuration technique pour la version d’évaluation d’Azure Resource Manager

Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure. En savoir plus sur la configuration d’une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régions** (obligatoire) : Il existe actuellement 26 régions Azure prises en charge où votre version d’évaluation peut être rendue disponible. En règle générale, vous devez rendre votre version d’évaluation disponible dans les régions où vous prévoyez le plus grand nombre de clients, de sorte qu’ils puissent sélectionner la région la plus proche pour des performances optimales. Vous devez vous assurer que votre abonnement a l’autorisation de déployer toutes les ressources nécessaires dans chacune des régions sélectionnées.

- **Instances** : Sélectionnez le type (chaud ou froid) et le nombre d’instances disponibles, qui seront multipliés par le nombre de régions dans lesquelles votre offre est disponible.

**Hot** : Ce type d’instance est déployé et en attente de l’accès par région sélectionnée. Les clients peuvent accéder instantanément aux instances *à chaud* d’une version d’évaluation au lieu d’attendre un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir au moins une instance *à chaud*, étant donné que la plupart de vos clients ne souhaitent pas attendre la fin des déploiements complets, et il y a donc une chute dans l’utilisation du client si aucune instance *à chaud* n’est disponible.

**À froid** : Ce type d’instance représente le nombre total d’instances pouvant éventuellement être déployées par région. Les instances à froid nécessitent le modèle Resource Manager entier de la version d’évaluation pour être déployées lorsqu’un client demande la version d’évaluation, les instances *à froid* sont donc plus lentes à charger que les instances *à chaud*. L’inconvénient est que vous avez uniquement à payer pour la durée de la version d’évaluation, elle n’est *pas* toujours en cours d’exécution sur votre abonnement Azure comme c’est le cas avec une instance *à chaud*.

- **Modèle Azure Resource Manager de version d’évaluation** : Téléchargez le fichier .zip contenant votre modèle Azure Resource Manager.  Apprenez-en plus sur la création d’un modèle Azure Resource Manager dans l’article de démarrage rapide [Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » heures ; « 1,5 » n’est pas valide).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuration technique pour la version d’évaluation de Dynamics 365

Microsoft peut retirer la complexité de la configuration d’une version d’évaluation en hébergeant et en conservant l’approvisionnement et le déploiement du service à l’aide de ce type de version d’évaluation. La configuration de ce type de version d’évaluation hébergé est la même, peu importe si la version d’évaluation cible un public Business Central, Customer Engagement ou Operations.

- **Versions d’évaluation simultanées max** (obligatoire) : Définissez le nombre maximum de client pouvant utiliser votre version d’évaluation à la fois. Chaque utilisateur simultané consommera une licence Dynamics 365 tant que la version d’évaluation est active. Veillez donc à disposer de suffisamment de licences pour prendre en charge la limite maximale définie. La valeur recommandée est de 3 à 5.

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en indiquant le nombre d’heures. Passée cette durée, la session prendra fin et aucune de vos licences ne sera plus utilisée. La valeur recommandée est de 2 à 24 heures, selon la complexité de votre offre. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » heures ; « 1,5 » n’est pas valide).  L’utilisateur peut demander une nouvelle session s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.

- **URL de l’instance** (obligatoire) : L’URL où le client commencera sa version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés (par exemple, https://testdrive.crm.dynamics.com) ).

- **URL d’API web de l’instance** (obligatoire): Récupérez l’URL d’API web pour votre instance Dynamics 365 en vous connectant à votre compte Microsoft 365 et en allant dans **Paramètres** \&gt; **Personnalisation** \&gt; **Ressources développeur** \&gt; **API web de l’instance (URL racine de service)** , copiez l’URL trouvée ici (par exemple, https://testdrive.crm.dynamics.com/api/data/v9.0) ).

- **Nom de rôle** (obligatoire).: Renseignez le nom du rôle de sécurité que vous avez défini dans votre version d’évaluation personnalisée de Dynamics 365. Il sera attribué à l’utilisateur lors de la version d’évaluation (par exemple, test-drive-role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuration technique pour la version d’évaluation d’applications logiques

Tous les produits personnalisés doivent utiliser ce type de modèle de déploiement de version d’évaluation, qui englobe une variété d’architectures de solution complexes. Pour plus d’informations sur la configuration de versions d’évaluation d’applications logiques, consultez [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) sur GitHub.

- **Région** (liste déroulante obligatoire, à sélection unique) : Il existe actuellement 26 régions Azure prises en charge où votre version d’évaluation peut être rendue disponible. Les ressources pour votre application logique seront déployées dans la région que vous sélectionnez. Si votre application logique compte des ressources personnalisées stockées dans une région spécifique, assurez-vous que celle-ci est sélectionnée ici. La meilleure façon de procéder consiste à déployer entièrement votre application logique localement vers votre abonnement Azure dans le portail et à vérifier qu’elle fonctionne correctement avant de la sélectionner.

- **Versions d’évaluation simultanées max** (obligatoire) : Définissez le nombre maximum de client pouvant utiliser votre version d’évaluation à la fois. Ces versions d’évaluation sont déjà déployées, permettant ainsi aux clients d’y accéder instantanément sans attendre un déploiement.

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

- **Nom du groupe de ressources Azure** (obligatoire) : Entrez le nom du [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) où la version d’évaluation de votre application logique est enregistrée.

- **Nom de l’application logique Azure** (obligatoire) : Entrez le nom de l’application logique qui affecte la version d’évaluation à l’utilisateur. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

- **Nom de l’application logique de déprovisionnement** (obligatoire) : Entrez le nom de l’application logique qui déprovisionne la version d’évaluation, une fois que le client a terminé. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuration technique non requise pour les versions d’évaluation de Power BI

Les produits souhaitant afficher un visuel interactif Power BI peuvent utiliser un lien hypertexte pour partager un tableau de bord personnalisé et intégré qui sert de version d’évaluation, sans configuration technique supplémentaire. En savoir plus sur la configuration d’applications de modèle [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour déployer la version d’évaluation de votre côté, veuillez créer et fournir un abonnement Azure séparé et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et les applications logiques) : Entrez l’ID d’abonnement pour accorder l’accès à vos services de compte Azure pour obtenir un rapport et une tarification sur l’utilisation des ressources. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID du locataire Azure AD** (obligatoire) : Saisissez l’[ID client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id) de votre Azure Active Directory. Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Propriétés**, cherchez le numéro de l’**ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi rechercher l’ID locataire de votre organisation à l’aide l’URL de votre nom de domaine : [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nom du locataire Azure AD** (obligatoire pour Dynamics 365) : Entrez le nom de votre locataire Azure Active Directory. Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.

- **ID de l’application Azure AD** (obligatoire) : Entrez l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) Azure Active Directory. Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Inscriptions d’applications**, cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé secrète client de l’application Azure AD** (obligatoire) : Entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) d’application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation gauche, sélectionnez l’onglet **Azure Active Directory**,puis **Inscriptions d’applications**, et enfin votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur. (Ne quittez pas la page avant d’avoir terminé, ou vous n’aurez pas accès à la valeur.)

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

### <a name="test-drive-listings-optional"></a>Listes des versions d’évaluation (facultatives)

L’option **Liste des versions d’évaluation** disponible sous l’onglet **Version d’évaluation** affiche les langues (et les marchés) disponibles pour votre version d’évaluation. English (États-Unis) est actuellement le seul emplacement disponible. De plus, cette page affiche le statut de l’annonce spécifique à une langue et l’heure et la date à laquelle elle a été ajoutée. Vous devez définir les détails de la version d’évaluation (description, manuel de l’utilisateur, vidéos, etc.) pour chaque langue et marché.

- **Description** (obligatoire) : Décrivez votre version d’évaluation, ce qui sera présenté, les objectifs que l’utilisateur pourra essayer, les fonctionnalités à explorer, et toute autre information qui aidera l’utilisateur à déterminer ou non s’il souhaite faire l’acquisition de votre offre. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 

- **Accéder aux informations** (obligatoire pour les versions d’évaluation Azure Resource Manager et d’applications logiques) : Expliquez ce qu’un client doit savoir pour accéder à cette version d’évaluation et l’utiliser. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) : Procédure approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos : Ajouter des vidéos** (facultatif) : Vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer via un lien et une image miniature (533 x 324 pixels), pour proposer au client un guide à suivre et l’aider à mieux comprendre la version d’évaluation, dont l’utilisation des fonctionnalités de votre offre et la compréhension des scénarios qui présentent les avantages.
  - **Nom** (obligatoire)
  - **URL (YouTube ou Vimeo uniquement)** (obligatoire)
  - **Miniature (533 x 324 px)** : L’image doit être au format .PNG.

Sélectionnez **Enregistrer** après avoir renseigné ces champs.

## <a name="publish"></a>Publish

#### <a name="submit-offer-to-preview"></a>Soumettre l’offre en préversion

Une fois que vous avez rempli toutes les sections nécessaires de l’offre, sélectionnez **Publier** en haut à droite du portail. Vous serez redirigé vers la page **Vérifier et publier**. 

S’il s’agit de votre première publication de cette offre, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre.
    - *Non démarrée* : cela signifie que la section n’a pas encore été touchée et doit être complétée.
    - *Incomplète* : cela signifie que la section contient des erreurs qui doivent être corrigées, ou qui nécessitent plus d’informations. Veuillez revenir à la ou les sections et mettez-les à jour.
    - *Complète* : cela signifie que la section est terminée, toutes les données requises ont été renseignées, et qu’il n’y a aucune erreur. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres notes supplémentaires utiles pour comprendre l’application.
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail pour vous faire savoir si une version d’essai de l’offre est disponible pour vérification et approbation. Vous devez revenir à Partner Center et sélectionner **Publier** pour que votre offre soit publiée pour le public (ou au public privé, si l’offre est privée).

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale existante](./update-existing-offer.md)
