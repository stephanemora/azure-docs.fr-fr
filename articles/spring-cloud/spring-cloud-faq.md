---
title: Questions fréquentes (FAQ) sur Azure Spring Cloud | Microsoft Docs
description: Cette page répond aux questions fréquentes à propos d’Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229248"
---
# <a name="azure-spring-cloud-faq"></a>Questions fréquentes sur Azure Spring Cloud

Cette page répond aux questions fréquentes à propos d’Azure Spring Cloud. 

## <a name="general"></a>Généralités

### <a name="why-azure-spring-cloud"></a>Pourquoi Azure Spring Cloud ?

Azure Spring Cloud fournit une plateforme PaaS pour les développeurs Spring Cloud. Azure Spring Cloud gère votre infrastructure d’applications pour vous permettre de vous concentrer sur le code et la logique métier des applications. Les principales fonctionnalités intégrées à Azure Spring Cloud incluent Eureka, le serveur de configuration, le serveur Service Registry, le service Pivotal Build, les déploiements bleus-verts, etc. Ce service permet également aux développeurs de lier leurs applications à d’autres services Azure, tels que Azure Cosmos DB, Azure Database pour MySQL et Azure Cache pour Redis.

Azure Spring Cloud améliore l’expérience de diagnostic des applications pour les développeurs et les opérateurs en intégrant Azure Monitor, Application Insights et Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quels sont les plans de service offerts par Azure Spring Cloud ?

Azure Spring Cloud offre un seul plan de service pendant la période de préversion.  Un déploiement Spring Cloud contient 16 cœurs de processeurs virtuels et 32 Go de mémoire.  La limite supérieure de chaque instance de microservice dans un déploiement est de 4 cœurs de processeurs virtuels avec 8 Go de mémoire.

Ressource | Montant
------- | -------
Instances d’application par application Spring | 20
Nombre total d’instances d’application par instance du service Azure Spring Cloud | 500
Instances du service Azure Spring Cloud par région et par abonnement | 10
Volumes persistants | 10 x 50 Go

\* _Pour relever les limites, ouvrez un [ticket de support](https://azure.microsoft.com/support/faq/)._

Pour plus d’informations, consultez les [Questions fréquentes sur le support Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Qu’en est-il de la sécurité d’Azure Spring Cloud ?

La sécurité et la confidentialité figurent parmi les principales priorités pour les clients Azure et Azure Spring Cloud. Azure garantit que seul le client a accès aux données, aux journaux ou aux configurations des applications en chiffrant de façon sécurisée toutes ces données. Toutes les instances de service dans Azure Spring Cloud sont isolées les unes des autres.

Azure Spring Cloud offre une gestion complète des certificats et de SSL.

Les correctifs de sécurité critiques pour les runtimes OpenJDK et Spring Cloud sont appliqués à Azure Spring Cloud dès que possible.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Dans quelles régions Azure Spring Cloud est-il disponible ?

USA Est, USA Ouest 2, Europe Ouest et Asie Sud-Est.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quelles sont les limitations connues d’Azure Spring Cloud ?

Pendant la préversion, Azure Spring Cloud a les limitations connues suivantes :

* `spring.application.name` sera remplacé par le nom de l’application utilisé pour créer chaque application.
* `server.port` n’est pas autorisé dans le fichier de configuration provenant du dépôt Git. Si vous l’ajoutez au fichier de configuration, votre application risque de ne pas être accessible depuis d’autres applications ou depuis Internet.
* Le portail Azure et les modèles Azure Resource Manager ne prennent pas en charge le chargement de packages d’application. Vous pouvez charger des packages d’application uniquement en déployant l’application via Azure CLI.
* Pour en savoir plus sur les limitations de quota, consultez [Quels sont les plans de service offerts par Azure Spring Cloud ?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Comment puis-je donner un feedback et signaler des problèmes ?

Si vous rencontrez des problèmes avec Azure Spring Cloud, créez une [demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pour soumettre une demande de fonctionnalité ou fournir des commentaires, accédez à la page de [commentaires sur Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Développement

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Je suis développeur Spring Cloud, mais je débute sur Azure. Quel est le moyen le plus rapide pour apprendre à développer une application Azure Spring Cloud ?

Pour connaître le moyen le plus rapide de se lancer avec Azure Spring Cloud, suivez les instructions du [guide de démarrage rapide : Lancer une application Azure Spring Cloud en utilisant le portail Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quel runtime Java est pris en charge par Azure Spring Cloud ?

Azure Spring Cloud prend en charge Java 8 et 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Où puis-je voir les journaux et les métriques de mon application Spring Cloud ?

Vous trouverez les métriques sous l’onglet Vue d’ensemble de l’application et sous l’onglet [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Spring Cloud prend en charge l’exportation des journaux et des métriques de votre application Spring Cloud vers Stockage Azure, EventHub et [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Le nom de la table dans Log Analytics est *AppPlatformLogsforSpring*. Pour savoir comment l’activer, consultez [Services de diagnostic](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud prend-il en charge le suivi distribué ?

Oui. Pour plus d’informations, consultez [Tutoriel : Utiliser le suivi distribué avec Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Quels sont les types de ressources pris en charge par la liaison de service ?

Trois services sont actuellement pris en charge : Azure Cosmos DB, Azure Database pour MySQL et Cache Azure pour Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Puis-je voir, ajouter ou déplacer des volumes persistants depuis mes applications ?

Oui.

## <a name="deployment"></a>Déploiement

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud prend-il en charge le déploiement bleu-vert ?
Oui. Pour plus d’informations, consultez [Configuration de votre environnement intermédiaire](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Puis-je accéder à Kubernetes pour manipuler mes conteneurs d’application ?

Non.  Azure Spring Cloud rend l’architecture sous-jacente abstraite pour le développeur, ce qui vous permet de vous concentrer sur le code et la logique métier de l’application.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud prend-il en charge la création de conteneurs à partir d’une source ?

Oui. Pour plus d’informations, consultez [Lancer votre application Spring Cloud à partir du code source](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud prend-il en charge la mise à l’échelle automatique dans les instances d’application ?

Non.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quelles sont les bonnes pratiques pour la migration de microservices Spring Cloud existants vers Azure Spring Cloud ?

À mesure que vous migrez des microservices Spring Cloud existants vers Azure Spring Cloud, il est judicieux d’observer les meilleures pratiques suivantes :
* toutes les dépendances de l’application doivent être résolues.
* Préparez les entrées, les variables d’environnement et les paramètres JVM de votre configuration pour pouvoir les comparer au déploiement dans Azure Spring Cloud.
* Si vous voulez utiliser la liaison de service, parcourez vos services Azure et assurez-vous d’avoir défini les autorisations d’accès appropriées.
* Nous vous recommandons de supprimer ou de désactiver les services incorporés qui peuvent entrer en conflit avec les services managés par Azure Spring Cloud, comme notre service de découverte de service, le serveur de configuration, etc.
* Nous vous recommandons d’utiliser des bibliothèques Pivotal Spring officielles et stables. Les versions non officielles, bêta ou dupliquées (forked) des bibliothèques Pivotal Spring ne bénéficient pas de prise en charge des contrats SLA.

Après la migration, supervisez les métriques de processeur/mémoire et le trafic réseau pour vérifier que les instances d’application sont mises à l’échelle de façon appropriée.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez d’autres questions, consultez le [Guide de résolution des problèmes d’Azure Spring Cloud](spring-cloud-troubleshoot.md).
