---
title: Configurer des prospects | Place de marché Azure
description: Configurez des prospects dans le portail Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280318"
---
<a name="get-customer-leads"></a>Obtenir des prospects
==================

Cet article explique comment créer des prospects à l’aide du portail Cloud Partner. Vous pouvez connecter ces prospects à votre système CRM et les intégrer à votre pipeline des ventes.

## <a name="leads"></a>Prospects

Les prospects sont des clients qui sont intéressés par le déploiement de vos produits à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/) ou à partir [d’AppSource](https://appsource.microsoft.com) ou qui procèdent à ce déploiement.

### <a name="azure-marketplace"></a>Place de marché Azure

1.  Un client opte pour une « version d’évaluation » de votre offre. Les versions d’évaluation constituent pour vous une opportunité accélérée de partager votre activité instantanément avec des clients potentiels sans barrières à l’entrée. Toutes les versions d’évaluation génèrent un prospect correspondant à un client qui s’intéresse à l’essai de votre produit pour en savoir plus. Pour en savoir plus sur les versions d’évaluation, consultez l’article [Version d’évaluation sur la Place de marché Microsoft Azure](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exemples de versions d’évaluation de la Place de marché](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Le client consent à partager ses informations après avoir sélectionné « Obtenir maintenant ». Ce prospect est un prospect **d’intérêt initial** ; nous partageons des informations sur un client qui a manifesté de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.

   ![Option Obtenir maintenant](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Le client sélectionne Acheter dans le [portail Azure](https://portal.azure.com/) pour obtenir votre produit. Ce prospect est un prospect **actif** ; nous partageons des informations sur un client qui a commencé à déployer votre produit.

   ![Option d’achat](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Un client a opté pour une « Version d’évaluation » de votre offre. Les versions d’évaluation constituent pour vous une opportunité accélérée de partager votre activité instantanément avec des clients potentiels sans barrières à l’entrée. Toutes les versions d’évaluation génèrent un prospect correspondant à un client qui s’intéresse à l’essai de votre produit pour en savoir plus. Pour en savoir plus sur les versions d’évaluation, voir l’article concernant les [versions d’évaluation sur AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemple de version d’évaluation](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Le client consent à partager ses informations après avoir sélectionné « Obtenir maintenant ». Ce prospect est un prospect **d’intérêt initial** ; nous partageons des informations sur un client qui manifeste de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.

      ![Option Obtenir maintenant](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Le client sélectionne Me contacter dans votre offre. Ce prospect est un prospect **actif** ; nous partageons des informations sur un client qui demande à être recontacté au sujet de votre produit.

    ![Option Me contacter](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Données de prospect
---------

Chaque prospect que vous recevez pendant le processus d’acquisition de clients dispose de données dans des champs spécifiques. Comme vous recevez des prospects lors de plusieurs étapes, la meilleure façon de les gérer consiste à dédupliquer et à personnaliser les suivis. De cette manière, chaque client reçoit un message approprié, et vous créez une relation unique.

### <a name="lead-source"></a>Source de prospect

Le format d’une source de prospect est **Source**-**Action** |  **Offre**

**Sources** : « AzureMarketplace », « AzurePortal », « TestDrive » et « AppSource (SPZA) »

**Actions** :
- « INS » : installation. Cette action figure sur la Place de marché Azure ou dans AppSource lorsqu’un client achète votre produit.
- « PLT » : désigne une évaluation conduite par un partenaire. Cette action apparaît dans AppSource lorsqu’un client utilise l’option Me contacter.
- « DNC » : ne pas contacter. Cette action apparaît dans AppSource lorsqu’un partenaire qui a été répertorié sur la page de votre application reçoit une demande de contact. Nous vous informons que ce client a été répertorié sur votre application, mais qu’il n’est pas nécessaire de le contacter.
- « Create » : cette action apparaît uniquement dans le portail Azure et est générée lorsqu’un client achète votre offre sur son compte.
- « StartTestDrive » : cette action est destinée uniquement aux versions d’évaluation ; elle est générée quand un client démarre sa version d’évaluation.

**Offres**

Les exemples suivants présentent des identificateurs uniques qui sont affectés à un éditeur et à une offre spécifique : checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress et docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informations client

Les champs indiqués dans l’exemple suivant indiquent les informations client qui sont contenues dans un prospect.
- Prénom : John
- Nom : Smith
- Adresse e-mail : jsmith\@microsoft.com
- Téléphone : 1234567890
- Pays : États-Unis
- Entreprise : Microsoft
- Fonction : directeur technique

>[!Note]
>Les données de l’exemple précédent ne sont pas toujours toutes disponibles pour chaque prospect.

Nous travaillons activement à l’amélioration des prospects. S’il y a un champ de données que vous ne voyez pas ici mais que vous aimeriez avoir, veuillez [nous envoyer vos commentaires](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Comment connecter votre système CRM au portail Microsoft Cloud Partner
------------------------------------------------------------

Pour commencer à obtenir des prospects, nous avons basé notre connecteur de gestion des prospects sur le portail Microsoft Cloud Partner afin que vous puissiez facilement intégrer vos informations CRM. De plus, nous établissons cette connexion à votre place. Vous pouvez désormais exploiter facilement les prospects générés par la Place de marché, sans avoir à consentir de lourds efforts d’ingénierie pour opérer l’intégration avec un système externe.

![Connecteur de gestion des prospects](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Nous pouvons écrire des prospects dans un vaste éventail de systèmes CRM ou directement dans une table de stockage Azure où vous pouvez gérer les prospects comme vous le souhaitez. Les liens suivants fournissent des instructions pour la connexion à des destinations de prospects possibles :

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) pour consulter les instructions sur la configuration de Dynamics CRM Online pour obtenir des prospects.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) pour consulter les instructions sur configuration des prospects Marketo pour obtenir des prospects.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) pour consulter les instructions sur la configuration de votre instance Salesforce pour obtenir des prospects.
-    [Table Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) pour consulter les instructions sur la configuration de votre compte de stockage Azure pour obtenir des prospects dans une table Azure.
-   [Point de terminaison HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) pour consulter les instructions sur la configuration de votre point de terminaison HTTPS pour obtenir des prospects.

Une fois que vous avez configuré votre destination de prospect et publié votre offre, nous validons la connexion et nous vous envoyons un prospect test. Lorsque vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acquérir vous-même l’offre dans l’environnement en version préliminaire. Il est important de s’assurer que vos paramètres de prospects restent à jour afin d’éviter de perdre des prospects. Veillez donc à mettre à jour ces connexions chaque fois qu’une modification intervient de votre côté.

<a name="what-next"></a>Étapes suivantes
----------

Une fois les paramètres techniques configurés, vous devez incorporer ces prospects dans votre stratégie commerciale et marketing, ainsi que dans vos processus opérationnels actuels. Nous cherchons à mieux comprendre votre processus de vente global. C’est pourquoi nous tenons à travailler en étroite collaboration avec vous pour vous offrir des prospects de haute qualité et des données en quantité suffisante pour garantir votre réussite. Nous apprécions vos commentaires sur la façon dont nous pouvons optimiser et améliorer les prospects que nous vous envoyons, accompagnés de toutes les données nécessaires pour assurer la réussite de ces clients. Faites-nous savoir si vous souhaitez formuler des [commentaires](mailto:AzureMarketOnboard@microsoft.com) et des suggestions pour permettre à votre équipe de vente de mieux exploiter les prospects de la Place de marché.
