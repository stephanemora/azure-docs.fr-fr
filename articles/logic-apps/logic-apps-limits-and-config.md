---
title: Limites et configuration - Azure Logic Apps | Microsoft Docs
description: Limites de service et valeurs de configuration pour Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 4a2b06f18e709f9a8479770d58f43928893281be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117351"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limites et informations de configuration pour Azure Logic Apps

Cet article décrit les limites et les détails de configuration liés à la création et à l’exécution de workflows automatisés avec Azure Logic Apps. Pour Microsoft Flow, consultez [Limites et configuration dans Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limites de définition

Les limites pour la définition d’une application logique sont les suivantes :

| Nom | Limite | Notes | 
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

| Nom | Limite | Notes | 
|------|-------|-------| 
| Durée d’exécution | 90 jours | Pour changer cette limite, consultez [Modifier la durée d’exécution et la rétention de stockage](#change-duration). | 
| Rétention de stockage | 90 jours à compter de l’heure de début de l’exécution | Pour modifier cette limite par une valeur comprise entre 7 jours et 90 jours, consultez [modifier la rétention de stockage](#change-retention). | 
| Intervalle de périodicité minimal | 1 seconde | | 
| Intervalle de périodicité maximal | 500 jours | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Modifier la durée d’exécution et la rétention de stockage

Pour modifier la limite par défaut par une valeur comprise entre 7 jours et 90 jours, procédez comme suit. Pour pouvoir dépasser la limite maximale, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) pour obtenir de l’aide pour vos exigences.

1. Dans le portail Azure, dans le menu de votre application logique, choisissez **Workflow settings** (Paramètres de workflow). 

2. Sous **Options de runtime**, dans la liste **Conservation de l’historique des exécutions en jours**, choisissez **Custom** (Personnalisée). 

3. Entrez le nombre de jours souhaité ou faites glisser le curseur pour sélectionner ce nombre.

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Désactivation ou suppression des applications logiques

Lorsque vous désactivez une application logique, aucune nouvelle exécution n’est instanciée. Toutes les exécutions en cours et en attente continuent jusqu’à ce qu’elles soient terminées, ce qui peut prendre du temps.

Lorsque vous supprimez une application logique, aucune nouvelle exécution n’est instanciée. Toutes les exécutions en cours et en attente sont annulées. Si vous avez des milliers d’exécutions, l’annulation peut prendre beaucoup de temps.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limites de simultanéité, de bouclage et de décomposition

Les limites pour l’exécution d’une application logique sont les suivantes :

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| Déclencheur simultané | * Illimité lorsque le contrôle d’accès concurrentiel est désactivé <p><p>* 25 est la limite par défaut lorsque le contrôle d’accès concurrentiel est activé, ce qui ne peut pas être annulé une fois le contrôle activé. Vous pouvez modifier la valeur par défaut en la remplaçant par une valeur comprise entre 1 et 50 (inclus). | Cette limite décrit le nombre maximal d’instances d’application logique pouvant être exécutée simultanément ou en parallèle. <p><p>Pour modifier la limite par défaut pour une valeur comprise entre 1 et 50, consultez [Modifier la limite du déclencheur simultané](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou [Déclencher des instances séquentiellement](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Exécutions en attente maximale | Lorsque le contrôle d’accès concurrentiel est activé, le nombre minimal d’exécutions en attente est égal à 10 plus le nombre d’exécutions simultanées (concurrence du déclencheur). Vous pouvez modifier le nombre maximal jusqu’à la valeur maximale 100 (inclus). | Cette limite décrit le nombre maximal d’instances d’application logique pouvant attendre de s’exécuter quand votre application logique exécute déjà le nombre maximal d’instances simultanées. <p><p>Pour changer la limite par défaut, consultez [Changer la limite d’exécutions en attente](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Éléments du tableau Foreach | 100 000 | Cette limite décrit le nombre maximal d’éléments de tableau qu’une boucle « for each » peut traiter. <p><p>Pour filtrer des tables plus grandes, vous pouvez utiliser l’[action de requête](../connectors/connectors-native-query.md). | 
| Accès concurrentiel Foreach | La limite par défaut est 20 lorsque le contrôle d’accès concurrentiel est désactivé. Vous pouvez modifier la valeur par défaut en la remplaçant par une valeur comprise entre 1 et 50 (inclus). | Cette limite indique le nombre maximal d’itérations de boucles « for each » qui peuvent s’exécuter simultanément ou en parallèle. <p><p>Pour changer la limite par défaut pour une valeur comprise entre 1 et 50, consultez [Changer la limite de simultanéité « for each »](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou [Exécuter des boucles « for each » séquentiellement](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| Éléments SplitOn | 100 000 | Pour les déclencheurs qui retournent un tableau, vous pouvez spécifier une expression utilisant une propriété « SplitOn » qui [fractionne ou dégroupe des éléments de tableau en plusieurs instances de workflows](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) à des fins de traitement, au lieu d’utiliser une boucle « for each ». Cette expression fait référence au tableau à utiliser pour la création et l’exécution d’une instance de workflow pour chaque élément du tableau. |
| Itérations Until | 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de débit

Les limites pour l’exécution d’une application logique sont les suivantes :

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| Action : Exécutions par tranche de 5 minutes | 300 000 | La limite par défaut est définie sur 100 000. Pour modifier la limite par défaut, consultez [exécuter votre application logique en mode de « débit élevé »](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), disponible dans la préversion. Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| Action : Appels sortants simultanés | ~2,500 | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. | 
| Point de terminaison du runtime : appels entrants simultanés | ~1,000 | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. | 
| Point de terminaison du runtime : appels de lecture toutes les cinq minutes  | 60 000 | Vous pouvez répartir une charge de travail entre plusieurs applications si nécessaire. | 
| Point de terminaison du runtime : appels d’invocation toutes les cinq minutes | 45,000 | Vous pouvez répartir une charge de travail entre plusieurs applications si nécessaire. | 
| Débit de contenu par tranche de 5 minutes | 600 Mo | Vous pouvez répartir une charge de travail entre plusieurs applications si nécessaire. | 
|||| 

Pour dépasser ces limites dans le cadre d’un traitement normal ou exécuter des tests de charge susceptibles d’entraîner leur dépassement, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) afin d’obtenir de l’aide.

<a name="sftp"></a>

## <a name="ftp-sftp-and-sftp-ssh-limits"></a>Limites FTP, SFTP et SFTP-SSH

### <a name="file-size"></a>Taille du fichier

| Nom | Limite | Notes |
|------|-------|-------|
| FTP | 50 Mo | Pour dépasser cette limite pour les actions uniquement, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). <p>**Remarque**: La segmentation ne s’applique pas aux déclencheurs. En outre, certains connecteurs et API peuvent ne pas prendre en charge la segmentation ou même la limite par défaut. | 
| SFTP | 50 Mo | Pour dépasser cette limite pour les actions uniquement, utilisez le [connecteur SFTP-SSH](../connectors/connectors-sftp-ssh.md) ou consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). <p>**Remarque**: La segmentation ne s’applique pas aux déclencheurs. En outre, certains connecteurs et API peuvent ne pas prendre en charge la segmentation ou même la limite par défaut. | 
| SFTP-SSH | 1 Go | Ce connecteur peut gérer jusqu’à 1 Go de données, qu’il transfère par portions de 50 Mo. Pour dépasser cette limite pour les actions uniquement, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). <p>**Remarque**: La segmentation ne s’applique pas aux déclencheurs. | 
|||| 

<a name="request-limits"></a>

## <a name="http-limits"></a>Limites HTTP

Les limites pour un appel de connecteur synchrone ou de requête HTTP unique sont les suivantes :

#### <a name="timeout"></a>Délai d'expiration

Comme certaines opérations de connecteur effectuent des appels asynchrones ou écoutent les demandes de webhook, le délai d’expiration pour ces opérations peut dépasser ces limites. Pour plus d’informations, consultez les détails techniques pour le connecteur spécifique et aussi [Actions et déclencheurs de workflow](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| Requête sortante | 120 secondes | Pour les opérations en cours d’exécution plus longues, utilisez un [modèle d’interrogation asynchrone](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou une [boucle Until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Réponse synchrone | 120 secondes | Pour que la requête d’origine obtienne la réponse, toutes les étapes de la réponse doivent être terminées avant la limite, sauf si vous appelez une autre application logique en tant que workflow imbriqué. Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Taille des messages

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| Taille des messages | 100 Mo | Pour contourner cette limite, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). Toutefois, certains connecteurs et API peuvent ne pas prendre en charge la segmentation ou même la limite par défaut. | 
| Taille des messages avec segmentation | 1 Go | Cette limite s’applique aux actions qui prennent en charge la segmentation en mode natif ou vous permettent d’activer la segmentation dans la configuration de leur runtime. Pour plus d’informations, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite d’évaluation des expressions | 131 072 caractères | Les expressions `@concat()`, `@base64()`, `@string()` ne peuvent pas contenir plus de caractères. | 
|||| 

#### <a name="retry-policy"></a>Stratégie de nouvelle tentative

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| Nouvelles tentatives | 90 | Valeur par défaut : 4. Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai maximal avant nouvelle tentative | 1 jour | Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Délai minimal avant nouvelle tentative | 5 secondes | Pour modifier la valeur par défaut, utilisez le [paramètre de stratégie de nouvelles tentatives](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites des connecteurs personnalisés

Les limites pour les connecteurs personnalisés qu’il est possible de créer à partir d’API web sont les suivantes.

| Nom | Limite | 
| ---- | ----- | 
| Nombre de connecteurs personnalisés | 1 000 par abonnement Azure | 
| Nombre de demandes par minute pour chaque connexion créée par un connecteur personnalisé | 500 demandes par connexion |
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identités managées

| Nom | Limite | 
| ---- | ----- | 
| Nombre d’applications logiques avec des identités managées assignées par le système par abonnement Azure | 10 | 
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites du compte d’intégration

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites du nombre d’artefacts par compte d’intégration

Les limites du nombre d’artefacts pour chaque compte d’intégration sont les suivantes. Pour plus d’informations, consultez [Tarifs Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Niveau Gratuit*

Utilisez le niveau gratuit uniquement pour les scénarios exploratoires, pas pour les scénarios de production. Ce niveau limite le débit et l’utilisation et n’inclut aucun contrat de niveau de service (SLA).

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Assemblys | 10 | | 
| Configurations par lots | 5. | 
| Certificats | 25 | | 
| Contrats commerciaux EDI | 10 | | 
| Partenaires commerciaux EDI | 25 | | 
| Cartes | 25 | | 
| Schémas | 25 | 
|||| 

*Niveau de base*

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Assemblys | 25 | | 
| Configurations par lots | 1 | | 
| Certificats | 2 | | 
| Contrats commerciaux EDI | 1 | | 
| Partenaires commerciaux EDI | 2 | | 
| Cartes | 500 | | 
| Schémas | 500 | 
|||| 

*Niveau standard*

| Artefact | Limite | Notes | 
|----------|-------|-------| 
| Assemblys | 50 | | 
| Configurations par lots | 5. |  
| Certificats | 50 | | 
| Contrats commerciaux EDI | 500 | | 
| Partenaires commerciaux EDI | 500 | | 
| Cartes | 500 | | 
| Schémas | 500 | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacité des artefacts

| Artefact | Limite | Notes | 
| -------- | ----- | ----- | 
| Assembly | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez un [compte de stockage Azure et un conteneur d’objets blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mappage (fichier XSLT) | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez l’[API REST Azure Logic Apps – Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). | 
| Schéma | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez un [compte de stockage Azure et un conteneur d’objets blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
||||

| Point de terminaison du runtime | Limite | Notes |
|------------------|-------|-------|
| appels de lecture toutes les cinq minutes | 60 000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| appels d’invocation toutes les cinq minutes | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| appels de suivi toutes les cinq minutes | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. | 
| appels simultanés de blocage | ~1,000 | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Taille des messages de protocole B2B (AS2, X12, EDIFACT)

Les limites qui s’appliquent aux protocoles B2B sont les suivantes :

| Nom | Limite | Notes | 
| ---- | ----- | ----- | 
| AS2 | 50 Mo | S’applique au décodage et à l’encodage. | 
| X 12 | 50 Mo | S’applique au décodage et à l’encodage. | 
| EDIFACT | 50 Mo | S’applique au décodage et à l’encodage. | 
|||| 

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Configuration du pare-feu : Adresses IP

Toutes les applications logiques qui se trouvent dans une même région utilisent les mêmes plages d’adresses IP. Pour prendre en charge les appels que les applications logiques passent directement à [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) et à d’autres requêtes HTTP, configurez vos pare-feu avec *toutes* les adresses IP [entrantes](#inbound) *et* [sortantes](#outbound) qui sont utilisées par le service Logic Apps, en fonction de l’emplacement de vos applications logiques. Ces adresses s’affichent sous les en-têtes **Entrantes** et **Sortantes** dans cette section, et sont triées par région.

Pour prendre en charge les appels que passent les [connecteurs managés par Microsoft](../connectors/apis-list.md), configurez votre pare-feu avec *toutes* les adresses IP [sortantes](#outbound) utilisées par ces connecteurs, en fonction de l’emplacement de vos applications logiques. Ces adresses s’affichent sous l’en-tête **Sortantes** dans cette section, et sont triées par région. 

Pour [Azure Government](../azure-government/documentation-government-overview.md) et [Azure China 21Vianet](/azure/china/china-welcome), les adresses IP réservées pour les connecteurs ne sont pas disponibles.

> [!IMPORTANT]
> 
> Si vous avez des configurations existantes, mettez-les à jour **dès que possible avant le 1er septembre 2018** afin qu’elles incluent et correspondent aux adresses IP de ces listes, pour les régions où se trouvent vos applications logiques. 

Logic Apps ne prend pas en charge la connexion directe à des comptes de stockage Azure à travers des pare-feu. Pour accéder à ces comptes de stockage, utilisez une des options suivantes : 

* Créez un [environnement de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), qui peut se connecter à des ressources dans un réseau virtuel Azure. 

* Si vous utilisez déjà Gestion des API, vous pouvez utiliser ce service pour ce scénario. Pour plus d’informations, consultez [Architecture d’intégration d’entreprise simple](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Adresses IP entrantes - Service Logic Apps uniquement

| Région | IP |
|--------|----|
| Australie Est | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Australie Sud-Est | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brésil Sud | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Centre du Canada | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Est du Canada | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Inde Centre | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA Centre | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Asie Est | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| USA Est | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| USA Est 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japon Est | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japon Ouest | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| USA Centre Nord | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europe Nord | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| USA Centre Sud | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Inde Sud | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asie Sud-Est | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| USA Centre-Ouest | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europe Ouest | 13.95.155.53, 52.174.49.6, 52.174.49.6, 52.174.54.218 |
| Inde Ouest | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| USA Ouest | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| USA Ouest 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Sud du Royaume-Uni | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Ouest du Royaume-Uni | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Adresses IP sortantes - Service Logic Apps et connecteurs managés

| Région | Adresse IP Logic Apps | Adresse IP des connecteurs managés | 
|--------|---------------|-----------------------|
| Australie Est | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Australie Sud-Est | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Brésil Sud | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Centre du Canada | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Est du Canada | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| Inde Centre | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| USA Centre | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Asie Est | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| USA Est | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| USA Est 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Japon Est | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Japon Ouest | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| USA Centre Nord | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Europe Nord | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| USA Centre Sud | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| Inde Sud | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Asie Sud-Est | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| USA Centre-Ouest | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Europe Ouest | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| Inde Ouest | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| USA Ouest | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| USA Ouest 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Sud du Royaume-Uni | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Ouest du Royaume-Uni | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
|||| 

## <a name="next-steps"></a>Étapes suivantes  

* Découvrez comment [créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* En savoir plus sur [les exemples et les scénarios courants](../logic-apps/logic-apps-examples-and-scenarios.md)
