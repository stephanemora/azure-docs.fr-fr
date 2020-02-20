---
title: Questions fréquentes et terminologie relatives à l’analytique de la Place de marché commerciale dans l’Espace partenaires
description: Découvrez des réponses aux questions fréquemment posées au sujet de l’analytique de la Place de marché commerciale. Comprend un dictionnaire de données sur la terminologie de l’analytique.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 081109c2208e2006eb1628fbf0cfb99b1e6bd8f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462137"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Questions fréquentes et terminologie relatives à l’analytique de la Place de marché commerciale

Cet article fournit des réponses à des questions fréquentes sur les messages de l’analytique dans l’Espace partenaires. Il comporte également un dictionnaire des termes de l’analytique.

## <a name="frequently-asked-questions"></a>Forum aux questions

Cette section fournit des réponses aux questions fréquemment posées sur les messages **Aucune analytique disponible pour l’instant** dans l’Espace partenaires.

**Je ne parviens pas à afficher mes données d’analyse dans l’Espace partenaires. Lorsque j’accède à ces pages, le message ci-dessous s’affiche. Pourquoi ?**

![Encore aucunes données pour vos offres](./media/analytics-faq-no-data.png)

Raisons possibles de ce message :

- Aucune acquisition n’existe actuellement pour vos offres publiées dans la Place de marché. Cela peut signifier que vos offres sont en ligne dans la Place de marché et qu’elles sont visionnées par des clients dans les pages d’affichage de produit, mais que les clients n’ont pas encore entrepris d’action pour les acheter et les déployer.
- La publication de votre offre est peut-être encore en cours et pas encore en ligne. Seules les offres en ligne peuvent être acquises par des clients. Pour vérifier le statut de vos offres, consultez Vue d’ensemble dans le [tableau de bord Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Pour plus d’informations, consultez [Tableau de bord Résumé dans l’analytique de la Place de marché commerciale](./summary-dashboard.md).
- Vos offres peuvent être listées sous **Me contacter**, qui sont des offres à consulter uniquement et qui ne peuvent pas être achetées par les clients dans la Place de marché. Bien que ces offres génèrent des acheteurs et soient partagées avec vous, aucune commande n’est créée pour ces offres car elles ne peuvent pas être achetées. Pour vérifier le type de référencement de vos offres, accédez à la page de configuration.

**Je sais que j’ai des données d’analytique, mais le message ci-dessous s’affiche :**

![Aucune donnée pour la plage de dates donnée](./media/analytics-faq-data-range.png)

Si vous recevez ce message, cela signifie que vous avez des données d’analytique, mais que vous n’avez pas de données pour la plage de dates que vous avez sélectionnée. Sélectionnez une autre plage de dates ou une plage de dates personnalisée pour afficher vos données depuis 2010. Pour plus d’informations, consultez la section Plage de dates du [Tableau de bord Résumé dans l’analytique de la Place de marché commerciale](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dictionnaire des termes relatifs aux données

| Nom de l'attribut | Rapports | Définition|
|---|---|---|
| Type de licence Azure | Client, Commande | Type de contrat de licence utilisé par les clients pour acheter Azure. Également appelé canal |
| Type de licence Azure : Fournisseur de solutions cloud | Client, Commande | Le client final se procure Azure et votre offre de la Place de marché par le biais de son fournisseur de solutions cloud, qui agit en tant que revendeur.|
| Type de licence Azure : Entreprise | Client, Commande | Le client final se procure Azure et votre offre de la Place de marché par le biais d'un contrat Entreprise, signé directement auprès de Microsoft.|
| Type de licence Azure : entreprise, via un revendeur  | Client, Commande | Le client final se procure Azure et votre offre de Place de marché par le biais d’un revendeur qui lui propose un contrat Entreprise avec Microsoft.|  |
| Type de licence Azure : paiement à l’utilisation| Client, Commande | Le client final se procure Azure et votre offre de la Place de marché dans le cadre d’un contrat « Paiement à l’utilisation », signé directement avec Microsoft.||
| Nom de l’instance cloud| JSON| Instance de Microsoft Cloud sur laquelle des machines virtuelles ont été déployées.||
| Nom de l’instance cloud : Azure Global| JSON| Cloud public global de Microsoft.|| |
| Nom de l’instance cloud : Azure Government | JSON| Clouds de Microsoft réservés aux gouvernements suivants : Chine, Allemagne ou États-Unis d’Amérique.| |
| Ville du client| Customer| Nom de ville fourni par le client final. La ville peut être différente de la ville associée à l’abonnement Azure du client.||
| Langue de communication du client  | Customer| Langue dans laquelle le client préfère communiquer.||
| Nom de la société du client | Client, Commande | Nom de société fourni par le client. Le nom peut être différent du nom de la ville associée à l’abonnement Azure du client.|  |
| Pays du client | Client, Commande | Nom de pays fourni par le client. Le pays peut être différent du pays associé à l’abonnement Azure du client.|  |
| Adresse e-mail du client| Customer| Adresse e-mail fournie par le client final. L’adresse e-mail peut être différente de l’adresse e-mail associée à l’abonnement Azure du client.||
| Prénom du client| Customer| Prénom fourni par le client. Le prénom peut être différent du prénom associé à l’abonnement Azure du client.| |
| ID de client | Client, Commande | Identificateur unique attribué à un client. Un client peut avoir zéro ou plusieurs abonnements de la Place de marché Azure.|  |
| Code postal du client  | Customer| Code postal fourni par le client. Le code postal peut être différent du code postal associé à l’abonnement Azure du client.| |
| État du client| Customer| État (adresse) fourni par le client. L’État peut être différent de l’État associé à l’abonnement Azure du client.| |
| Date d'acquisition| Customer| Date à laquelle le client a acheté pour la première fois une offre que vous avez publiée.| |
| Date d'annulation| Customer| Date à laquelle le client a annulé la dernière des offres achetées précédemment.||
| Nouveau client  | JSON| La valeur identifie un nouveau client achetant une ou plusieurs de vos offres pour la première fois (ou pas). La valeur est « Oui » si le mois calendaire correspond avec la « date d’acquisition ». La valeur est « Non » si le client a acheté une de vos offres avant le mois calendaire signalé. |
| Référence SKU en préversion| JSON| La valeur indique si vous avez étiqueté la référence SKU en tant que « préversion ». La valeur est « Oui » si la référence SKU a été étiquetée en conséquence, et seuls les abonnements Azure que vous avez autorisés peuvent déployer et utiliser cette image. La valeur est « Non » si la référence SKU n’a pas été identifiée en tant que « préversion ».  |
| Acceptation des messages promotionnels| Customer| La valeur indique si le client a accepté les messages promotionnels provenant d’éditeurs de manière proactive. À ce stade, nous n'offrons pas cette possibilité aux clients. Par conséquent, nous avons indiqué « Non » sur le tableau. Une mise à jour interviendra lorsque cette fonctionnalité sera déployée.|
| Type de licence de la Place de marché| JSON| Mode de facturation de l’offre de la Place de marché.||
| Type de licence de la Place de marché : facturé via Azure| JSON| Pour cette offre de la Place de marché, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)||
| Type de licence de la Place de marché : BYOL (apportez votre propre licence) | JSON| Le déploiement de la machine virtuelle requiert une clé de licence fournie par le client. Microsoft ne facture rien aux clients qui répertorient leurs offres de cette façon via la Place de marché.||
| Type de licence de la Place de marché : Gratuit| JSON| L'offre est configurée pour permettre à tous les utilisateurs d’y accéder gratuitement. Microsoft ne facture rien aux clients qui utilisent cette offre.||
| Type de licence de la Place de marché : Microsoft en tant que revendeur  | JSON| Microsoft est votre revendeur pour cette offre de la Place de marché.|  |
| ID d’abonnement de la Place de marché | Client, Commande | Identificateur unique associé à l’abonnement Azure que le client a utilisé pour acheter votre offre de la Place de marché. Anciennement, GUID d’abonnement Azure.||
| Nom de l’offre  | JSON| Nom de l’offre de la Place de marché.|| |
| Type d’offre  | JSON| Type de l’offre de la Place de marché Microsoft.|||
| Type d’offre : Application managée  | Commande, | Utilisez le type d’offre Application Azure : application managée lorsque les conditions suivantes sont requises : Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle ou d’une solution IaaS complète. Vous ou votre client exigez que la solution soit gérée par un partenaire. |
| Type d’offre : Azure Application| Commande, | Utilisez le type d’offre Azure Application : modèle de solution si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, en plus d’une simple machine virtuelle.||
| Type d’offre : Service de conseil| JSON| Les services de conseil de la Place de marché Azure aident les clients à se connecter aux services pour gérer et étendre leur utilisation d’Azure.| |
| Type d’offre : Conteneur | JSON| Utilisez le type d’offre Conteneur si votre solution est une image de conteneur Docker approvisionnée en tant que service de conteneur Azure Kubernetes.||
| Type d’offre : Dynamics 365 Business Central| JSON| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 for Finance and Operations| |
| Type d’offre : Dynamics 365 for Customer Engagement | JSON| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 for Customer Engagement.||
| Type d’offre : Module IoT Edge | JSON| Les modules Azure IoT Edge sont les plus petites unités de calcul gérées par IoT Edge. Ils peuvent contenir des services Microsoft (par exemple, Azure Stream Analytics), des services tiers ou votre propre code de solution. |
| Type d’offre : Application Power BI | JSON| Utilisez le type d’offre d'application Power BI lorsque vous déployez une application intégrée à Power BI.|  |
| Type d’offre : Application SaaS| JSON| Utilisez le type d’offre Applications SaaS pour permettre à votre client d’acheter votre solution technique SaaS sous la forme d’un abonnement.||
| Type d’offre : Machine virtuelle | JSON| Utilisez le type d’offre Machine virtuelle si vous déployez une appliance virtuelle dans le cadre de l’abonnement associé à votre client.||
| Type d’offre : Extension Visual Studio Marketplace  | JSON| Type d'offre précédemment disponible pour les développeurs d’extensions Azure DevOps. Désormais, les développeurs d'extensions Azure DevOps peuvent vendre leurs extensions directement aux clients. Les offres d'extensions peuvent être configurées comme payées ou à l'essai. |
| Date d’annulation de la commande| JSON| Date à laquelle la commande passée sur la Place de marché a été annulée.||
| ID de commande| JSON| Identificateur unique de la commande du client pour votre service de la Place de marché. Les offres basées sur l’utilisation de machines virtuelles ne sont pas associées à une commande.| |
| Date de la commande| JSON| Date à laquelle la commande de la Place de marché a été passée.|||
| État de la commande| JSON| État d’une commande de la Place de marché au moment de la dernière actualisation des données.|     |
| État de la commande : Actif  | JSON| Le client a passé commande et ne l’a pas annulée.|         |
| État de la commande : annulée | JSON| Le client a passé commande, puis l’a annulée.||
| Adresse e-mail du fournisseur| Customer| Adresse e-mail du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur. Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.|
| Nom du fournisseur| Customer| Nom du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur. Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.|
| SKU| JSON| Nom de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre.||
| Date de fin d'essai| JSON| La date de fin de la période d’essai de cette commande approche ou est dépassée.||

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des rapports d’analytique disponibles sur la place de marché commerciale de l’Espace partenaires, voir [Analytique de la place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché pour vos offres, voir [Tableau de bord Résumé dans l’analytique de la place de marché commerciale](./summary-dashboard.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, voir [Tableau de bord des commandes dans l’analytique de la place de marché commerciale](./orders-dashboard.md).
- Pour consulter les métriques d’utilisation et de facturation à l’usage des offres de machines virtuelles, voir [Tableau de bord de l’utilisation dans l’analytique de la place de marché commerciale](./usage-dashboard.md).
- Pour des informations détaillées sur vos clients, y compris des tendances de croissance, voir [Tableau de bord client dans l’analytique de la place de marché commerciale](./customer-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, voir [Tableau de bord des téléchargements dans l’analytique de la place de marché commerciale](./downloads-dashboard.md).
- Pour voir tous les commentaires des clients sur les offres disponibles dans AppSource et la Place de marché Azure, consultez [Tableau de bord Évaluations et avis dans l’analytique de la Place de marché commerciale](./ratings-reviews.md).
