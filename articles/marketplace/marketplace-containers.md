---
title: Guide de publication des offres de conteneur dans la Place de marché Azure
description: Cet article décrit les conditions requises pour publier des conteneur sur la Place de marché Azure.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 43c569204f879e865eb5bd4f040c96e907e63abb
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144706"
---
# <a name="plan-an-azure-container-offer"></a>Planifier une offre Azure Container

Les offres de conteneur Azure vous aident à publier votre image conteneur sur la Place de marché Azure. Utilisez ce guide pour comprendre les exigences propres à ce type d’offre.

Les offres de conteneur Azure sont des offres de transaction qui sont déployées et facturées par le biais de la Place de marché Azure. L’option d’annonce qu’un utilisateur voit est **Obtenir maintenant**.

Utilisez le type d’offre Conteneur Azure quand votre solution est une image conteneur Docker qui est configurée en tant qu’instance de conteneur Azure basée sur Kubernetes.

> [!NOTE]
> Une instance Azure Container est une instance Docker à l’exécution qui offre la façon la plus simple et la plus rapide d’exécuter un conteneur dans Azure, sans avoir à gérer des machines virtuelles ni à adopter un service de niveau supérieur. Les instances de conteneur peuvent être déployées directement sur Azure ou orchestrées par Azure Kubernetes Services ou le moteur Azure Kubernetes Services.  

## <a name="licensing-options"></a>Options de licence

Voici les options de licence disponibles pour les offres Azure Container :

| Option de licence | Processus de transaction |
| --- | --- |
| Gratuit | Référencez votre offre gratuitement pour les clients. |
| BYOL | L’option BYOL (avec apport de sa propre licence) permet à vos clients d’intégrer des licences logicielles existantes à Azure.\* |
|

\* En tant qu’éditeur, vous prenez en charge tous les aspects de la transaction de licence logicielle, notamment la commande, l’exécution de celle-ci, sa mesure, sa comptabilisation et sa facturation, ainsi que son paiement et son encaissement.

## <a name="customer-leads"></a>Prospects

Quand vous publiez une offre sur le marketplace commercial par le biais d’Espace partenaires, connectez-la à votre système de Gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un CRM est obligatoire si vous souhaitez activer une version d’évaluation ; sinon, la connexion à un CRM est facultative. Espace partenaires prend en charge les tables Azure, Dynamics 365 Customer Engagement, le point de terminaison HTTPS, Marketo et Salesforce.

## <a name="legal-contracts"></a>Contrats juridiques

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard que vous pouvez utiliser pour vos offres dans la place de marché commerciale. Lorsque vous proposez votre logiciel dans le cadre du contrat standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées.

Vous pouvez également utiliser vos propres conditions générales au lieu du contrat standard. Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

## <a name="offer-listing-details"></a>Détails du référencement de l’offre

> [!NOTE]
> Le contenu du référencement de l’offre ne doit pas nécessairement être en anglais si la description de l’offre commence par la phrase « This application is available only in [langue autre que l’anglais] ».

Pour faciliter la création de votre offre, préparez ces éléments à l’avance. Tous sont obligatoires, sauf indication contraire.

- **Nom** : Le nom apparaîtra comme titre de votre offre sur le marketplace commercial. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résumé des résultats de recherche** : L’objectif ou la fonction de votre offre en une phrase, sans saut de ligne et de 100 caractères maximum. Ce résumé est utilisé dans les résultats de recherche des offres du marketplace commercial.
- **Brève description** : Détails de l’objectif ou de la fonction de l’offre, écrits en texte brut sans saut de ligne. Elle apparaîtra sur la page de détails de votre offre.
- **Description** : Cette description s’affiche dans la vue d’ensemble des offres du marketplace commercial. Vous pouvez inclure une proposition de valeur, des avantages clés, une base utilisateur visée, des associations de catégorie ou de secteur, des opportunités d’achats dans l’application, des informations requises et un lien pour en savoir plus. Cette zone de texte contient des contrôles d’éditeur de texte enrichi pour rendre votre description plus attrayante. Si vous le souhaitez, utilisez des balises HTML pour la mise en forme.
- **Lien vers la politique de confidentialité** : URL de la politique de confidentialité de votre entreprise. Il vous incombe de veiller à ce que votre application soit conforme aux lois et règlements relatifs à la protection des données personnelles.
- **Liens utiles** (facultatif) : Liens vers diverses ressources destinées aux utilisateurs de votre offre. Par exemple, forums, FAQ et notes de publication.
- **Informations de contact**
  - **Coordonnées du support** : Le nom, le numéro de téléphone et l’adresse e-mail des partenaires Microsoft à utiliser quand vos clients ouvrent des tickets. Incluez l’URL du site web de votre support.
  - **Coordonnées de l’équipe d’ingénierie** : Le nom, le numéro de téléphone et l’adresse e-mail de Microsoft à utiliser directement en cas de problème avec votre offre. Ces informations de contact ne sont pas répertoriées dans la place de marché commerciale.
  - **Coordonnées du programme CSP** (facultatif) : Le nom, le numéro de téléphone et l’adresse e-mail si vous avez choisi de participer au programme CSP, afin que ces partenaires puissent vous contacter pour toute question. Vous pouvez également inclure une URL pour vos documents marketing.
- **Média**
    - **Logos** : Fichier PNG pour le logo **Grande taille**. Espace partenaires l’utilisera pour créer d’autres tailles requises du logo. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.
    - **Captures d’écran** : Au moins une et au plus cinq captures d’écran qui illustrent le fonctionnement de votre offre. Les images doivent être de 1280 x 720 pixels, au format PNG, et inclure une légende.
    - **Vidéos** (facultatif) : Jusqu’à quatre vidéos qui illustrent votre offre. Incluez un nom, une URL pour YouTube ou Vimeo et une miniature PNG de 1280 x 720 pixels.

> [!Note]
> Pour être publiées sur la place de marché commerciale, votre offre doit respecter les [stratégies de certification de la place de marché commerciale](/legal/marketplace/certification-policies#100-general).

## <a name="preview-audience"></a>Public de la préversion

Le public de la préversion peut accéder à votre offre avant sa publication dans les magasins en ligne, afin de tester sa fonctionnalité de bout en bout avant la publication en ligne. Dans la page **Public de la préversion**, vous pouvez définir un public de préversion limité.

Vous pouvez envoyer des invitations à des ID d’abonnement Azure. Ajoutez jusqu’à 10 ID manuellement, ou importez-en jusqu’à 100 à l’aide d’un fichier .csv. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de tester d’éventuels changements ou mises à jour.

## <a name="plans-and-pricing"></a>Plans et tarifs

Les offres de conteneur nécessitent au moins un plan. Un plan définit l’étendue et les limites de la solution. Vous pouvez créer plusieurs plans pour votre offre afin d’offrir à vos clients différentes options techniques et de licence. 

Les conteneurs prennent en charge deux modèles de licence : Gratuit ou BYOL (apportez votre propre licence). BYOL signifie que vous facturerez directement vos clients et que Microsoft ne vous facturera aucun frais. Microsoft transmet uniquement les frais d’utilisation de l’infrastructure Azure. Pour plus d’informations, consultez [Fonctionnalités de transaction de la Place de marché commerciale](marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="additional-sales-opportunities"></a>Opportunités de ventes supplémentaires

Vous pouvez opter pour des canaux marketing et de vente pris en charge par Microsoft. Lorsque vous créez votre offre dans l’Espace partenaires, deux onglets s’affichent vers la fin du processus :

- **Revendre via des fournisseurs de solutions Cloud** : Permettre aux partenaires fournisseurs de solutions Cloud (CSP) de Microsoft de revendre votre solution dans le cadre d’une offre groupée. Pour plus d’informations sur ce programme, consultez [Programme des fournisseurs de solutions Cloud](cloud-solution-providers.md).
- **Co-vendre avec Microsoft** : Permettre aux équipes de vente de Microsoft de tenir compte de votre solution éligible de co-vente IP lors de l’évaluation des besoins de leurs clients. Pour plus d’informations sur l’éligibilité à la co-vente, consultez [Prérequis relatifs à l’état de co-vente](/legal/marketplace/certification-policies). Pour plus d’informations sur la préparation de votre offre à des fins d’évaluation, consultez [Option de co-vente sur Espace partenaires](./co-sell-configure.md).

## <a name="container-offer-requirements"></a>Exigences de l’offre conteneur

| Condition requise | Détails |  
|:--- |:--- |  
| Facturation et mesure | Prise en charge du modèle de facturation gratuit ou BYOL. |
| Image créée à partir d’un Dockerfile | Les images conteneur doivent être basées sur la spécification d’image Docker et générées à partir d’un Dockerfile. Pour plus d’informations sur la création d’images Docker, consultez la section « Utilisation » de la [documentation de référence de Dockerfile](https://docs.docker.com/engine/reference/builder/#usage). |
| Hébergement dans un référentiel Azure Container Registry | Les images conteneur doivent être hébergées dans un référentiel Azure Container Registry. Pour plus d’informations sur l’utilisation d’Azure Container Registry, consultez [Guide de démarrage rapide : Créer un registre de conteneurs privé à l’aide du Portail Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |
| Balisage d’images | Les images conteneur doivent contenir au moins une balise (nombre maximal de balises : 16). Pour plus d’informations sur le balisage d’une image, consultez la page `docker tag` sur le site [Documentation Docker](https://docs.docker.com/engine/reference/commandline/tag). |

## <a name="next-steps"></a>Étapes suivantes

- [Préparer des ressources techniques](azure-container-technical-assets.md)