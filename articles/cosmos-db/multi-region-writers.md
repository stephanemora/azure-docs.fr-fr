---
title: Multimaître à une échelle globale avec Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 12306b7868fa7fb2321f26657aab81beabb9db35
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Multimaître à une échelle globale avec Azure Cosmos DB 
 
Le développement d’applications distribuées à l’échelle mondiale qui répondent avec des latences locales tout en conservant des vues de données cohérentes dans le monde entier est un véritable défi. Les clients utilisent des bases de données distribuées à l’échelle mondiale car ils ont besoin d’améliorer la latence d’accès aux données, d’obtenir une haute disponibilité des données, de garantir la reprise d’activité après sinistre et de répondre aux besoins de leur entreprise. Multimaître dans Azure Cosmos DB fournit des niveaux élevés de disponibilité (99,999 %), une latence inférieure à dix millisecondes pour l’écriture des données et une scalabilité avec une prise en charge intégrée, complète et flexible de la résolution de conflit. Ces fonctionnalités simplifient considérablement le développement des applications distribuées à l’échelle mondiale. Pour les applications distribuées à l’échelle mondiale, la prise en charge du multimaître est cruciale. 

![Architecture multimaître](./media/multi-region-writers/multi-master-architecture.png)

Avec la prise en charge du multimaître Azure Cosmos DB, vous pouvez effectuer des écritures sur des conteneurs de données (par exemple des collections, des graphes et des tables) distribués n’importe où dans le monde. Vous pouvez mettre à jour des données dans n’importe quelle région associée à votre compte de base de données. Ces mises à jour de données peuvent se propager de façon asynchrone. Outre un accès rapide et une faible latence d’écriture pour vos données, le multimaître fournit également une solution pratique aux problèmes de basculement et d’équilibrage de charge. En résumé, avec Azure Cosmos DB vous bénéficiez d’une latence d’écriture inférieure à 10 ms au 99e centile n’importe où dans le monde, d’une disponibilité d’écriture et de lecture de 99,999 % n’importe où dans le monde et de la possibilité de mettre à l’échelle le débit d’écriture et de lecture n’importe où dans le monde.   

> [!IMPORTANT]
> La prise en charge du multimaître est en préversion privée. Pour utiliser la préversion, [inscrivez-vous](#sign-up-for-multi-master-support) dès maintenant.

## <a name="sign-up-for-multi-master-support"></a>S’inscrire pour la prise en charge du multimaître

Si vous avez déjà un abonnement Azure, vous pouvez vous inscrire pour participer au programme de préversion du multimaître dans le portail Azure. Si vous débutez avec Azure, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/free) qui vous donnera 12 mois d’accès à Azure Cosmos DB. Procédez comme suit pour demander l’accès au programme de préversion du multimaître.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.  

2. Sur la page Nouveau compte, nommez votre compte Azure Cosmos DB, puis sélectionnez l’API, l’abonnement, le groupe de ressources et la localisation.  

3. Ensuite, sélectionnez **Inscrivez-vous pour une préversion aujourd’hui** sous le champ de préversion du multimaître.  

   ![S’inscrire pour la préversion du multimaître](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. Dans le volet **Inscrivez-vous pour une préversion aujourd’hui**, cliquez sur **OK**. Une fois que la demande est envoyée, l’état passe à **En attente d’approbation** dans le panneau de création du compte.  

Après avoir soumis votre demande, vous recevrez par e-mail la notification indiquant que votre demande a été approuvée. En raison du volume élevé de demandes, vous devez recevoir une notification dans un délai d’une semaine. Vous n’avez pas besoin de créer un ticket de support pour effectuer la demande. Les demandes sont étudiées dans l’ordre dans lequel elles sont reçues.

## <a name="a-simple-multi-master-example--content-publishing"></a>Exemple simple d’architecture multimaître : publication de contenu  

Examinons un scénario réel qui illustre comment utiliser la prise en charge du multimaître avec Azure Cosmos DB. Prenons l’exemple d’une plateforme de contenu reposant sur Azure Cosmos DB. Voici certaines exigences que cette plateforme doit respecter pour une expérience utilisateur exceptionnelle pour les éditeurs et les consommateurs. 

* Les auteurs et les abonnés sont répartis dans le monde entier.  

* Les auteurs doivent publier (écrire) les articles dans leur région locale (la plus proche).  

* Les auteurs ont des lecteurs/abonnés de leurs articles répartis dans le monde entier.  

* Les abonnés doivent recevoir une notification lorsque de nouveaux articles sont publiés.  

* Les abonnés doivent être en mesure de lire les articles de leur région. Ils doivent également être en mesure d’ajouter des avis concernant ces articles.  

* Tout le monde, y compris l’auteur des articles, doit être en mesure de visualiser tous les avis liés aux articles à partir d’une région.  

En supposant qu’il y a des millions de consommateurs et d’éditeurs avec des milliards d’articles, nous devrons bientôt faire face à des problèmes de mise à l’échelle et de garantie de localité d’accès. Un tel cas d’utilisation est un candidat parfait pour le multimaître Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Avantages offerts par la prise en charge du multimaître 

La prise en charge du multimaître est essentielle pour les applications distribuées à l’échelle mondiale. L’architecture multimaître est composée de [plusieurs régions maîtresses](distribute-data-globally.md) qui participent de manière égale à un modèle « écriture n’importe où » (modèle actif-actif), qui permet de s’assurer que les données sont disponibles à tout moment là où vous en avez besoin. Les mises à jour apportées à une région sont propagées de façon asynchrone à toutes les autres régions (qui elles-mêmes sont des régions maîtresses). Les régions Azure Cosmos DB jouant le rôle de régions maîtresses dans une configuration multimaître opèrent automatiquement afin de faire converger les données de tous les réplicas et de garantir la [cohérence globale et l’intégrité des données](consistency-levels.md). L’illustration suivante montre la réplication en lecture-écriture pour une architecture à maître unique et multimaître.

![Maître unique et multimaître](./media/multi-region-writers/single-vs-multi-master.png)

L’implémentation du multimaître ajoute une charge de travail pour les développeurs. Les clients à grande échelle qui tentent d’implémenter eux-mêmes le multimaître peuvent consacrer des centaines d’heures à la configuration et aux tests d’une configuration multimaître à l’échelle mondiale, et nombre d’entre eux ont une équipe d’ingénieurs dédiée dont la seule tâche consiste à surveiller et à tenir à jour la réplication multimaître. Si vous essayez de créer et de gérer vous-même votre configuration multimaître, vous devrez faire face à des coûts beaucoup plus élevés, et vous gaspillerez du temps et des ressources qui pourraient être consacrées à l’innovation de l’application. Azure Cosmos DB offre une prise en charge du multimaître « prête à l’emploi » et évite aux développeurs de devoir assumer cette charge de travail supplémentaire.  

En résumé, le multimaître offre les avantages suivants :

* **Amélioration de la reprise d’activité après sinistre, de la disponibilité d’écriture et du basculement** : Le multimaître peut servir à accroître la haute disponibilité d’une base de données critique. Par exemple, une base de données multimaître peut répliquer des données d’une région vers une région de basculement quand la région principale devient indisponible suite à une panne ou un sinistre régional. Cette région de basculement servira de région principale entièrement fonctionnelle pour prendre en charge l’application. Le multimaître augmente la « capacité de survie » en cas de catastrophe naturelle, de panne de courant ou de sabotage, car les régions restantes peuvent se trouver dans un multimaître géographiquement différent avec une disponibilité en écriture garantie supérieure à 99,999 %. 

* **Amélioration de la latence d’écriture pour les utilisateurs finaux** : Plus vos données seront proches de l’utilisateur final, meilleure sera l’expérience. Par exemple, si vous avez des utilisateurs en Europe mais que votre base de données est aux États-Unis ou en Australie, la latence supplémentaire s’élève respectivement à environ 140 ms et 300 ms pour ces régions. Tout délai est inacceptable quant il s’agit de nombreux jeux populaires, d’exigences relatives aux opérations bancaires ou d’applications interactives (web ou mobiles). La latence joue un rôle considérable dans la perception du client. Il a été prouvé qu’une faible latence était synonyme d’expérience de qualité et avait un impact sur le comportement des utilisateurs. Avec les progrès technologiques actuels (en particulier l’avènement de la réalité augmentée, de la réalité virtuelle et de la réalité mixte, qui nécessitent des expériences toujours plus réalistes et immersives), les développeurs se doivent de produire des logiciels avec des exigences de latence strictes. Ainsi, il est plus important d’avoir des applications et des données (contenu pour les applications) disponibles localement. Avec le multimaître dans Azure Cosmos DB, la rapidité est aussi élevée que s’il s’agissait de lectures et d’écritures locales ordinaires, et les performances sont améliorées à l’échelle mondiale grâce à la géodistribution.  

* **Amélioration de la scalabilité d’écriture et du débit d’écriture** : Le multimaître procure un débit plus élevé et une meilleure utilisation tout en offrant plusieurs modèles de cohérence avec des garanties d’exactitude mentionnées dans des contrats SLA. 

  ![Mise à l’échelle du débit d’écriture avec le multimaître](./media/multi-region-writers/scale-write-throughput.png)

* **Amélioration de la prise en charge des environnements déconnectés (par exemple avec les appareils de périphérie)**  : Le multimaître permet aux utilisateurs de répliquer tout ou un sous-ensemble de données d’un appareil de périphérie vers une région plus proche dans un environnement déconnecté. Ce scénario est typique des systèmes d’automatisation de la force de vente, où l’ordinateur portable d’un individu (un appareil déconnecté) stocke un sous-ensemble de données associées à ce vendeur. Les régions maîtresses dans le cloud situées n’importe où dans le monde peuvent être la cible de la copie à partir des appareils de périphérie distants.  

* **Équilibrage de charge** : Avec le multimaître, la charge sur l’application peut être rééquilibrée en déplaçant des utilisateurs/charges de travail d’une région surchargée vers des régions où la charge est répartie équitablement. La capacité d’écriture peut facilement être étendue en ajoutant une nouvelle région puis en faisant basculer certaines écritures vers la nouvelle région. 

* **Amélioration de l’utilisation de la capacité provisionnée** : Avec le multimaître, pour les charges de travail mixtes et nécessitant beaucoup d’écritures, vous pouvez saturer la capacité déployée parmi plusieurs régions.  Dans certains cas, vous pouvez redistribuer les lectures et les écritures de façon plus égale, ce qui permet de réduire le débit nécessaire au provisionnement et les coûts pour les clients.  

* **Architectures d’applications plus simples et plus résistantes** : Les applications qui adoptent une architecture multimaître bénéficient d’une résilience des données garantie.  Azure Cosmos DB masque toute la complexité et peut simplifier considérablement la conception et l’architecture de l’application. 

* **Test de basculement sans risque** : Les tests de basculement n’entraîneront aucune dégradation du débit d’écriture. Avec le multimaître, toutes les autres régions étant des maîtres complets, le basculement n’aura que peu d’impact sur le débit d’écriture.  

* **Réduction du coût total de possession et des opérations de développement** : Répondre aux objectifs en matière de  scalabilité, de performances, de distribution globale et de temps de récupération est souvent onéreux en raison du coût lié aux modules complémentaires ou à la maintenance d’une infrastructure de sauvegarde qui est au repos jusqu’à ce qu’une situation d’urgence survienne. Avec le multimaître Azure Cosmos DB secondé par les contrats SLA les plus exigeants du secteur, les développeurs n’ont plus besoin de créer et de tenir eux-mêmes à jour la « logique de collage backend ». Ils bénéficient de la tranquillité d’esprit nécessaire pour exécuter leurs charges de travail critiques. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Cas d’utilisation où la prise en charge du multimaître est nécessaire

Il existe de nombreux cas d’utilisation pour le multimaître dans Azure Cosmos DB : 

* **IoT** : Le multimaître Azure Cosmos DB permet de simplifier l’implémentation distribuée du traitement de données IoT. Les déploiements de périphérie géodistribués qui utilisent des types de données répliqués sans conflit (CRDT) doivent souvent effectuer le suivi de données de série chronologique à partir de plusieurs emplacements. Chaque appareil peut être hébergé dans l’une des régions les plus proches, et un appareil peut voyager (par exemple, s’il s’agit d’une voiture) et être réhébergé de manière dynamique afin d’écrire dans une autre région.  

* **Commerce électronique** : La garantie d’une expérience utilisateur satisfaisante dans les scénarios de commerce électronique exige une haute disponibilité et une résilience face aux scénarios de défaillance. En cas de défaillance d’une région, les sessions utilisateur, les paniers et les listes de souhaits actives doivent être récupérés par une autre région sans interruption et sans perte d’état. Entre-temps, les mises à jour effectuées par l’utilisateur doivent être gérées correctement (par exemple, les ajouts et les suppressions dans le panier doivent être transférés). Avec le multimaître, Azure Cosmos DB peut gérer ces scénarios en douceur, avec une transition fluide entre les régions actives tout en conservant une vue cohérente du point de vue de l’utilisateur. 

* **Détection des fraudes/anomalies** : Souvent, les applications qui surveillent l’activité de l’utilisateur ou du compte sont distribuées à l’échelle mondiale et doivent effectuer le suivi de plusieurs événements simultanément. Lors de la création et de la gestion des scores pour un utilisateur, les actions provenant de différentes régions géographiques doivent mettre à jour simultanément les scores afin de conserver l’alignement des métriques de risque. Grâce à Azure Cosmos DB, les développeurs n’ont pas besoin de gérer les scénarios de conflit au niveau de l’application. 

* **Collaboration** : Pour les applications dont le classement dépend de la popularité d’articles tels que des biens en vente ou des éléments multimédia à consommer, etc. Le suivi de la popularité dans différentes régions géographiques peut être compliqué, en particulier quand des droits d’auteur doivent être versés ou que des décisions en temps réel relatives à la publicité doivent être prises. Le classement, le tri et la création de rapports parmi plusieurs régions du monde entier, en temps réel avec Azure Cosmos DB, permet aux développeurs de fournir des fonctionnalités avec un effort minimal et sans transiger sur la latence. 

* **Contrôle** : Le comptage et la régulation de l’utilisation (tels que les appels d’API, le nombre de transactions/seconde et les minutes utilisées) peuvent être implémentés globalement en toute simplicité à l’aide de l’architecture multimaître Azure Cosmos DB. La résolution de conflit intégrée garantit à la fois la précision des chiffres et la régulation en temps réel. 

* **Personnalisation** : Que vous teniez à jour des compteurs géodistribués qui déclenchent des actions telles que des récompenses grâce aux points de fidélité, ou que vous implémentiez des vues de session utilisateur personnalisées, la haute disponibilité et le comptage géodistribué simplifié fournis par Azure Cosmos DB permettent aux applications de délivrer de hautes performances sans sacrifier la simplicité. 

## <a name="conflict-resolution-with-multi-master"></a>Résolution de conflit avec le multimaître 

Avec le multimaître, le défi est souvent que deux réplicas (ou plus) du même enregistrement peuvent être mis à jour simultanément par différents writers dans plusieurs régions différentes. Les écritures simultanées peuvent générer deux versions différentes du même enregistrement, et sans la résolution de conflit intégrée l’application doit elle-même résoudre le conflit afin d’éliminer ce problème.  

**Exemple** : Supposez que vous utilisez Azure Cosmos DB comme magasin de persistance pour une application de panier d’achat, et que cette application est déployée dans deux régions : États-Unis de l’Est et États-Unis de l’Ouest.  Supposez maintenant qu’un utilisateur à San Francisco ajoute un élément à son panier (par exemple un livre) tandis qu’à peu près au même moment un processus de gestion des stocks dans la région États-Unis de l’Est invalide un autre élément du panier (par exemple un nouveau téléphone) pour ce même utilisateur en réponse à une notification du fournisseur indiquant que la date de sortie a été différée. À l’instant T1, les enregistrements du panier dans les deux régions sont différents. La base de données utilisera son mécanisme de réplication et de résolution de conflit pour résoudre cette incohérence, et l’une des deux versions du panier finira par être sélectionnée. Avec l’heuristique de résolution de conflit la plus souvent appliquée par les bases de données multimaîtres (par exemple, la dernière écriture l’emporte), il est impossible à l’utilisateur ou à l’application de prédire quelle version sera sélectionnée. Dans les deux cas, des données sont perdues ou un comportement inattendu peut se produire. Si la version de la région Est est sélectionnée, la sélection par l’utilisateur d’un nouvel article (un livre) est perdue, et si la région Ouest est sélectionnée, l’article précédemment sélectionné (le téléphone) figure toujours dans le panier d’achat. Dans les deux cas, des informations sont perdues. Pour finir, tout autre processus inspectant le panier entre les instants T1 et T2 constatera également un comportement non déterministe. Par exemple, un processus en arrière-plan qui sélectionne l’entrepôt d’exécution et met à jour les frais d’expédition du panier produira des résultats qui seront en conflit avec le contenu final du panier. Si le processus s’exécute dans la région Ouest et que l’alternative 1 devient réalité, il calculera les frais d’expédition pour deux articles, même si le panier ne contiendra bientôt qu’un seul article, le livre. 

Azure Cosmos DB implémente la logique de gestion des conflits d’écriture dans le moteur de base de données proprement dit. Azure Cosmos DB offre une **prise en charge de la résolution de conflit complète et flexible** en proposant plusieurs modèles de résolution de conflit, notamment Automatique (types de données répliqués sans conflit), Priorité à la dernière écriture et Personnalisé (procédure stockée). Les modèles de résolution de conflit fournissent des garanties d’exactitude et de cohérence, et ils évitent aux développeurs de devoir se soucier de la cohérence, de la disponibilité, des performances, de la latence de réplication et de combinaisons complexes d’événements en cas de géoréplication et de conflit d’écriture inter-régions.  

  ![Résolution de conflit multimaître](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Azure Cosmos DB offre trois types de modèle de résolution de conflit. La sémantique des modèles de résolution de conflit est la suivante : 

**Automatique** : Il s’agit de la stratégie de résolution de conflit par défaut. Quand vous la sélectionnez, Azure Cosmos DB résout automatiquement les mises à jour conflictuelles du côté serveur et fournit des garanties élevées de cohérence finale. En interne, Azure Cosmos DB implémente la résolution automatique de conflit en tirant parti des types de données répliqués sans conflit à l’intérieur du moteur de base de données.  

**Priorité à la dernière écriture** : Cette stratégie vous permet de résoudre les conflits en fonction de la propriété d’horodateur synchronisée définie par le système ou d’une propriété personnalisée définie sur la version conflictuelle des enregistrements. La résolution de conflit se produit côté serveur, et la version ayant l’horodateur le plus récent l’emporte.  

**Personnalisé** : Vous pouvez inscrire une logique de résolution de conflit définie par l’application en inscrivant une procédure stockée. La procédure stockée sera appelée en cas de détection de conflit de mise à jour dans le cadre d’une transaction de base de données, côté serveur. Si vous sélectionnez cette option mais que vous n’inscrivez pas de procédure stockée (ou si la procédure stockée lève une exception au moment de l’exécution), vous pouvez accéder à toutes les versions conflictuelles par le biais du flux de conflits et les résoudre individuellement.  

## <a name="next-steps"></a>Étapes suivantes  

Dans cet article, vous avez découvert comment utiliser l’architecture multimaître distribuée à l’échelle mondiale avec Azure Cosmos DB. Vous pouvez maintenant examiner les ressources suivantes : 

* [Découvrez comment Azure Cosmos DB prend en charge la distribution à l’échelle mondiale](distribute-data-globally.md)  

* [Découvrez-en plus le basculement automatique dans Azure Cosmos DB](regional-failover.md)  

* [Découvrez-en plus sur la cohérence globale avec Azure Cosmos DB](consistency-levels.md)  

* Développer en mode multirégions à l’aide d’Azure Cosmos DB : [API SQL](tutorial-global-distribution-sql-api.md), [API MongoDB](tutorial-global-distribution-mongodb.md) ou [API Table](tutorial-global-distribution-table.md)  