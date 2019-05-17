---
title: Créez une offre SaaS dans la place de marché Commercial
description: Comment créer un nouveau logiciel en tant qu’une offre de Service (SaaS) pour répertorier ou de vente dans la place de marché Azure, AppSource, ou via le programme fournisseur de solutions Cloud (CSP) à l’aide du portail de la place de marché Commercial sur Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 32537426b7b9b1a7015610fc0c3e2dd7c3efa49b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806205"
---
# <a name="create-a-new-saas-offer"></a>Créez une offre SaaS

Pour commencer à créer des logiciels que l’offre de Service (SaaS), assurez-vous que vous avez premier [créer un compte espace partenaires](./create-account.md) et ouvrez le [tableau de bord de la place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), avec le **offre** onglet sélectionné. 

![Tableau de bord Marketplace commercial sur les partenaires](./media/commercial-marketplace-offers.png)

Sélectionnez le + **créer un nouveau...** bouton, puis sélectionnez le **logiciel en tant que Service** élément de menu. 

Si vous sélectionnez un des autres types d’offre, vous êtes redirigé vers l’ancien [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/).  Seules les offres SaaS sont disponibles dans le portail de la place de marché Commercial sur les partenaires pour l’instant. 

![Créer la fenêtre de l’offre sur les partenaires](./media/new-offer.png)


Le **nouvelle offre** boîte de dialogue s’affiche. ![Boîte de dialogue Nouvelle offre](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>ID d’offre et le nom

- **ID de l’offre** : Créer un identificateur unique pour chaque offre dans votre compte. Cet ID sera visible aux utilisateurs dans l’adresse URL de l’offre de la place de marché et des modèles Azure Resource Manager (le cas échéant). ID d’offre doit être en minuscules, d’alphanumériques (y compris des traits d’union et des traits de soulignement, mais aucun espace blanc). Cela est limité à 50 caractères et ne peut pas être mis à jour une fois que vous sélectionnez Créer.  
Exemple : test-offre-1
<br>Ce qui entraîne l’URL : `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Nom de l’offre** : Le nom officiel de votre offre d’application SaaS, cohérent entre les publications, des publications et des sites web.  Ce nom peut être marques.  Offrir le nom ne doit pas contenir un espace blanc, emojis (sauf s’ils sont le symbole de marque ou de droits d’auteur) et doit être limité à 50 caractères.
<br>Exemple : Offre de test 1&#8482;

Sélectionnez **Créer**.  Un **offrent la vue d’ensemble** page est créée pour cette offre.  

![Vue d’ensemble de l’offre sur les partenaires](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

Le **offrent la vue d’ensemble** page inclut : 

- Le **l’état de publication** affiche une représentation visuelle des étapes requises pour publier cette offre et combien de temps requises pour effectuer chaque étape. Icônes d’étape de publication incomplète seront grisés. 

- Le **offrent la vue d’ensemble** menu contient une liste de liens pour effectuer des opérations sur cette offre. Cette liste d’opérations changent en fonction de la sélection que vous effectuez pour votre offre.  
    - Si l’offre est un brouillon – Delete brouillon 
    - Si l’offre est publiée – arrêt vendre offre 
    - Si l’offre est disponible en version préliminaire – prêts à l’emploi 
    - Si vous n’avez pas effectué le serveur de publication de déconnexion : annuler la publication

## <a name="offer-setup"></a>Programme d’installation offre

Le **offrent le programme d’installation** onglet demande les informations suivantes. Sélectionnez **enregistrer** après avoir effectué ces champs.

- **Vous souhaitez vendre via Microsoft ?** (Oui/non)
    - **Oui**, vous souhaitez vendre votre offre via Microsoft, avec des transactions de la place de marché en votre nom ; d’hébergement Microsoft ou 
    - **Ne**, vous préférez simplement répertorier votre offre via les places de marché, traitement des transactions monétaires indépendamment de Microsoft.    

### <a name="sell-through-microsoft"></a>Vendre via Microsoft

Vente via Microsoft fournit une meilleure découverte des clients et acquisition, permet à Microsoft de transactions de la place de marché d’hôte à votre place et tire parti des fonctionnalités de commerce globalement disponibles de Microsoft.

#### <a name="saas-offer-requirements"></a>Exigences SaaS offre

Pour répertorier les logiciels que celle proposée par un Service (SaaS) avec la place de marché Commercial sur les partenaires, les critères suivants doivent être remplies :

- Votre offre doit être compatible avec les clients Azure. (Souvent les applications SaaS sont aussi hébergées sur Azure pour meilleures performances et la compatibilité, mais cela n’est pas obligatoire.) 
- Votre offre doit utiliser [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification et de gestion des identités.
- Votre offre doit utiliser [API de traitement des commandes SaaS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) à intégrer à la place de marché Azure.

#### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure
Pour les offres SaaS, vous, en tant que le serveur de publication devez tenir compte de frais d’utilisation de l’infrastructure Azure et les frais de licence de logiciel en tant qu’un élément de coût unique. Ce coût est représenté comme un tarif mensuel fixe pour le client. Utilisation de l’infrastructure Azure est gérée et facturée à vous, le partenaire, directement. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle. 

Logiciel de frais de licence sont présentées sous la forme d’un tarif forfaitaire d’abonnement mensuel, périodique sur site et ne sont pas limitées ou basés sur une consommation.

|**Coût de votre licence**|**100 $ par mois**|
|:---|:---|
|Coût d’utilisation Azure (D1/1 cœur)|Facturé directement à l’éditeur au lieu du client|
|Client est facturé par Microsoft|100,00 $ par mois (le serveur de publication doit tenir compte pour les coûts d’infrastructure déterminés ou directe dans les frais de licence)|

- Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.
- Les partenaires qui ont qualifié pour le **réduite frais liés au Service place de marché** verront un tarif réduit de transaction sur le SaaS offre à partir de 2019 mai jusqu'à juin 2020. Dans ce scénario, Microsoft facture 100,00 $ à votre licence du logiciel et paie out $90,00 au serveur de publication.

> [!NOTE]
> **Réduit les frais liés au Service place de marché**: Pour certains SaaS offre que vous avez publié sur notre place de marché Commercial, Microsoft sera réduire ses frais liés au Service place de marché de 20 % (comme décrit dans le contrat d’éditeur Microsoft) à 10 %. Dans l’ordre de votre offre qualifier, au moins un de vos offres doit avoir été désigné par Microsoft comme étant IP vente conjointe prêt ou vente conjointe IP par ordre de priorité.  Éligibilité à respecter au moins cinq (5) jours ouvrés avant la fin de chaque mois du calendrier afin de recevoir ces frais de Service place de marché réduit pour le mois.  Les frais de place de marché réduit ne s’applique pas aux machines virtuelles, les applications gérées ou d’autres produits mis à disposition via notre place de marché Commercial.  Les frais de place de marché réduit sera uniquement disponible pour les offres qualifiés pour les frais de licence collectées par Microsoft entre le 31 mai 2019 et le 30 juin 2020.  Après cette date, les frais de place de marché renvoie à son nombre normal. 

|**Microsoft facture**|**100 $ par mois**|
|:---|:---|
|Microsoft vous verse 80 % des revenus générés par les licences <br>**Pour les applications SaaS qualifiées, Microsoft paie 90 % de vos coûts de licence*|80 $/mois <br>*$* 90,00 par mois *|


#### <a name="csp-program-opt-in"></a>Fournisseur de services cryptographiques programme Opt-in
Le [fournisseur de solutions Cloud (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programme permet des offres de logiciels d’atteindre des millions de clients Microsoft qualifiés avec un minimum investissement de vente et marketing.

- **Canaux : Pour que mon offre disponible dans le programme CSP** (case à cocher)

Permet de proposer votre offre dans le programme CSP permet de fournisseurs de solutions de Cloud pour vendre votre produit dans le cadre d’une solution groupée à leurs clients. 

### <a name="list-through-microsoft"></a>Liste à l’aide Microsoft

Promouvoir votre entreprise avec Microsoft en créant une annonce de la place de marché. Sélection liste votre offre uniquement et pas réaliser via Microsoft signifie que Microsoft ne participer directement aux transactions de licence de logiciel. Aucun frais de transaction associée est et le serveur de publication conserve 100 % de n’importe quel logiciel collectées auprès du client des frais de licence. Toutefois, le serveur de publication est responsable de la prise en charge de tous les aspects de la transaction de licence de logiciel, y compris mais sans limitation : ordre de traitement, de contrôle, la facturation, facturation, paiement et collection. 

- **Comment voulez-vous que les clients potentiels pour interagir avec cette offre de la liste ?**

##### <a name="get-it-now-free"></a>Obtenez-le maintenant (gratuit)
Liste de gratuitement votre offre aux clients en fournissant une URL valide (commençant par http ou https), où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Évaluation gratuite
Répertorier votre offre aux clients sur une base d’évaluation gratuite en fournissant une URL valide (commençant par http ou https), où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Me contacter
Collecter les informations de contact client en vous connectant à votre système de gestion de relation client (CRM). Le client sera demandé pour l’autorisation de partager leurs informations. Ces détails du client, ainsi que le nom de l’offre, un ID et une source de la place de marché où ils trouvent votre offre, seront envoyés au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre système CRM, consultez [gestion des prospects Connect](#connect-lead-management). 

## <a name="enable-a-test-drive"></a>Activer une version d’évaluation

Une version d’évaluation est un excellent moyen de présenter votre offre aux clients potentiels en leur donnant la possibilité de « essayer avant d’acheter », ce qui entraîne une conversion accrue et la génération de prospects hautement qualifiés. [En savoir plus sur les versions d’évaluation.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Activer une version d’évaluation** (case à cocher) 

En activant la version d’évaluation, vous devrez configurer un environnement de démonstration pour les clients de tester votre offre pour une période fixe. 

### <a name="type-of-test-drive"></a>Type de version d’évaluation

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: Un modèle de déploiement qui contient toutes les ressources Azure qui composent votre solution. Les produits qui correspondent à ce scénario utilisent uniquement les ressources Azure.
- **[Dynamics 365 pour Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Microsoft héberge et gère le service de lecteur de test (y compris l’approvisionnement et le déploiement) pour un système de planification des ressources d’entreprise Business Central (finance, opérations, chaîne d’approvisionnement, CRM, etc..).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Microsoft héberge et gère le service de lecteur de test (y compris l’approvisionnement et le déploiement) pour un système d’Engagement des clients (ventes, service, service de projet, service après-vente, etc.).  
- **[Dynamics 365 pour les opérations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Microsoft héberge et gère le service de lecteur de test (y compris l’approvisionnement et le déploiement) pour un Finance and Operations système enterprise resource planning (finance, opérations, fabrication, la chaîne logistique, etc.). 
- **[Application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Un modèle de déploiement englobant toutes les architectures de solution complexe. Tous les produits personnalisés doivent utiliser ce type de version d’évaluation.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Un lien incorporé à un tableau de bord personnalisé. Produits qui veulent démontrer qu'un visuel Power BI interactive doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

#### <a name="additional-test-drive-resources"></a>Ressources de lecteur de test supplémentaires
- [Meilleures pratiques de test lecteur techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Version d’évaluation des meilleures pratiques de commercialisation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tester l’une pagineur vue d’ensemble de lecteur](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Se connecter à la gestion des prospects

Se connecter avec les clients directement par le référencement de votre offre dans les places de marché et la raccorder à votre système de gestion de relation client (CRM) afin que vous pouvez recevoir des informations de contact client immédiatement après un client demande expressément ou déploie votre produit.

- **Choisir une destination de prospect** (menu déroulant) : Fournissent des informations de connexion pour le système CRM où vous souhaitez que nous envoyer des prospects. 

Partenaires prend en charge les systèmes CRM suivants pour la gestion des prospects. Sélectionnez le lien pour obtenir des instructions d’installation.

- E-mail de contact Azure Blob : fournissent, nom du conteneur et chaîne de connexion de compte de stockage. 
- [Table Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – fournir l’e-mail de contact et de la chaîne de connexion de compte de stockage. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – fournir l’e-mail de contact, l’URL et mode d’authentification (Office 365 ou Azure Active Directory).
- [Point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – fournir l’e-mail de contact et les URL de point de terminaison HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – fournir l’e-mail de contact, formulaire ID, Munchkin ID de compte et ID de serveur.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -fournir l’e-mail de contact et les ID d’organisation. 

#### <a name="additional-lead-management-resources"></a>Ressources de gestion des prospects supplémentaires
- [Forum aux questions de gestion des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erreurs courantes de configuration de prospect](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Entraîner une radiomessagerie vue d’ensemble de gestion](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

N’oubliez pas de **enregistrer** avant de passer à la section suivante !

## <a name="properties"></a>properties
Le **propriétés** onglet vous invite à définir les abscisses et les secteurs d’activité utilisées pour regrouper votre offre sur les places de marché, les contrats prenant en charge votre offre et votre version de l’application. 

Sélectionnez **enregistrer** après avoir effectué ces champs. 

### <a name="category"></a>Catégorie
Sélectionnez un minimum d’un (1) et un maximum de trois (3) les catégories utilisées pour regrouper votre offre dans les zones de recherche de la place de marché approprié. Énoncer de comment votre offre prend en charge ces catégories dans la description de l’offre. 

### <a name="industry"></a>Secteur d'activité
Sélectionnez jusqu'à deux (2) les secteurs utilisés pour le regroupement de votre offre dans les zones de recherche de la place de marché approprié. Si votre offre n’est pas spécifique à un secteur d’activité, ne sélectionnez pas un. Énoncer de comment votre offre prend en charge les industries sélectionnés dans la description de l’offre. 

### <a name="app-version"></a>Version de l’application
Il s’agit d’un champ facultatif permettant d’identifier le numéro de version de votre offre dans la place de marché AppSource. 

### <a name="standard-contract"></a>Contrat standard
- **Utiliser le contrat Standard ?** 

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique éditeurs de logiciels, Microsoft propose un modèle de contrat Standard afin de faciliter une transaction dans la place de marché. 

Au lieu d’élaborer des conditions générales personnalisées, les serveurs de publication Azure Marketplace peuvent choisir d’offrir leur logiciel contractuelles Standard, qui les clients doivent uniquement les antécédents et accepter qu’une seule fois. 

Le contrat Standard est disponible ici : https://go.microsoft.com/fwlink/?linkid=2041178.

##### <a name="terms-of-use"></a>Conditions d’utilisation
Si votre contrat de licence est différent du contrat Standard, vous pouvez choisir d’entrer vos propres conditions légales ici. Vous pouvez également entrer jusqu'à 10 000 caractères de texte dans ce champ. Si vos conditions d’utilisation requièrent une description plus longue, entrez un lien URL unique dans ce champ où vous pouvez trouver votre contrat de licence supplémentaires. Il affichera aux clients qu’un lien actif.

Les clients doivent accepter ces conditions avant de pouvoir essayer de votre application. 

N’oubliez pas de **enregistrer** avant de passer à la section suivante !

## <a name="offer-listing"></a>Annonce de l’offre
Les offres onglet affiche les langages (et les marchés) où votre offre est disponible, actuellement anglais (États-Unis) est le seul emplacement disponible. En outre, cette page affiche l’état de la liste spécifique à la langue et la date/heure qui il a été ajouté. Vous devez définir les détails de la place de marché (offrent les nom, description, les termes de recherche, etc.) pour chaque langue / mise sur le marché.

### <a name="offer-listings"></a>Liste des offres
Fournissent des informations à afficher dans la place de marché, notamment des descriptions de votre offre et des ressources marketing.

- **Nom** (obligatoire) : Le nom défini ici s’affiche comme titre de vos offres sur la marketplace(s) que vous avez choisi. Le nom est prérempli en fonction de votre précédente **nouvelle offre** entrée.  Cela peut être marques.  Cela ne doit pas contenir un espace blanc, emojis (sauf si elles sont les symboles de marque et le copyright) et doit être limité à 50 caractères.
- **Résumé** (obligatoire) : Fournir une brève description de votre offre pour être utilisés dans les résultats de recherche de la place de marché annonces. Jusqu'à 100 caractères de texte peuvent être entrées dans ce champ.
- **Description** (obligatoire) : Fournissez une description de votre offre à afficher dans la vue d’ensemble des annonces de la place de marché. Vous pouvez inclure une proposition de valeur, les principaux avantages, les associations de catégorie ou du secteur, opportunités d’achat dans l’application, toutes requises divulgations, ainsi qu’un lien pour en savoir plus.
Jusqu'à 3 000 caractères de texte peuvent être entrées dans ce champ. Pour obtenir des conseils supplémentaires, consultez l’article [écrire une description de l’application proprement](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Rechercher des mots clés**: Entrez jusqu'à trois mots clés de recherche utilisables par les clients pour trouver votre offre dans le marketplace(s).
- **Instructions de mise en route** (obligatoire) : Explique comment configurer et commencer à utiliser votre application pour les clients potentiels.  Ce démarrage rapide peut contenir des liens vers la documentation en ligne plus détaillée. Jusqu'à 3 000 caractères de texte peuvent être entrées dans ce champ. 

#### <a name="links"></a>Liens

- **Politique de confidentialité** (obligatoire) : Lien vers la politique de confidentialité de votre organisation. Vous êtes chargé de s’assurer de que votre application est conforme aux réglementations et lois sur la confidentialité et pour fournir une politique de confidentialité valide
- **Supports Marketing du programme CSP** (facultatif) : Vous devez fournir un lien vers les documents de marketing, si vous choisissez d’étendre votre offre dans le [fournisseur de solutions Cloud (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programme. CSP étend votre offre à une large gamme de clients éligibles en activant les partenaires CSP à regrouper, marché et revendre votre offre. Ces revendeurs devez accéder à des documents marketing de votre offre. Pour plus d’informations, consultez [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Liens utiles** (facultatif) : Des documents en ligne supplémentaires facultatives sur votre application ou les services connexes répertoriés en fournissant un **titre** et **URL**. Ajouter des liens utiles supplémentaires en cliquant sur **+ ajouter une URL**.

#### <a name="contact-information"></a>Informations de contact

- **Contacts**: Pour chaque contact client, fournir un employé **nom** , **numéro de téléphone**, et **E-mail** adresse.  (Ces *ne sera pas* affichées publiquement). Un **URL du support technique** est également requise pour le **Contact de Support** groupe.  (Ces informations *sera* affichées publiquement).

**Prise en charge de** (obligatoire) : Pour les questions de prise en charge générale.

**Contact d’ingénierie** (obligatoire) : Pour les questions techniques.

**Contact du Gestionnaire de canal** (obligatoire) : Pour les questions de revendeur relatives au programme CSP.

#### <a name="files-and-images"></a>Fichiers et des Images

- **Documents** (obligatoire) : Ajouter des documents marketing connexes pour votre offre, au format PDF, en fournissant un minimum d’un (1) et maximum de trois (3) les documents par offre.
- **Images** (facultatif) : Il existe plusieurs emplacements où les images de logo de votre offre peuvent apparaître dans le marketplace(s) nécessitant les tailles suivantes--petit : 48 x 48 pixels _(obligatoire),_ moyenne : 90 x 90 pixels, grande : 216 x 216 pixels _(obligatoire),_ large : 255 x 115 pixels et bannière : 815 x 290 pixels. Toutes les images doivent être dans. Format PNG.
- **Captures d’écran** (obligatoire) : Ajouter des captures d’écran illustrant votre offre. Un maximum de cinq (5) des captures d’écran peut-être être ajouté et doivent donc être dimensionné à 1280 x 720 pixels. Toutes les images doivent être dans. Format PNG.
- **Vidéos** (facultatif) : Ajouter des liens vers les vidéos présentant de votre offre. Vous pouvez utiliser des liens de vidéos YouTube et/ou Vimeo, qui seront présentées aux clients en même temps que votre offre. Vous devez également entrer une image miniature de la vidéo, en taille réelle des pixels de 1280 x 720 au format PNG. Vous pouvez afficher un maximum de quatre vidéos par offre.

N’oubliez pas de **enregistrer** avant de passer à la section suivante !

#### <a name="additional-marketplace-listing-resources"></a>Listage des ressources supplémentaires de la place de marché

- [Meilleures pratiques pour la place de marché offrent des annonces](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)



## <a name="preview"></a>VERSION PRÉLIMINAIRE

Le **aperçu** onglet vous permet de définir une limitée **aperçu public** pour libérer votre offre avant de publier votre offre en direct à l’audience plus large de la place de marché.

> [!IMPORTANT]
> Vous devez sélectionner **lancés** avant que votre offre sera diffusé en direct à l’audience publique de la place de marché après avoir vérifié votre offre en version préliminaire.

- **Définir un public Preview : Ajouter un e-mail du compte AAD/MSA unique par ligne, ainsi qu’une description facultative.**

Ajouter manuellement des adresses de messagerie jusqu'à dix (10) ou vingt (20) si le chargement d’un fichier CSV, pour le compte existant Microsoft (MSA) ou des comptes Azure Active Directory (AAD) pour faciliter la validation de votre offre avant la publication en direct. En ajoutant ces comptes, vous définissez une audience pourra y avoir accès à votre offre avant sa publication dans le marketplace(s). Si votre offre est déjà en ligne, vous pouvez toujours définir un public preview pour tester les modifications ou les mises à jour à votre offre.

> [!NOTE]
> Le public preview diffère d’un public privé. Un public preview est autorisé à accéder à votre offre _préalable_ à être diffusé en direct dans les places de marché. Vous pouvez également choisir de créer un plan et le rendre disponible uniquement à un public privé. Dans le **liste des prévisions** onglet, vous pouvez définir une audience privée avec le **il s’agit d’un plan privé** case à cocher. Vous pouvez définir un public privé de jusqu'à 20 000 clients à l’aide des ID de locataire Azure.

## <a name="technical-configuration"></a>Configuration technique

Le **configuration technique** onglet définit les détails techniques (chemin d’URL webhook, ID de locataire et ID d’application) utilisés pour se connecter à votre offre. Cette connexion permet de configurer votre offre en tant que ressource dans l’abonnement du client Azure s’ils choisissent de l’acquérir.

- **URL de la page d’accueil** (obligatoire) : Définir le site URL que les clients seront dirigés arrivent sur après l’acquisition de votre offre à partir de la place de marché. Cette URL sera également le point de terminaison qui recevra les API de connexion afin de faciliter le commerce avec Microsoft.

- **Connexion webhook** (obligatoire) : Pour tous les événements asynchrones que Microsoft doit vous envoyer au nom du client (exemple : Abonnement Azure est devenu non valide), nous vous demandons de fournir un webhook de la connexion. Si vous ne disposez pas d’un système de webhook en place, la configuration la plus simple consiste à disposer d’une application de logique de point de terminaison HTTP qui écoute les événements en cours de publication à ce dernier et puis traiter de manière appropriée (par exemple, https://prod-1westus.logic.azure.com:443/work). Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID de locataire Azure AD** (obligatoire) : Dans le portail Azure, nous avons besoin que vous [créer une application Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) afin que nous puissions valider la connexion entre nos deux services se trouve derrière une communication authentifiée. Pour rechercher le [id_locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), accédez à votre Azure Active Directory et sélectionnez **propriétés**, puis recherchez le **ID de répertoire** nombre répertorié (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID d’application Azure AD** (obligatoire) : Vous devez également votre [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) et une clé d’authentification. Pour obtenir ces valeurs, accédez à votre Azure Active Directory et sélectionnez **inscriptions**, puis recherchez le **ID d’Application** nombre répertorié (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Pour trouver la clé d’authentification, accédez à **paramètres** et sélectionnez **clés**. Vous devez fournir une description et la durée et le sera ensuite être fourni une valeur numérique.

 Notez que l’ID d’application Azure est associée à votre ID d’éditeur, donc vous assurer que le même ID d’application est utilisé dans toutes vos offres.

## <a name="plan-overview"></a>Vue d’ensemble du plan

Le **vue d’ensemble du Plan** onglet vous permet de fournir une variété d’options de plan dans la même offre. Ces plans (parfois appelées comme deux références SKU) peuvent différer en termes de version, de monétisation ou de niveaux de service. Vous devez configurer au moins un plan avant de pouvoir vendre votre offre dans la place de marché.

Une fois créé, vous verrez vos noms de plan, ID, modèles, de disponibilité (Public ou privé), la tarification actuelle de publication d’état et les actions disponibles.

-   **Actions** disponibles dans le **vue d’ensemble du Plan** varient selon l’état actuel de votre plan et peut inclure :
  - Si l’état du plan est **Draft** – supprimer le brouillon
  - Si l’état du plan est **Live** – arrêt vendre plan ou audience privé de synchronisation

**Créer un plan** (au moins un plan pour ceux qui permet de vendre via Microsoft)

- **ID du plan :** Créer un ID de plan unique pour chaque plan dans cette offre. Cet ID sera visible par les clients dans les modèles produit URL et Azure Resource Manager (le cas échéant). Utilisez uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. Un maximum de 50 caractères sont autorisés pour cet ID du plan. Notez que l’ID ne peut pas être modifiée une fois en sélectionnant créer.
- **Nom du plan :** Les clients verront ce nom lorsque vous choisissez le plan à sélectionner dans votre offre. Créer un nom de l’offre unique pour chaque plan dans cette offre. Le nom du plan est utilisé pour différencier les plans de logiciel qui peuvent faire partie de l’offre même (ex :) Nom de l'offre : Windows Server ; plans : Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Liste de plan

Le **liste des prévisions** onglet affiche les langues (et les marchés) où votre plan est disponible, actuellement anglais (États-Unis) est le seul emplacement disponible. En outre, cette page affiche l’état de la liste spécifique à la langue et la date/heure qui il a été ajouté. Vous devez définir les détails de la place de marché (offrent les nom, description, les termes de recherche, etc.) pour chaque langue / mise sur le marché.

#### <a name="plan-listing-details"></a>Planifier les détails de l’annonce

Sélectionnez l’une des langues de plan pour afficher le **planifier la liste** plus d’informations, y compris **nom** et **Description.**

- **Nom** : Préremplies en fonction de la version d’évaluation **nouveau plan** entrée et s’affiche sous le titre de votre d’offre « logiciel plan » affiché dans la place de marché.
- **Description :** Cette description est une possibilité d’expliquer ce qui rend ce plan logiciel unique et toutes les différences à partir d’autres plans de logiciels au sein de votre offre. Peut contenir jusqu'à 500 caractères.

Sélectionnez **enregistrer** après avoir effectué ces champs.

#### <a name="plan-pricing-and-availability"></a>Plan de tarification et disponibilité

Le **tarification et disponibilité** onglet vous permet de configurer les marchés ce plan sera disponible dans, le modèle de monétisation souhaité, le prix et facturation terme. En outre, vous pouvez indiquer s’il faut rendre le plan visible à tous les utilisateurs ou uniquement pour des clients spécifiques (un public privé).

#### <a name="markets"></a>Marchés

- **Modifier les marchés** (facultatif)

Chaque plan doit être disponible dans au moins une mise sur le marché. Sélectionnez la case à cocher pour n’importe quel emplacement sur le marché où vous souhaitez proposer ce plan. Une zone de recherche et un bouton de sélection des pays « Tax Remitted », dans lequel Microsoft renvoie taxe de vente et de votre part, sont inclus afin d’aider. 


Si vous avez déjà défini les prix pour votre plan dans United States Dollars (USD) et que vous ajoutez un autre emplacement sur le marché, le prix pour le nouveau marché est calculé en fonction des taux de change. Vous devez toujours examiner le prix pour chaque marché avant la publication. Tarification peut être consultée à l’aide du lien « Exporter les prix (xlsx) » après avoir enregistré vos modifications.

#### <a name="pricing"></a>Tarifs

- **Modèle de tarification** : Tarification forfaitaire ou siège en fonction

**Tarification forfaitaire :** Activer l’accès à votre offre avec un tarif unique prix mensuel ou annuel. Cela est parfois appelé tarification basée sur le site.

**Siège en :** Activer l’accès à votre offre avec le prix selon le nombre d’utilisateurs l’accès à l’offre ou occupant *sièges*. Ce modèle basé sur le siège vous permet de définir le minimum et le nombre maximal de sièges autorisés en fonction du prix. De cette façon, les points de prix différent peuvent être configurés en fonction du nombre d’utilisateurs en configurant plusieurs plans.  Ces champs sont facultatifs. Si vide, le nombre de sièges sera interprété comme n’ayant ne pas une limite (minimum 1) et maximum autant que le système peut prendre en charge. Ces champs peuvent être modifiés dans le cadre d’une mise à jour à votre plan.

Une fois publié, le choix de modèle de tarification facturation ne peut pas être modifié. En outre, tous les plans de l’offre même doivent partager le même modèle de tarification.

- **Terme de facturation**: Mensuel ou annuel

Sélectionnez la fréquence à laquelle les clients doivent payer le prix indiqué. Au moins un mensuel ou annuel au prix doit être fourni, ou les deux options peuvent être rendues disponibles aux clients.

- **Prix**: USD par mois ou USD par an

Les tarifs de jeu dans la devise locale (USD = Dollar américain) sont convertis dans la devise locale de tous les marchés à l’aide du taux de change disponibles pendant l’installation. Valider ces tarifs avant la publication en exportant la feuille de calcul de tarification et consulter le prix dans chaque marché. Si vous souhaitez définir le prix personnalisés dans un marché individuel, modifier et importer la feuille de calcul de tarification. Vous êtes responsable pour valider cette tarification et que vous possédez ces paramètres.
**Vous devez tout d’abord enregistrer vos modifications de tarification pour activer l’exportation de données de tarification.*

Passez en revue vos prix soigneusement avant la publication, car il existe certaines restrictions sur ce qui peut changer après la publication d’un plan :

- Une fois qu’un plan est publié, le modèle de tarification ne peut pas être modifié.
- Une fois qu’un terme de facturation est publié pour un plan, il ne peut pas être supprimée ultérieurement.
- Une fois un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement.

### <a name="plan-audience"></a>Plan de public

Vous avez la possibilité de configurer chaque plan pour être visibles à tous les utilisateurs ou uniquement une audience spécifique de votre choix. Vous pouvez affecter l’appartenance à ce public limité à l’aide des ID de locataire Azure AD.

#### <a name="privacy"></a>Confidentialité

- **Il s’agit d’un plan privé** (case à cocher facultatif)

Cochez cette case pour rendre votre plan privé et visible uniquement pour l’audience restreint de votre choix. Une fois publié en tant qu’un plan privé, vous pouvez mettre à jour l’audience ou choisir de rendre le plan disponible pour tout le monde. Une fois un plan publié comme visibles par tout le monde, il doit rester visible pour tout le monde. (Le plan ne peut pas être configuré comme un plan privé à nouveau).

- **Restreint Audience (ID client)**

Affecter l’audience qui auront accès à ce plan privé. L’accès est affecté à l’aide des ID de locataire avec l’option pour inclure une description de chaque ID de locataire assigné. ID de locataire 10 au maximum peut être ajouté, ou ID de locataire 20 000 clients si vous importez un fichier de feuille de calcul .csv.

Un locataire est une représentation sous forme d’une organisation, dont l’ID est représenté sous la forme d’un GUID (Identificateur Global Unique, un nombre entier de 128 bits utilisé pour identifier les ressources). Il s’agit d’une instance Azure AD dédiée, reçue par une organisation ou un développeur d’applications lorsque l’organisation ou le développeur d’applications crée une relation avec Microsoft (l’inscription à Azure, Microsoft Intune ou Microsoft 365, par exemple). Chaque client Azure AD est distinct et indépendant des autres clients Azure AD. Pour vérifier le locataire, connectez-vous au portail Azure avec le compte que vous souhaitez utiliser pour gérer votre application. Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous pouvez voir son nom directement sous le nom de votre compte. Dans l’angle supérieur droit du Portail Azure, survolez le nom de votre compte avec la souris pour voir votre nom, votre adresse e-mail, votre répertoire et votre ID de locataire (un GUID), ainsi que votre domaine. Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID. Vous pouvez également rechercher des ID de locataire de votre organisation à l’aide d’une URL de nom de domaine au niveau : [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

Tandis que les offres SaaS utilisent des ID de locataire pour définir un public privé, autres types d’offre peuvent utiliser des ID d’abonnement Azure (qui sont également représentés sous forme de GUID).

> [!NOTE]
> L’audience privé (ou audience restreint) diffère d’un public preview. Dans le **[aperçu](#preview)** onglet, vous pouvez définir un public preview. Un public preview est autorisé à accéder à votre offre *préalable* à l’offre qui sont publiée en direct dans la place de marché. La désignation privée audience s’applique uniquement à un plan spécifique, l’audience de l’aperçu peut afficher tous les plans (privé ou non), mais seulement pendant la période d’évaluation limitée pendant que le plan est testé et validé.


## <a name="test-drive"></a>Test drive

Le **testez** onglet vous permet de configurer une démonstration (ou « tester ») qui permettront aux clients d’essayer votre offre avant de les valider pour l’acheter. En savoir plus, consultez l’article [What ' s Test Drive ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Si vous ne souhaitez plus fournir une version d’évaluation de votre offre, revenez à la **[offrent le programme d’installation](#offer-setup)** page, puis décochez **activation du lecteur de test**.

### <a name="technical-configuration"></a>Configuration technique
Les types suivants de la version d’évaluation sont disponibles, chacun avec ses propres exigences techniques de configuration.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Application logique](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuration technique non requise)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Version d’évaluation de configuration technique pour Azure Resource Manager

Un modèle de déploiement qui contient toutes les ressources Azure qui composent votre solution. Les produits qui correspondent à ce scénario utilisent uniquement les ressources Azure. En savoir plus sur la configuration d’un [version d’évaluation Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régions** (obligatoire) : Il existe actuellement 26 régions Azure pris en charge où votre version d’évaluation peut être rendue disponible. En règle générale, vous devez rendre votre version d’évaluation disponible dans les régions où vous prévoyez le plus grand nombre de clients, afin qu’ils peuvent sélectionner la région la plus proche pour des performances optimales. Vous devez vous assurer que votre abonnement est autorisé à déployer toutes les ressources nécessaires dans chacune des régions que vous sélectionnez.

- **Instances** : Sélectionnez le type (à chaud ou à froid) et le nombre d’instances disponibles, qui seront multipliés par le nombre de régions où votre offre est disponible.

**Hot**: Ce type d’instance est déployé et en attente de l’accès par région sélectionnée. Les clients peuvent accéder instantanément aux *chaud* instances d’une version d’évaluation, plutôt que de devoir patienter pendant un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir au moins une *chaud* de l’instance, comme la plupart des clients ne souhaitez pas attendre pour les déploiements complets, ce qui entraîne une remise dans l’utilisation du client si aucun *chaud* instance n’est disponible.

**À froid**: Ce type d’instance représente le nombre total d’instances qui peuvent éventuellement être déployées par région. L’ensemble du modèle de Test Drive Resource Manager pour déployer lorsqu’un client demande la version d’évaluation, par conséquent, les instances à froid nécessitent *à froid* instances sont beaucoup plus lentes à charger *chaud* instances. L’inconvénient est que vous avez uniquement à payer pour la durée de la version d’évaluation, il est *pas* toujours en cours d’exécution sur votre abonnement Azure en tant qu’avec un *chaud* instance.

- **Modèle Azure Resource Manager de lecteur test**: Téléchargez le fichier .zip qui contient votre modèle Azure Resource Manager.  En savoir plus sur la création d’un modèle Azure Resource Manager dans l’article de démarrage rapide [créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durée du test drive** (obligatoire) : Entrez la durée pendant laquelle le Test Drive restera actif, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période. Cette durée peut déterminer uniquement l’ensemble d’un nombre entier d’heures (par exemple) heures de « 2 », « 1.5 » n’est pas valide).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Version d’évaluation de configuration technique pour Dynamics 365

Microsoft peut supprimer la complexité de la configuration d’une version d’évaluation par l’hébergement et la maintenance de l’approvisionnement du service et le déploiement à l’aide de ce type de version d’évaluation. La configuration pour ce type de version d’évaluation hébergé est le même quel que soit l’indique si la version d’évaluation ciblée par une audience Business Central, Customer Engagement ou d’opérations.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) : Définir le nombre maximal de clients que vous pouvez utiliser votre version d’évaluation en même temps. Chaque utilisateur simultanée consommera une licence Dynamics 365, tandis que la version d’évaluation est active, donc vous devrez vous assurer que vous disposez de suffisamment de licences disponibles pour prendre en charge la limite maximale définie. La valeur recommandée est de 3 à 5.

- **Durée du test drive** (obligatoire) : Entrez la durée pendant laquelle le Test Drive restera actif en définissant le nombre d’heures. Après ce nombre d’heures, la session mettra fin et ne plus l’utiliser une de vos licences. Nous vous recommandons une valeur de 2 à 24 heures selon la complexité de votre offre. Cette durée peut déterminer uniquement l’ensemble d’un nombre entier d’heures (par exemple) heures de « 2 », « 1.5 » n’est pas valide).  L’utilisateur peut demander une nouvelle session si elles sont exécutées plus assez de temps et d’accéder à nouveau de la version d’évaluation.

- **URL de l’instance** (obligatoire) : L’URL où le client commencera à leur version d’évaluation. En général, l’URL de votre instance Dynamics 365 exécutant votre application avec les exemples de données installés (par exemple, https://testdrive.crm.dynamics.com).

- **URL de l’API Web de l’instance** (obligatoire) : Récupérer l’URL de l’API Web pour votre instance Dynamics 365 en vous connectant à votre compte Microsoft 365 et en accédant à **paramètres** \&gt ; **Personnalisation** \&gt ; **Ressources pour les développeurs** \&gt ; **API Web (URL racine du Service) de l’instance**, copiez l’URL disponible ici (par exemple, https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nom du rôle** (obligatoire) : Fournir le nom du rôle de sécurité que vous avez défini dans votre version d’évaluation de Dynamics 365 personnalisée. Cela sera attribué à l’utilisateur lors de leur version d’évaluation (par exemple, tester-défilant-rôle).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuration technique pour la version d’évaluation application logique

Tous les produits personnalisés doivent utiliser ce type test lecteur du modèle de déploiement qui englobe plusieurs architectures de solution complexe. Pour plus d’informations sur la configuration d’application logique versions d’évaluation, visitez [opérations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) sur GitHub.

- **Région** (liste déroulante obligatoire, à sélection unique) : Il existe actuellement 26 régions Azure pris en charge où votre version d’évaluation peut être rendue disponible. Les ressources pour votre application logique seront déployées dans la région que vous sélectionnez. Si votre application logique a toutes les ressources personnalisées stockées dans une région spécifique, assurez-vous que cette région est sélectionnée ici. La meilleure façon de procéder consiste à déployer votre application logique localement sur votre abonnement Azure dans le portail entièrement et de vérifier qu’il fonctionne correctement avant d’effectuer cette sélection.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) : Définir le nombre maximal de clients que vous pouvez utiliser votre version d’évaluation en même temps. Ces lecteurs sont déjà déployés, permettant aux clients d’y accéder instantanément, sans attendre un déploiement de test.

- **Durée du test drive** (obligatoire) : Entrez la durée pendant laquelle le Test Drive restera actif, en nombre d’heures. La version d’évaluation se termine automatiquement après la fin de cette période.

- **Nom de groupe de ressources Azure** (obligatoire) : Entrez le [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nom où la version d’évaluation de votre application logique est enregistrée.

- **Nom de l’application logique Azure** (obligatoire) : Entrez le nom de l’application logique qui affecte la version d’évaluation à l’utilisateur. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

- **Nom de l’application logique deprovision** (obligatoire) : Entrez le nom de l’application logique qui déprovisionne la version d’évaluation, une fois que le client est terminé. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuration technique non requise pour les versions d’évaluation Power BI

Produits qui veulent démontrer qu'un visuel Power BI interactive peut utiliser un lien incorporé pour partager un tableau de bord personnalisée en tant que leur version d’évaluation, aucune configuration supplémentaire ne technique requises. En savoir plus sur la configuration de[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) applications du modèle.

### <a name="deployment-subscription-details"></a>Détails de l’abonnement déploiement

Pour déployer la version d’évaluation de votre part, veuillez créer et fournir un abonnement Azure séparée et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (requis pour Azure Resource Manager et vos applications logiques) : Entrez l’ID d’abonnement pour accorder l’accès à vos services de compte Azure pour l’utilisation des ressources reporting et de facturation. Nous vous recommandons d’envisager [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour la version d’évaluation si vous n’en avez pas déjà. Vous pouvez trouver votre ID d’abonnement Azure en vous connectant à la [Azure portal](https://portal.azure.com/) et en accédant à la **abonnements** onglet du menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID de locataire Azure AD** (obligatoire) : Entrez votre Azure Active Directory (AD) [id_locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Pour rechercher cet ID, connectez-vous à la [portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, sélectionnez **propriétés** , puis recherchez le **ID de répertoire** nombre répertorié (par exemple) 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez également rechercher des ID de locataire de votre organisation à l’aide de votre URL de nom de domaine au niveau : [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Nom du locataire Azure AD** (requis pour Dynamics 365) : Entrez le nom de votre Azure Active Directory (AD). Pour rechercher ce nom, vous connecter à la [Azure portal](https://portal.azure.com/), dans le coin supérieur droit, le nom de votre locataire s’afficheront sous le nom de votre compte.

- **ID d’application Azure AD** (obligatoire) : Entrez votre Azure Active Directory (AD) [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Pour rechercher cet ID, connectez-vous à la [Azure portal](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, sélectionnez **inscriptions**, puis recherchez le **ID d’Application** nombre répertoriés (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé d’application Azure AD** (obligatoire) : Entrez votre Azure Active Directory (AD) [clé d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Pour rechercher cet ID, connectez-vous à la [Azure portal](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, sélectionnez **inscriptions** , puis sélectionnez **paramètres**  >  **Clés**.

N’oubliez pas de **enregistrer** avant de passer à la section suivante !

### <a name="test-drive-listings-optional"></a>Tester les exemples de lecteur (facultatifs)

Le **annonces de version d’évaluation** option se trouve sous le **testez** onglet affiche les langues (et les marchés) où votre version d’évaluation est disponible, actuellement anglais (États-Unis) est le seul emplacement disponible . En outre, cette page affiche l’état de la liste spécifique à la langue et la date/heure qui il a été ajouté. Vous devez définir les détails de lecteur de test (description, manuel de l’utilisateur, vidéos, etc.) pour chaque langue/marché.

- **Description** (obligatoire) : Décrivez votre version d’évaluation, ce qui seront présenté, objectifs pour l’utilisateur à faire des essais avec des fonctionnalités à Explorer et toute information pertinente pour aider l’utilisateur à déterminer si vous devez acquérir votre offre. Jusqu'à 3 000 caractères de texte peuvent être entrées dans ce champ. 

- **Accéder aux informations** (requis pour les versions d’évaluation Azure Resource Manager et logique) : Expliquez qu’un client a besoin de connaître pour accéder et utiliser cette version d’évaluation. Suivre un scénario d’utilisation de votre offre et exactement ce que le client doit savoir pour accéder aux fonctionnalités tout au long de la version d’évaluation. Jusqu'à 10 000 caractères de texte peuvent être entrées dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) : Une procédure pas à pas détaillée de votre expérience de test. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client pour obtenir la version d’évaluation ne surviendra et servir de référence pour toutes les questions auxquelles ils peuvent. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos : Ajouter des vidéos** (facultatif) : Vidéos peuvent être chargées sur YouTube ou Vimeo et référencées ici avec une image miniature et de lien (pixels de 533 x 324) afin qu’un client peut afficher une procédure pas à pas d’informations pour les aider à mieux comprendre la version d’évaluation, y compris comment utiliser correctement les fonctionnalités de votre proposer et comprendre les scénarios qui mettent en évidence leurs avantages.
  - **Nom** (obligatoire)
  - **URL (YouTube ou Vimeo uniquement)** (obligatoire)
  - **Miniature (533 x 324px)**: Fichier image doit être au format PNG.

Sélectionnez **enregistrer** après avoir effectué ces champs.

## <a name="publish"></a>Publish

#### <a name="submit-offer-to-preview"></a>Soumettre offrent afficher un aperçu

Une fois que vous avez terminé toutes les sections requises de l’offre, sélectionnez **publier** dans l’angle supérieur droit du portail. Vous serez redirigé vers le **révision et publier** page. 

S’il s’agit de votre première publication de cette offre, vous pouvez :

- Consultez l’état d’achèvement pour chaque section de l’offre.
    - *N’a ne pas démarré* : signifie que la section n’a pas été touchée et doit être effectuée.
    - *Incomplet* : signifie que la section comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations doit être fourni. Veuillez revenir en arrière à l’ou aux sections et mettez-le à jour.
    - *Complète* : signifie que la section est terminée, toutes les données requises ont été fournies, et il y a aucune erreur. Toutes les sections de l’offre doivent être dans un état terminé avant de pouvoir soumettre l’offre.
- Fournissent des instructions de tests à l’équipe de certification pour vous assurer que votre application est testée correctement, en plus des remarques supplémentaires utiles pour comprendre votre application.
- Soumettre l’offre pour la publication en sélectionnant **envoyer**. Nous vous enverrons un e-mail pour vous permettre de savoir quand une version d’évaluation de l’offre est disponible pour vous permettre de vérifier et approuver. Vous devez revenir au centre de partenaires et sélectionnez **Go-live** pour l’offre à publier votre offre dans le grand public (ou si une privée offre, à l’audience privé).

## <a name="next-steps"></a>Étapes suivantes

- [Mise à jour une offre existante dans la place de marché Commercial](./update-existing-offer.md)
