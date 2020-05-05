---
title: Exigences par type de liste | Azure
description: Cet article décrit les critères d’éligibilité et les exigences de publication pour les partenaires qui essaient de comprendre comment publier des applications dans la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684356"
---
# <a name="requirements-by-listing-type"></a>Exigences par type de liste  
Les exigences techniques et de contenu marketing varient en fonction de la vitrine, du type d’offre et du type de liste. Consultez les spécifications suivantes pour vous assurer que vous respectez les critères.  
1. Configuration requise pour la vitrine :  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Exigences par type de liste et type d’offre :  
    *   Pour plus d’informations sur les types de liste et les types d’offre, consultez la page Déterminer le type de référencement adapté à votre offre à l’adresse [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Configuration requise pour la vitrine : AppSource  
Le tableau suivant présente la configuration préalable requise pour la publication sur AppSource.  

| Condition requise | Détails | Requis ou recommandé |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Votre application doit permettre l’authentification unique fédérée Azure Active Directory avec activation du consentement.<ul> <li>Pour plus d’informations sur l’activation de l’authentification unique fédérée Azure AD, consultez la page Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory à l’adresse [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Obligatoire |   
| ***Intégration à Microsoft Cloud Services*** | Votre application doit pouvoir s’intégrer à d’autres services Microsoft Cloud tels que Microsoft Power BI, Cortana Intelligence ou aux services Microsoft Azure.<ul> <li>L’Internet des objets est un exemple de service Microsoft Cloud.</li> </ul> | Recommandé |  
| ***Public ciblé*** | Votre application doit être destinée aux utilisateurs métier et aux propriétaires d’entreprises. | Obligatoire | 
| ***Application SaaS (Software as a service) professionnelle*** | Votre application doit répondre aux exigences suivantes.<ul> <li>Une application SaaS métier</li> <li>Centrée sur les processus métier</li> <li>Destinée aux clients professionnels</li> <li>Permet aux utilisateurs d’utiliser leurs informations d’identification professionnelles pour se connecter (nom d’utilisateur et mot de passe, par exemple)</li> </ul> | Obligatoire |  
| ***Période d’essai gratuite et évaluation*** | Votre application doit inclure l’une des options suivantes pour qu’un client puisse utiliser votre application gratuitement pendant une durée limitée.<ul> <li>Fournissez une méthode `try` pour que les clients puissent commencer la période d’essai de votre application dans AppSource</li> <li>Fournissez une option `request trial` dans AppSource pour que les clients puissent demander une version d’évaluation de votre application</li> </ul>L’essai gratuit fourni doit inclure une période prédéfinie pendant laquelle le client peut tester votre application sans coût supplémentaire. | Obligatoire |  
| ***Solution facile à configurer et prête à l’emploi*** | Votre application doit être facile et rapide à configure,r sans personnalisation. | Obligatoire |  
| ***Gestion des prospects*** | Activez l’acceptation des données des prospects dans votre système CRM avant de générer des prospects via la vitrine.<ul> <li>Les CRM sont par exemple Marketo, Microsoft Dynamics ou Salesforce</li> </ul> | Obligatoire |  
| ***Politique de confidentialité et conditions d’utilisation*** | Votre application doit fournir un lien vers votre page de politique de confidentialité en utilisant une URL publique. Vous devez fournir le texte de vos conditions d’utilisation lors de la publication. | Obligatoire |  
| ***Support*** | Votre application doit fournir un lien vers votre page de service client en utilisant une URL publique. Si votre application est une version d’évaluation, vous devez fournir une assistance sans coût supplémentaire pendant la période d’essai. | Obligatoire |  

## <a name="storefront-requirements-azure-marketplace"></a>Configuration requise pour la vitrine : Place de marché Azure  
La configuration préalable requise pour les types de liste dans la Place de marché Microsoft Azure est la suivante.  

| Condition requise | Détails | Type de liste |  
|:--- |:--- |:--- |  
| ***Politiques concernant la participation*** | Votre application doit appliquer les politiques concernant la participation à la Place de marché Azure.<ul> <li>Pour plus d’informations sur les politiques de participation, consultez la page Politiques concernant la Participation à la Place de Marché Microsoft Azure à l’adresse [azure.microsoft.com/support/legal/marketplace/participation-policies/](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />transact<br />trial |  
| ***Intégration à Microsoft*** | Votre offre doit utiliser ou étendre les types de services Microsoft Azure (par exemple, calcul, réseau, stockage). Votre offre doit être alignée sur une catégorie de la Place de marché Microsoft Azure existante (par exemple, bases de données, sécurité ou réseau).<ul> <li>Pour plus d’informations sur les offres de la Place de marché, consultez la page des applications de la Place de marché à l’adresse [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />transact<br />trial |  
| ***Public ciblé*** | Votre offre doit s’adresser aux professionnels de l’informatique, aux développeurs cloud et autres rôles client techniques. | list<br />transact<br />trial |  
| ***Gestion des prospects*** | Pour générer des prospects via la vitrine, configurez votre système CRM (Marketo, Microsoft Dynamics ou Salesforce) de manière à accepter les données liées aux prospects. | list<br />transact<br />trial |  
| ***Politique de confidentialité et conditions d’utilisation*** | Votre application doit fournir un lien vers votre page de politique de confidentialité en utilisant une URL publique. Vous devez fournir le texte de vos conditions d’utilisation lors de la publication. | list<br />transact<br />trial |  
| ***Support*** | Votre offre doit fournir un lien vers votre page de service client en utilisant une URL publique. Si votre offre est une version d’évaluation, vous devez fournir une assistance sans coût supplémentaire pendant la période d’essai. | transact<br />trial |    

## <a name="non-transact-listings"></a>Référencements non-Transaction  
Cette section décrit tous les types d’offres qui n’utilisent pas le type de référencement Transaction. 

### <a name="list"></a>List  
Le type de référencement Liste inclut les types d’offre suivants dans les vitrines de la Place de marché.  

| Type d’offre | Vitrine | Détails |  
|:---        |:---        |:---     |  
| Services de conseil | AppSource | Conditions requises : AppSource : Liste : Services de conseil |  
| Services de conseil | Place de marché Azure | Conditions requises : Place de marché Azure : Liste : Services de conseil |  
| Me contacter | AppSource | [](#) |  
| Me contacter | Place de marché Azure | Conditions requises : AppSource : Liste : Me contacter |  

#### <a name="requirements-appsource-list-consulting-service"></a>Conditions requises : AppSource : Liste : Service de conseil  

| Spécifications | Détails |  
|:--- |:--- |  
| Caractéristiques de l’offre Services | Votre service de conseil doit respecter les critères suivants.<ul> <li>Fournir un engagement pour une durée, une portée et un prix fixes (ou gratuitement).</li> <li>Orienter principalement vers la prévente.</li> <li>Se limiter à un seul client.</li> <li>Être effectué sur site.</li> </ul> |  
| Exigences du partenaire pour les services de conseil | Vous répondez aux critères dans le domaine correspondant à votre service.<table><tr><th>Domaine fonctionnel</th><th>Critères</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Avoir la compétence Gestion de la relation client dans le Cloud, statut Silver ou Gold.</td></tr><tr><td>Dynamics 365 pour les opérations et la finance, édition Entreprise</td><td>Avoir la compétence Planification des ressources d’entreprise, statut Silver ou Gold, ainsi qu’un chiffre d’affaires résultant de vos opérations dans le cloud supérieur ou égal à 25 000 $ au cours des 12 derniers mois.</td></tr><tr><td>Dynamics 365 pour les opérations et la finance, édition Business</td><td>Agir en tant que fournisseur de services cloud ou partenaire de référence numérique pour un ou plusieurs clients.</td></tr><tr><td>Power BI</td><td>Respecter les critères du partenaire de solution.</td></tr><tr><td>PowerApps</td><td>Avoir une solution de présentation des partenaires.</td></tr></table><ul> <li>Pour plus d’informations sur la gestion de la relation client, visitez la page Gestion de la relation client dans le cloud à l’adresse [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Pour plus d’informations sur la planification des ressources, visitez la page Gestion intégrée à l’adresse [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Pour plus d’informations sur le fournisseur de services cloud, visitez la page Fournisseur de solutions cloud à l’adresse [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Pour plus d’informations sur le partenaire numérique d’enregistrement, visitez la page Partenaire de référence (POR) numérique et association au partenaire à l’adresse [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Pour plus d’informations sur les critères en matière de partenaire de solution, consultez le document Présentation des partenaires de solution et incitations accessible à l’adresse [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives).</li> <li>Pour plus d’informations sur la présentation du partenaire, visitez la page Découvrez ce que nos partenaires font avec PowerApps à l’adresse [powerapps.microsoft.com/partner-showcase/](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Conditions requises : Place de marché Azure : Liste : Service de conseil  

| Spécifications | Détails |  
|:--- |:--- |  
| Caractéristiques de l’offre Services | Votre service de conseil doit respecter les critères suivants.<ul> <li>Fournir un engagement pour une durée, une portée et un prix fixes (ou gratuitement).</li> <li>Orienter principalement vers la prévente.</li> <li>Se limiter à un seul client.</li> <li>Être effectué sur site.</li> </ul> |  
| Exigences du partenaire pour les services de conseil | Vous devez avoir le statut Silver ou Gold dans l’une des compétences suivantes dans le domaine correspondant à votre service. <table><tr><th>Domaine fonctionnel</th><th>Compétence</th></tr><td>Infrastructure et plateforme cloud</td><td>Plateforme cloud<br />Centre de données</td><tr><td>Développement d’applications et éditeurs de logiciels indépendants</td><td>Développement d'applications<br />Intégration des applications<br />DevOps</td></tr><tr><td>Gestion et analyse des données</td><td>Analytique des données<br />Plateforme de données</td></tr></table><ul> <li>Pour plus d’informations sur les compétences, visitez la page Partenaires de compétence via Microsoft Partner Network à l’adresse [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Pour plus d’informations sur le référencement, visitez la page Services de conseil de la Place de marché Azure à l’adresse [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Version d’évaluation  

| Type d’offre | Vitrine | Détails |  
|:---        |:---        |:---     |  
| Version d’évaluation gratuite/SaaS | AppSource | Exigences du type de liste : Version d’évaluation |  
| Version d’évaluation gratuite/SaaS | Place de marché Azure | Conditions requises : Place de marché Azure : Version d’évaluation : Version d’évaluation gratuite/SaaS |  
| Démonstration interactive | AppSource | Exigences du type de liste : Version d’évaluation |  
| Démonstration interactive | Place de marché Azure | [Exigences : Place de marché Azure : Version d’évaluation : Démonstration interactive](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test drive | AppSource | Exigences du type de liste : Version d’évaluation |  
| Test drive | Place de marché Azure | [Exigences : Place de marché Azure : Version d’évaluation : Test drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Conditions requises : Place de marché Azure : Version d’évaluation  

| Condition requise | Détails |  
|:--- |:--- |  
| Période d’essai gratuite et évaluation | Votre client peut utiliser votre application gratuitement pendant une durée limitée.<br /><br />Votre client n’est pas obligé de payer des frais de licence ou d’abonnement pour votre offre ou votre application. Votre client n’est pas obligé de payer le produit ou service interne Microsoft sous-jacent. Toutes les options de la version d’évaluation sont déployées sur votre abonnement Azure. Vous avez le contrôle exclusif de la gestion et de l’optimisation des coûts.<br /><br />Vous pouvez choisir entre les options Version d’évaluation, Démonstration interactive ou Test drive. Quel que soit votre choix, votre essai gratuit doit permettre au client de tester gratuitement votre application pendant une durée définie.<ul> <li>Pour commencer la création d’un test drive, contactez-nous à l’adresse [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Remarque : Les versions d’essai SaaS de la Place de marché Microsoft Azure doivent permettre aux clients d’utiliser leurs informations d’identification professionnelles pour se connecter.<ul> <li>Pour plus d’informations, visitez la section Expérience d’essai AppSource située à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Solution facile à configurer et prête à l’emploi | Votre application doit être facile et rapide à configurer. |  
| Disponibilité/Temps d’activité | La durée de fonctionnement de votre plateforme ou application SaaS doit être au minimum égale à 99,9 % du temps total. |  
| Azure Active Directory | Votre offre doit permettre l’authentification unique fédérée Azure Active Directory (Azure AD) avec activation du consentement. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Conditions requises : Place de marché Azure : Version d’évaluation : Version d’évaluation gratuite/SaaS  

| Avantage | Condition requise |  
|:--- |:--- |  
| Permet à un client d’essayer votre produit avant de l’acheter en proposant une méthode automatisée pour passer à une version payante. Offre également des preuves de concept au client et un contrat conjoint avec les équipes de vente Microsoft. | Votre solution est une machine virtuelle ou un modèle de solution.<br /><br />Votre solution est une offre SaaS, et si vous proposez un produit mutualisé SaaS.<br /><br />Vous avez une expérience de première exécution pour qu’un client puisse être opérationnel rapidement.<br /><br />Vous avez un seul locataire, mais ajoutez des clients en tant qu’utilisateurs invités. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Conditions requises : Place de marché Azure : Version d’évaluation : Démonstration interactive  

| Avantage | Condition requise |  
|:--- |:--- |  
| Permet aux clients de voir votre solution en action sans avoir à installer de programme. | Votre solution nécessite une installation complexe qui serait trop difficile à effectuer pendant la période d’évaluation. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Conditions requises : Place de marché Azure : Version d’évaluation : Test drive  

| Avantage | Condition requise |  
|:--- |:--- |  
| Permet à un client d’essayer votre produit avant de l’acheter.<br /><br />Fournit une expérience guidée de votre solution avec des paramètres préconfigurés.<br /><br />Voici les avantages supplémentaires de l’utilisation d’un test drive.<ul> <li>27 % des recherches utilisateur sur la Place de marché sont affinées pour n’afficher que les offres proposant un test drive.</li> <li>Les offres avec test drive génèrent 38 % de prospects en plus que les autres.</li> <li>36 % des nouvelles acquisitions client sur la Place de marché proviennent de clients ayant d’abord utilisé un test drive.</li> <li>Les test drives permettent aux vendeurs sur site Microsoft de mieux comprendre votre produit, ce qui est important pour la vente conjointe.</li> </ul> | Votre solution est une machine virtuelle, un modèle de solution ou une application SaaS avec un seul locataire, ou si son approvisionnement est compliqué. <br /><br />Vous ne disposez pas d’une méthode permettant de transformer votre version d’évaluation en version payante. |  

---

## <a name="transact-specific-listings"></a>Référencements Transaction

### <a name="transact"></a>Transaction  

| Type d’offre | Vitrine | Détails |   
|:---        |:---        | :--- |  
| Applications Azure : application gérée | Place de marché Azure | Conditions requises : Place de marché Azure : Transaction : Applications Azure : application gérée |  
| Applications Azure : Modèle de solution | Place de marché Azure | Conditions requises : Place de marché Azure : Transaction : Applications Azure : Modèle de solution |  
| Containers | Place de marché Azure | [Exigences : Place de marché Azure : Transaction : Conteneur](#requirements-azure-marketplace-transact-container) |  
| Application SaaS  | Place de marché Azure | [Exigences : Place de marché Azure : Transaction : Application SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Machine virtuelle | Place de marché Azure | [Exigences : Place de marché Azure : Transaction : Machine virtuelle](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Conditions requises : Place de marché Azure : Transaction : Conteneur  

| Condition requise | Détails |  
|:--- |:--- |  
| Facturation et mesure | Prise en charge du modèle de facturation gratuit ou BYOL. |  
| Image Docker | L’images de conteneur doit être basée sur le format d’image Docker et doit être extraite des registres de conteneurs Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Conditions requises : Place de marché Azure : Transaction : Application SaaS  

| Condition requise | Détails |  
|:--- |:--- |  
| Facturation et mesure | Votre offre est facturée selon un tarif mensuel forfaitaire. La tarification basée sur l’utilisation et les options *d’égalisation* basées sur l’utilisation ne sont pas prises en charge pour l’instant. |  
| Annulation | Votre offre peut être annulée par le client à tout moment. |  
| Page d’accueil de transaction | Hébergez une page d’accueil de transaction conjointe avec Azure. Votre page d’accueil permet à vos clients de créer et de gérer votre compte de service SaaS. |  
| API d’abonnement SaaS | Fournissez un service capable d’interagir avec l’abonnement SaaS pour créer, mettre à jour et supprimer un compte utilisateur et un plan de service. Toutes les modifications critiques de l’API doivent être effectuées dans les 24 heures. Les modifications non critiques de l’API sont effectuées régulièrement. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Conditions requises : Place de marché Azure : Transaction : Machine virtuelle  

| Condition requise | Détails |  
|:--- |:--- | 
| Facturation et mesure | Votre machine virtuelle doit prendre en charge la facturation mensuelle BYOL ou avec paiement à l’utilisation. |  
| Disque dur virtuel compatible avec Azure | Les machines virtuelles doivent être basées sur Windows ou Linux.<ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez [Créer un disque dur virtuel compatible avec Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Étapes suivantes
*   Consultez la page [Guide de l’éditeur Place de marché Azure et AppSource](./marketplace-publishers-guide.md).  

