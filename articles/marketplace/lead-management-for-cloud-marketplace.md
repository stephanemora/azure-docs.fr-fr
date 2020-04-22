---
title: Gestion des prospects pour la Place de marché commerciale | Place de marché Azure et AppSource
description: Vue d’ensemble des différentes questions relatives à la publication d’offres et d’artefacts techniques sur la Place de marché Azure et AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383781"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gestion des prospects pour la Place de marché commerciale


Les clients sont au centre de toute entreprise prospère. Dans la transformation actuelle de l’acquisition des produits, les responsables marketing doivent s’attacher à établir un lien direct avec les clients et à nouer une relation. C’est pourquoi la génération de prospects de haute qualité est un outil essentiel pour votre cycle de vente. Une fois votre offre publiée dans l'[Espace partenaires](https://partner.microsoft.com/), des outils vous permettent de recevoir systématiquement les informations de contact des clients qui manifestent leur l’intérêt pour votre produit ou qui le déploient sur la Place de marché. 



## <a name="what-are-leads-in-the-marketplace"></a>Que sont les prospects sur la Place de marché ?

Les prospects comptent parmi les clients qui sont intéressés par vos produits ou qui les déploient à partir de la Place de marché. Que votre produit soit publié sur la Place de marché Azure ou sur AppSource, vous pouvez recevoir les prospects une fois qu’il est correctement configuré entre votre système CRM et vos référencements dans l'Espace partenaires. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Comment connecter votre système CRM à l'Espace partenaires

Pour vous permettre de commencer à obtenir des prospects, le connecteur Gestion des prospects de l'Espace partenaires a été conçu pour se connecter facilement à plusieurs systèmes CRM disponibles à partir de vos informations CRM. Vous pouvez désormais exploiter facilement les prospects générés par la Place de marché, sans avoir à consentir de lourds efforts d’ingénierie pour opérer l’intégration avec un système externe.

Voici les instructions pas à pas pour vous connecter à chacune des destinations de prospects possibles :

**Dynamics CRM Online** - Consultez [Configurer la gestion des prospects pour Dynamics 365 for Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) pour savoir comment configurer Dynamics CRM Online afin d'obtenir des prospects.

**Marketo** - Consultez [Configurer la gestion des prospects dans Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) pour savoir comment configurer Marketo afin d'obtenir des prospects.

**Salesforce** - Consultez [Configurer la gestion des prospects pour Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) pour savoir comment configurer votre instance Salesforce afin d'obtenir des prospects.

**Table Azure** - Consultez [Configurer la gestion des prospects à l’aide d’une table Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) pour savoir comment configurer votre compte Stockage Azure afin d'obtenir des prospects dans une table Azure.

**Point de terminaison HTTPS** - Consultez [Configurer la gestion des prospects à l’aide d’un point de terminaison HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) pour savoir comment configurer votre point de terminaison HTTPS afin d'obtenir des prospects.

Une fois que vous avez correctement configuré votre destination de prospect et que vous avez publié votre offre, nous validons la connexion et vous envoyons un prospect de test. Lorsque vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acquérir vous-même l’offre dans l’environnement en version préliminaire. Il est important de s’assurer que vos paramètres de prospects restent à jour afin d’éviter de perdre des prospects. Veillez donc à mettre à jour ces connexions chaque fois qu’une modification intervient de votre côté.

### <a name="what-are-the-next-steps"></a>Quelles sont les étapes suivantes ?

Une fois les paramètres techniques configurés, vous devez incorporer ces prospects à votre stratégie commerciale et marketing, ainsi qu’à vos processus opérationnels actuels. Nous cherchons à mieux comprendre votre processus de vente global. C’est pourquoi nous tenons à travailler en étroite collaboration avec vous pour vous apporter des prospects de qualité et des données en quantité suffisante pour garantir votre réussite. Nous apprécions vos commentaires sur la façon dont nous pouvons optimiser et améliorer les prospects que nous vous envoyons, accompagnés de toutes les données nécessaires pour assurer la réussite de ces clients. Faites-nous savoir si vous souhaitez nous communiquer vos commentaires et suggestions pour permettre à votre équipe commerciale de mieux exploiter les prospects de la Place de marché.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Erreurs courantes de configuration des prospects au moment de publier dans l'Espace partenaires

**Impossible d’enregistrer le prospect dans Dynamics CRM. Vérifiez les paramètres de compte Dynamics CRM. LastCRMError : Impossible de se connecter à Dynamics CRM, LastCRMException :** 

> Si l’authentification O365 a été sélectionnée, vérifiez la validité du compte d’utilisateur et du mot de passe. Si AAD a été sélectionné, vérifiez que l’ID de locataire, l’ID d’application et la clé secrète de l’application correspondent à ce qui a été configuré dans AAD. Suivez les instructions [ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Si le nom d’utilisateur/mot de passe du compte est valide, vérifiez que celui-ci a accès à Dynamics 365 et qu’une licence lui est attribuée (étapes 11 à 15 si vous utilisez Azure Active Directory ou les paramètres de sécurité si vous utilisez un utilisateur Office). 

**Impossible d’enregistrer le prospect dans Dynamics CRM. L’utilisateur ne dispose pas d’autorisations de création pour l’attribut leadsourcecode dans l’entité prospect** 

> Il manque un ou plusieurs rôles de sécurité à l’application/utilisateur pour le rédacteur de prospects de la Place de marché Microsoft. Suivez les étapes 11 à 15 si vous utilisez Azure Active Directory ou Paramètres de sécurité si vous utilisez un utilisateur Office [ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Impossible d’enregistrer le prospect dans Dynamics CRM en utilisant AAD. Exception:: Locataire introuvable. Cette instance peut se produire s’il n’existe aucun abonnement actif pour le locataire.**  

> L’ID de répertoire fourni dans la section de gestion des prospects n’est pas un répertoire valide. Procurez-vous l’ID de répertoire en suivant les instructions fournies à l’étape 2 (sous Azure Active Directory, à partir d’[ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Impossible d’enregistrer le prospect dans Dynamics CRM. LastCRMError : SecLib::RetrievePrivilegeForUser en échec - aucun rôle attribué à l’utilisateur.**  

> Résolution : attribuez un rôle de sécurité au rédacteur de prospects de la Place de marché Microsoft. Suivez les instructions fournies [ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) sous Paramètres de sécurité.

**Impossible d’enregistrer le prospect dans Dynamics CRM en utilisant AAD. Exception:: L’application associée à l’identificateur est introuvable dans le répertoire** 

> L’ID d’application fourni dans la section de gestion des prospects n’est pas un répertoire valide. Procurez-vous l’ID de répertoire en suivant les instructions fournies à l’étape 8 (sous Azure Active Directory, à partir d’[ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Impossible d’enregistrer le prospect dans Dynamics CRM en utilisant AAD. Exception:: L’identificateur de locataire demandé n’est pas valide et n’est pas un format de domaine externe valide** 

> L’ID de répertoire fourni dans la section de gestion des prospects n’est pas un répertoire valide. Procurez-vous l’ID de répertoire en suivant les instructions fournies à l’étape 2 (sous Azure Active Directory, à partir d’[ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Impossible d’enregistrer le prospect dans Dynamics CRM en utilisant AAD. Exception:: Erreur de validation des informations d’identification. Le secret client fourni n’est pas valide.** 

> Résolution : Connectez-vous au portail Azure et vérifiez que la clé d’application correspond à celle de l'Espace partenaires. Générez un mot de passe en suivant les instructions fournies à l’étape 10 (sous Azure Active Directory, à partir d’[ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Impossible d’enregistrer le prospect dans Dynamics CRM. LastCRMError : Le canal de demande a dépassé le délai de réponse après 00:02:00. Augmentez la valeur de délai d’attente passée à l’appel de Request ou augmentez la valeur de SendTimeout de la liaison. Le temps alloué à cette opération fait peut-être partie d’un délai d’attente plus long.**  

> Résolution : Connectez-vous à l'Espace partenaires, puis dans Détails de la vitrine >> Destination des prospects >> URL, vérifiez qu’il s’agit d’une instance Dynamics CRM valide.

## <a name="frequently-asked-questions"></a>Forum aux questions

**Que sont les prospects et en quoi sont-ils importants pour moi qui suis éditeur sur la Place de marché ?** 

Les prospects sont des clients qui déploient vos produits à partir de la Place de marché. Que votre produit soit publié sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) ou sur [AppSource](https://appsource.microsoft.com/), vous pouvez recevoir des prospects intéressés par votre produit si vous avez configuré la destination des prospects dans votre offre.  

**Où puis-je obtenir de l’aide pour configurer la destination des prospects ?** 

Vous trouverez la documentation ici : [Obtenir des prospects](./partner-center-portal/commercial-marketplace-get-customer-leads.md) Vous pouvez aussi soumettre un ticket de support via aka.ms/marketplacepublishersupport en sélectionnant le type d’offre et la gestion des prospects. 

**Dois-je obligatoirement configurer une destination des prospects pour publier une offre sur la Place de marché ?**

Oui, si vous publiez une application SaaS Contact Me ou des services de conseil.  
 
**Comment puis-je vérifier que la configuration des prospects est correcte ?**

Après avoir configuré votre offre et la destination des prospects, publiez votre offre. À l’étape de validation des prospects, la Place de marché envoie un prospect de test à la destination des prospects configurée dans votre offre. 

**Comment puis-je trouver le prospect de test ?**

Recherchez « MSFT_TEST » dans votre destination des prospects. Voici un exemple de données de prospect de test : 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Nom Offre> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

**J’ai une offre en ligne, mais je ne vois aucun prospect.**

Les données de chaque prospect passent dans les champs de la destination des prospects que vous avez sélectionnée ; les offres se présentent sous la forme suivante : **Source-Action|Offre** 

  *Sources :*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Actions :*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Offres :*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Voici des exemples de données d’informations client :*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Pour en savoir plus, consultez [Informations sur les prospects](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**J’ai configuré Blob Azure comme destination des prospects. Pourquoi ne vois-je pas de prospects ?** 

Les prospects sont écrits uniquement si vous sélectionnez le Stockage Blob Azure comme destination des prospects. Basculez vers Table Azure pour recevoir les prospects en temps réel.

**J’ai reçu un e-mail de la Place de marché. Pourquoi donc je ne trouve pas les prospects dans mon système CRM ?**  

Il est possible que le domaine de messagerie de l’utilisateur final soit .edu. Pour des raisons de confidentialité, nous ne transmettons pas de données personnelles identifiables en provenance du domaine .edu. Envoyez-nous un [ticket de support](https://aka.ms/marketplacepublishersupport).

**J’ai configuré Table Azure/Blob Azure comme destination des prospects. Pourquoi ne vois-je pas les prospects ?** 

Vous pouvez accéder à l’objet blob ou à la table Azure sur le portail Azure, ou télécharger et installer gratuitement [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour afficher les tables/objets blob de votre compte de stockage Azure. 

**J’ai configuré Table Azure comme destination des prospects. Puis-je recevoir une notification chaque fois qu’un nouveau prospect est envoyé par la Place de marché ?** 

Oui, pour configurer Table Azure + Azure Function, suivez les instructions fournies dans la documentation [ici](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**J’ai configuré Salesforce comme destination des prospects. Pourquoi donc je ne trouve pas les prospects  ?** 

Vérifiez si le formulaire « web to lead » est un champ obligatoire basé sur une liste déroulante. Si c’est le cas, faites-en un champ de texte non obligatoire.  
 
**Un problème s’est produit au niveau de ma destination des prospects, ce qui m’a fait manquer des prospects. Puis-je me les faire envoyer par e-mail ?** 

En raison des politiques de confidentialité, nous ne pouvons pas partager les informations sur les prospects via une messagerie non sécurisée. 

**J’ai configuré le Stockage Azure (Blob/Table) comme destination des prospects. Quel en sera le coût ?** 

Le volume de données de génération de prospects est faible (< 1 Go pour pratiquement tous les éditeurs). Le coût dépend du nombre de prospects reçus. Pour 1 000 prospects reçus dans un mois, le coût est d’environ 50 cents. 
