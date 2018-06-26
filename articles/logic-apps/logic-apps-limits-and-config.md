---
title: Limites et configuration - Azure Logic Apps | Microsoft Docs
description: Limites de service et valeurs de configuration pour Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2534210c903e77462ece91c577d731d9c8e3726f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299713"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limites et informations de configuration pour Azure Logic Apps

Cet article décrit les limites et les détails de configuration liés à la création et à l’exécution de workflows automatisés avec Azure Logic Apps. Pour Microsoft Flow, consultez [Limites et configuration dans Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limites de définition

Les limites pour la définition d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Actions par flux de travail | 500 | Pour étendre cette limite, vous pouvez au besoin ajouter des workflows imbriqués. |
| Niveaux d’imbrication d’actions autorisés | 8 | Pour étendre cette limite, vous pouvez au besoin ajouter des workflows imbriqués. | 
| Flux de travail par région et par abonnement | 1 000 | | 
| Déclencheurs par flux de travail | 10 | Lors de l’utilisation du mode Code, et non du concepteur | 
| Limite de cas de basculement d’étendue | 25 | | 
| Variables par workflow | 250 | | 
| Caractères par expression | 8 192 | | 
| Taille maximale de `trackedProperties` | 16 000 caractères | 
| Nom pour `action` ou `trigger` | 80 caractères | | 
| Longueur de `description` | 256 caractères | | 
| Valeur maximale pour `parameters` | 50 | | 
| Valeur maximale pour `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Durée d’exécution et limites de rétention

Les limites pour l’exécution d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
|------|-------|-------| 
| Durée d’exécution | 90 jours | Pour changer cette limite, consultez [Modifier la durée d’exécution et la rétention de stockage](#change-duration). | 
| Rétention de stockage | 90 jours à compter de l’heure de début de l’exécution | Pour changer cette limite, consultez [Modifier la durée d’exécution et la rétention de stockage](#change-retention). | 
| Intervalle de périodicité minimal | 1 seconde | | 
| Intervalle de périodicité maximal | 500 jours | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Modifier la durée d’exécution et la rétention de stockage

Vous pouvez remplacer cette limite par une valeur comprise entre 7 jours et 90 jours. Pour pouvoir dépasser la limite maximale, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) pour obtenir de l’aide.

1. Dans le portail Azure, dans le menu de votre application logique, choisissez **Workflow settings** (Paramètres de workflow). 

2. Sous **Options de runtime**, dans la liste **Conservation de l’historique des exécutions en jours**, choisissez **Custom** (Personnalisée). 

3. Entrez le nombre de jours souhaité ou faites glisser le curseur pour sélectionner ce nombre.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Limites de bouclage et de décomposition

Les limites pour l’exécution d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Itérations Until | 5 000 | | 
| Éléments ForEach | 100 000 | Vous pouvez utiliser [l’action de requête](../connectors/connectors-native-query.md) pour filtrer des tableaux plus grands au besoin. | 
| Parallélisme ForEach | 50 | Valeur par défaut : 20. <p>Pour modifier ce niveau par défaut dans une boucle ForEach, définissez la propriété `runtimeConfiguration` dans l’action `foreach`. <p>Pour exécuter séquentiellement une boucle ForEach, donnez la valeur « Séquentiel » à la propriété `operationOptions` dans l’action `foreach`. | 
| Éléments SplitOn | 100 000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de débit

Les limites pour l’exécution d’une application logique sont les suivantes :

| NOM | Limite | Notes | 
| ----- | ----- | ----- | 
| Exécutions d’actions par tranche de 5 minutes | 100 000 | Pour augmenter la limite à 300 000, vous pouvez exécuter une application de logique en mode `High Throughput`. Pour configurer le mode de débit élevé, sous `runtimeConfiguration` de la ressource de flux de travail, définissez la propriété `operationOptions` sur `OptimizedForHighThroughput`. <p>**Remarque** : le mode Débit élevé est en préversion. Vous pouvez également répartir une charge de travail entre plusieurs applications si nécessaire. | 
| Appels sortants simultanés des actions | ~2,500 | Diminuer le nombre de requêtes simultanées ou réduire la durée en fonction des besoins. | 
| Point de terminaison du runtime : appels entrants simultanés | ~1,000 | Diminuer le nombre de requêtes simultanées ou réduire la durée en fonction des besoins. | 
| Point de terminaison du runtime : appels de lecture toutes les cinq minutes  | 60 000 | Vous pouvez répartir une charge de travail entre plusieurs applications si nécessaire. | 
| Point de terminaison du runtime : appels d’invocation toutes les cinq minutes| 45,000 | Vous pouvez répartir une charge de travail entre plusieurs applications si nécessaire. | 
|||| 

Pour dépasser ces limites dans le cadre d’un traitement normal ou exécuter des tests de charge susceptibles d’entraîner leur dépassement, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) afin d’obtenir de l’aide.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limites de requête HTTP

Les limites pour un appel de connecteur synchrone ou de requête HTTP unique sont les suivantes :

#### <a name="timeout"></a>Délai d'expiration

Comme certaines opérations de connecteur effectuent des appels asynchrones ou écoutent les demandes de webhook, le délai d’expiration pour ces opérations peut dépasser ces limites. Pour plus d’informations, consultez les détails techniques pour le connecteur spécifique et aussi [Actions et déclencheurs de workflow](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Requête sortante | 120 secondes | Pour les opérations en cours d’exécution plus longues, utilisez un [modèle d’interrogation asynchrone](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou une [boucle Until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Réponse synchrone | 120 secondes | Pour que la requête d’origine obtienne la réponse, toutes les étapes de la réponse doivent être terminées avant la limite, sauf si vous appelez une autre application logique en tant que workflow imbriqué. Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Taille des messages

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Taille des messages | 100 Mo | Pour contourner cette limite, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). Toutefois, certains connecteurs et API peuvent ne pas prendre en charge la segmentation ou même la limite par défaut. | 
| Taille des messages avec segmentation | 1 Go | Cette limite s’applique aux actions qui prennent en charge la segmentation en mode natif ou vous permettent d’activer la segmentation dans la configuration de leur runtime. Pour plus d’informations, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite d’évaluation des expressions | 131 072 caractères | Les expressions `@concat()`, `@base64()`, `@string()` ne peuvent pas contenir plus de caractères. | 
|||| 

#### <a name="retry-policy"></a>Stratégie de nouvelle tentative

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Nouvelles tentatives | 90 | Valeur par défaut : 4. Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai maximal avant nouvelle tentative | 1 jour | Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai minimal avant nouvelle tentative | 5 secondes | Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites des connecteurs personnalisés

Les limites pour les connecteurs personnalisés qu’il est possible de créer à partir d’API web sont les suivantes.

| NOM | Limite | 
| ---- | ----- | 
| Nombre de connecteurs personnalisés | 1 000 par abonnement Azure | 
| Nombre de demandes par minute pour chaque connexion créée par un connecteur personnalisé | 500 demandes par connexion |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites du compte d’intégration

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites du nombre d’artefacts par compte d’intégration

Les limites du nombre d’artefacts pour chaque compte d’intégration sont les suivantes. Pour plus d’informations, consultez [Tarifs Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Niveau Gratuit*

Utilisez le niveau gratuit uniquement pour les scénarios exploratoires, pas pour les scénarios de production. Ce niveau limite le débit et l’utilisation et n’inclut aucun contrat de niveau de service (SLA).

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Partenaires commerciaux EDI | 25 | | 
| Contrats commerciaux EDI | 10 | | 
| Cartes | 25 | | 
| Schémas | 25 | 
| Assemblys | 10 | | 
| Configurations par lots | 5. | 
| Certificats | 25 | | 
|||| 

*Niveau de base*

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Partenaires commerciaux EDI | 2 | | 
| Contrats commerciaux EDI | 1 | | 
| Cartes | 500 | | 
| Schémas | 500 | 
| Assemblys | 25 | | 
| Configurations par lots | 1 | | 
| Certificats | 2 | | 
|||| 

*Niveau standard*

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Partenaires commerciaux EDI | 500 | | 
| Contrats commerciaux EDI | 500 | | 
| Cartes | 500 | | 
| Schémas | 500 | 
| Assemblys | 50 | | 
| Configurations par lots | 5. |  
| Certificats | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacité des artefacts

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| Schéma | 8 Mo | Pour charger des fichiers supérieurs à 2 Mo, utilisez [l’URI d’objet blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mappage (fichier XSLT) | 2 Mo | | 
| Point de terminaison du runtime : appels de lecture toutes les cinq minutes | 60 000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| Point de terminaison du runtime : appels d’invocation toutes les cinq minutes | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| Point de terminaison du runtime : appels de suivi toutes les cinq minutes | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| Point de terminaison du runtime : appels simultanés de blocage | ~1,000 | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Taille des messages de protocole B2B (AS2, X12, EDIFACT)

Les limites qui s’appliquent aux protocoles B2B sont les suivantes :

| NOM | Limite | Notes | 
| ---- | ----- | ----- | 
| AS2 | 50 Mo | S’applique au décodage et à l’encodage. | 
| X 12 | 50 Mo | S’applique au décodage et à l’encodage. | 
| EDIFACT | 50 Mo | S’applique au décodage et à l’encodage. | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuration : adresses IP

### <a name="azure-logic-apps-service"></a>Service Azure Logic Apps

Toutes les applications logiques dans une région utilisent les mêmes plages d’adresses IP. Pour prendre en charge les appels que les applications logiques passent directement à [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)et à d’autres requêtes HTTP, effectuez vos configurations de pare-feu afin qu’elles incluent ces adresses IP entrantes et sortantes, en fonction de l’emplacement de vos applications logiques :

| Région Logic Apps | Adresse IP sortante |
|-------------------|-------------|
| Australie | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Est de l’Australie | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Sud du Brésil | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Centre du Canada | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Est du Canada | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| Inde centrale | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Centre des États-Unis | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Est de l'Asie | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Est des États-Unis | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Est des États-Unis 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Est du Japon | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Ouest du Japon | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Centre-Nord des États-Unis | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Europe du Nord | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| États-Unis - partie centrale méridionale | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Inde du Sud | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Asie du Sud-Est | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Centre-Ouest des États-Unis | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Europe de l'Ouest | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Inde occidentale | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| États-Unis de l’Ouest | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Ouest des États-Unis 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Sud du Royaume-Uni | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Ouest du Royaume-Uni | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Région Logic Apps | IP entrante |
|-------------------|------------|
| Est de l’Australie | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Sud-est de l’Australie | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Sud du Brésil | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Centre du Canada | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Est du Canada | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| Inde centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Centre des États-Unis | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Est de l'Asie | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Est des États-Unis | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Est des États-Unis 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Est du Japon | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Ouest du Japon | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Centre-Nord des États-Unis | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europe du Nord | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| États-Unis - partie centrale méridionale | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Inde du Sud | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Asie du Sud-Est | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Centre-Ouest des États-Unis | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Europe de l'Ouest | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Inde occidentale | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| États-Unis de l’Ouest | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Ouest des États-Unis 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Sud du Royaume-Uni | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Ouest du Royaume-Uni | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Connecteurs

Pour prendre en charge les appels passés par les [connecteurs](../connectors/apis-list.md), configurez vos configurations de pare-feu afin qu’elles incluent ces adresses IP sortantes, selon l’emplacement de vos applications logiques.

> [!IMPORTANT]
>
> Si vous avez des configurations existantes, mettez-les à jour **dès que possible avant le 1er septembre 2018** afin qu’elles incluent et fassent correspondre les adresses IP dans cette liste pour les régions où se trouvent vos applications logiques. 

| Région Logic Apps | Adresse IP sortante | 
|-------------------|-------------|  
| Est de l’Australie | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Sud-est de l’Australie | 13.77.50.240 - 13.77.50.255, 13.70.136.174, 40.127.80.34 | 
| Sud du Brésil | 191.233.203.192 - 191.233.203.207, 104.41.59.51, 191.232.38.129 | 
| Centre du Canada | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126, 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 | 
| Est du Canada | 40.69.106.240 - 40.69.106.255, 52.242.35.152, 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 | 
| Inde centrale | 104.211.81.192 - 104.211.81.207, 52.172.211.12, 104.211.98.164 | 
| Centre des États-Unis | 13.89.171.80 - 13.89.171.95, 52.173.245.164, 40.122.49.51 | 
| Est de l'Asie | 13.75.36.64 - 13.75.36.79, 52.175.23.169, 23.99.116.181 | 
| Est des États-Unis | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Est des États-Unis 2 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Est du Japon | 13.78.108.0 - 13.78.108.15, 13.71.153.19, 40.115.186.96 | 
| Ouest du Japon | 40.74.100.224 - 40.74.100.239, 104.215.61.248, 40.74.130.77 | 
| Centre-Nord des États-Unis | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Europe du Nord | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| États-Unis - partie centrale méridionale | 104.214.19.48 - 104.214.19.63, 13.65.86.57, 104.214.70.191 | 
| Inde du Sud | 40.78.194.240 - 40.78.194.255, 13.71.125.22, 104.211.227.225 | 
| Asie du Sud-Est | 13.67.8.240 - 13.67.8.255, 52.187.68.19, 13.76.231.68 | 
| Centre-Ouest des États-Unis | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 52.161.27.108, 52.161.30.5, 52.161.29.35, 52.161.26.212 | 
| Europe de l'Ouest | 13.69.64.208 - 13.69.64.223, 52.174.88.118, 40.115.50.13 | 
| Inde occidentale | 104.211.146.224 - 104.211.146.239, 104.211.189.218, 104.211.161.203 | 
| États-Unis de l’Ouest | 40.112.243.160 - 40.112.243.175, 104.42.122.49, 104.40.51.248 | 
| Ouest des États-Unis 2 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 13.66.225.219, 13.66.218.78, 13.66.220.135, 13.66.219.14 | 
| Sud du Royaume-Uni | 51.140.148.0 - 51.140.148.15, 51.140.80.51, 51.140.80.51 | 
| Ouest du Royaume-Uni | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>Étapes suivantes  

* Découvrez comment [créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* En savoir plus sur [les exemples et les scénarios courants](../logic-apps/logic-apps-examples-and-scenarios.md)
