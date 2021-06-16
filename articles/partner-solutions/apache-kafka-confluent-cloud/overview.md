---
title: Vue d’ensemble d’Apache Kafka sur Confluent Cloud - Solutions partenaires Azure
description: En savoir plus sur l’utilisation d’Apache Kafka dans Cloud Confluent sur la Place de marché Azure.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: 6fbd6b72c17b8483f9fdb5bc227625fd92009594
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952631"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Qu’est-ce qu’Apache Kafka pour Confluent Cloud ?

Apache Kafka pour Cloud Confluent est une offre de la Place de marché Azure, qui fournit Apache Kafka en tant que service. Comme il s’agit d’une solution entièrement gérée, vous pouvez vous concentrer sur la création de vos applications plutôt que sur la gestion des clusters.

Pour réduire le fardeau de la gestion multiplateforme, Microsoft s’est associé à Confluent Cloud pour construire une couche de provisionnement intégrée d'Azure à Confluent Cloud. Vous profitez ainsi d’une expérience consolidée pour l’utilisation de Confluent Cloud sur Azure. Vous pouvez facilement intégrer et gérer Confluent Cloud avec vos applications Azure.

Auparavant, vous deviez acheter l’offre Confluent Cloud sur la Place de marché, puis configurer séparément le compte dans Confluent Cloud. Pour gérer les configurations et les ressources, vous deviez naviguer entre les portails pour Azure et Confluent Cloud.

À présent, vous provisionnez les ressources Confluent Cloud via un fournisseur de ressources nommé **Microsoft.Confluent**. Vous créez et gérez des ressources d’organisation Confluent Cloud via le [portail Azure](https://portal.azure.com/), [Azure CLI](/cli/azure/)ou des [SDK Azure](/azure/index.yml#languages-and-tools). Confluent Cloud possède et exécute l’application SaaS (Software as a Service), y compris les environnements, les clusters, les rubriques, les clés API et les connecteurs gérés.

## <a name="capabilities"></a>Fonctions

L’intégration étroite entre Confluent Cloud et Azure offre les fonctionnalités suivantes :

- Provisionnez une nouvelle ressource d’organisation Confluent Cloud à partir du portail Azure avec une infrastructure entièrement gérée.
- Simplifiez l’authentification unique (SSO) à partir d’Azure pour Confluent Cloud avec Azure Active Directory (Azure AD). Aucune authentification distincte n’est nécessaire à partir du portail Confluent Cloud.
- Bénéficiez d’une facturation unifiée de la consommation Confluent Cloud via la fonctionnalité de facturation de l’abonnement Azure.
- Gérez des ressources Confluent Cloud à partir du portail Azure et suivez-les dans la page **Toutes les ressources** avec vos autres ressources Azure.

## <a name="confluent-organization"></a>Organisation Confluent

Une organisation Confluent est une ressource qui fournit le mappage entre les ressources Azure et Confluent Cloud. Il s’agit de la ressource parente pour les autres ressources Confluent Cloud.

Chaque abonnement Azure peut contenir plusieurs plans Confluent. Chaque plan Confluent est mappé à un compte d’utilisateur et à une organisation dans le portail Confluent. Au sein de chaque organisation Confluent, vous pouvez créer plusieurs environnements, clusters, rubriques et connecteurs.

Lorsque vous provisionnez une ressource Cloud Confluent dans Azure, vous bénéficiez d’un ID d’organisation Confluent, d’un environnement par défaut et d’un compte d’utilisateur. Pour plus d’informations, consultez [Démarrage rapide : Prise en main de Confluent Cloud sur Azure](create.md).

Pour la facturation, chaque offre Confluent Cloud achetée dans la Place de marché est mappée à une organisation Confluent unique.

## <a name="single-sign-on"></a>Authentification unique

Lorsque vous vous connectez au portail Azure, vos informations d’identification sont également utilisées pour la connexion au portail SaaS Confluent Cloud. L’expérience utilise [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) et l’[authentification unique Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md) pour vous garantir une connexion facile et sécurisée.

Pour plus d’informations, consultez [Authentification unique](manage.md#single-sign-on).

## <a name="billing"></a>Facturation

Deux options de facturation sont disponibles : un plan mensuel avec paiement au fur et à mesure, et un plan d’engagement.

- Pour le **plan mensuel avec paiement au fur et à mesure**, les frais de consommation Confluent Cloud apparaissent sur votre facture mensuelle Azure.
- Avec un **plan d’engagement**, vous vous inscrivez pour un montant minimal de dépenses et bénéficiez d’une remise sur votre utilisation validée de Confluent Cloud.

Vous choisissez votre option de facturation lors de la création du service.

## <a name="connector-to-azure-cosmos-db"></a>Connecteur à Azure Cosmos DB

À partir du client Confluent Hub, installez le connecteur Cosmos DB comme recommandé dans l’[offre Confluent Hub](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos). 

Pour installer le connecteur manuellement, commencez par télécharger un uber JAR à partir de la [page des versions de Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Vous pouvez également [créer votre propre uber JAR directement à partir du code source](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Terminez l’installation en suivant les instructions décrites dans la documentation de Confluent relative à l’[installation manuelle des connecteurs](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="confluent-links"></a>Liens Confluent

Afin d’obtenir une aide supplémentaire sur l’utilisation d’Apache Kafka pour Confluent Cloud, consultez les liens suivants vers le [site Confluent](https://docs.confluent.io/home/overview.html).

Pour en savoir plus sur les options de facturation, consultez :

* [Place de marché Azure avec paiement au fur et à mesure](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Place de marché Azure avec engagements](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Pour en savoir plus sur la gestion des solutions, consultez :

* [Créer un cluster dans Confluent Cloud](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Environnements Confluent Cloud](https://docs.confluent.io/current/cloud/using/environments.html)
* [Concepts de base sur Confluent Cloud](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Pour obtenir de l’aide et consulter les conditions d’utilisation, consultez :

* [Support Confluent](https://support.confluent.io)
* [Conditions d'utilisation](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Étapes suivantes

Afin de créer une instance d’Apache Kafka pour Confluent Cloud, consultez [Démarrage rapide : Prise en main de Confluent Cloud sur Azure](create.md).