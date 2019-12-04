---
title: Détection des menaces pour la couche des services Azure - Azure Security Center
description: Cette rubrique présente les alertes des couches de service Azure disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186502"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Détection des menaces pour la couche des services Azure dans Azure Security Center

Cette rubrique présente les alertes Azure Security Center disponibles lors de la supervision des couches de service Azure suivantes :

* [Couche réseau Azure](#network-layer)
* [Couche Gestion Azure (Azure Resource Manager) (préversion)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

>[!NOTE]
>Les analyses suivantes s’appliquent à tous les types de ressources. Elles utilisent la télémétrie fournie par Security Center en exploitant les flux internes Azure.

## Couche Réseau Azure<a name="network-layer"></a>

L’analytique de la couche réseau de Security Center est basée sur des exemples de [données IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), qui sont des en-têtes de paquets collectés par des routeurs de base Azure. En fonction de ce flux de données, les modèles Machine Learning de Security Center identifient et signalent les activités de trafic malveillantes. Pour enrichir les adresses IP, Security Center utilise la base de données Microsoft Threat Intelligence.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Activité réseau sortante RDP suspecte**|L’analyse d’un échantillon du trafic réseau a détecté une communication RDP (Remote Desktop Protocol) sortante anormale, à partir d’une ressource dans votre déploiement. Cette activité est considérée comme anormale pour cet environnement. Cela peut indiquer que votre ressource a été compromise et qu’elle est désormais utilisée pour forcer l’attaque d’un point de terminaison RDP externe. Ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|
|**Activité réseau sortante RDP suspecte vers plusieurs destinations**|L’analyse d’un échantillon du trafic réseau a détecté une communication RDP sortante anormale, depuis une ressource dans votre déploiement vers plusieurs destinations. Cette activité est considérée comme anormale pour cet environnement. Cela peut indiquer que votre ressource a été compromise et qu’elle est désormais utilisée pour forcer l’attaque de points de terminaison RDP externes. Ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|
|**Activité réseau sortante SSH suspecte**|L’analyse d’un échantillon du trafic réseau a détecté une communication SSH (Secure Shell) sortante anormale, à partir d’une ressource dans votre déploiement. Cette activité est considérée comme anormale pour cet environnement. Cela peut indiquer que votre ressource a été compromise et qu’elle est désormais utilisée pour forcer l’attaque d’un point de terminaison SSH externe. Ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|
|**Activité réseau sortante SHH suspecte vers plusieurs destinations**|L’analyse d’un échantillon du trafic réseau a détecté une communication SSH sortante anormale, depuis une ressource dans votre déploiement vers plusieurs destinations. Cette activité est considérée comme anormale pour cet environnement. Cela peut indiquer que votre ressource a été compromise et qu’elle est désormais utilisée pour forcer l’attaque de points de terminaison SSH externes. Ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|
|**Activité réseau entrante SSH suspecte à partir de plusieurs sources**|L’analyse d’un échantillon du trafic réseau a détecté des communications SSH entrantes anormales depuis plusieurs sources vers une ressource dans votre déploiement. La connexion de diverses adresses IP uniques à votre ressource est considérée comme anormale pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre interface SSH à partir de plusieurs hôtes (Botnet).|
|**Activité réseau entrante SSH suspecte**|L’analyse d’un échantillon du trafic réseau a détecté une communication SSH entrante anormale vers une ressource dans votre déploiement. Un nombre relativement élevé de connexions entrantes à votre ressource est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre interface SSH.
|**Activité réseau entrante RDP suspecte à partir de plusieurs sources**|L’analyse d’un échantillon du trafic réseau a détecté des communications RDP entrantes anormales depuis plusieurs sources vers une ressource dans votre déploiement. La connexion de diverses adresses IP uniques à votre ressource est considérée comme anormale pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre interface RDP à partir de plusieurs hôtes (Botnet).|
|**Activité réseau entrante RDP suspecte**|L’analyse d’un échantillon du trafic réseau a détecté une communication RDP entrante anormale vers une ressource dans votre déploiement. Un nombre relativement élevé de connexions entrantes à votre ressource est considéré comme anormal pour cet environnement. Cette activité peut indiquer une tentative d’attaque par force brute sur votre interface SSH.|
|**Communication réseau avec une adresse malveillante détectée**|L’analyse d’un échantillon du trafic réseau a détecté une communication provenant d’une ressource dans votre déploiement avec un possible serveur C&C (commande et contrôle). Ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|

Pour comprendre comment Security Center peut utiliser les signaux liés au réseau pour appliquer la protection contre les menaces, consultez [Détections DNS heuristiques dans Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Les alertes de détection des menaces de la couche réseau Azure, dans Azure Security Center, sont uniquement générées sur les machines virtuelles auxquelles la même adresse IP a été attribuée pendant toute l’heure au cours de laquelle une communication suspecte a eu lieu. Cela s’applique aux machines virtuelles, ainsi qu’aux machines virtuelles créées dans l’abonnement du client dans le cadre d’un service géré (par exemple, AKS, Databricks).

## Couche de gestion Azure (Azure Resource Manager) (préversion)<a name ="management-layer"></a>

>[!NOTE]
>La couche de protection de Security Center basée sur Azure Resource Manager est disponible en préversion.

Security Center offre une couche supplémentaire de protection en utilisant les événements Azure Resource Manager, considérée comme le plan de contrôle pour Azure. En analysant les enregistrements Azure Resource Manager, Security Center détecte les opérations inhabituelles ou potentiellement dangereuses dans l’environnement d’un abonnement Azure.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Exécution du Kit de ressources MicroBurst**|L’exécution d’un Kit de ressources de reconnaissance d’environnement cloud connu a été détectée dans votre environnement. L’outil [MicroBurst](https://github.com/NetSPI/MicroBurst) peut être utilisé par un attaquant (ou testeur d’intrusion) pour mapper les ressources de vos abonnements, identifier les configurations non sécurisées et soustraire des informations confidentielles.|
|**Exécution du Kit de ressources Azurite**|L’exécution d’un Kit de ressources de reconnaissance d’environnement cloud connu a été détectée dans votre environnement. L’outil [Azurite](https://github.com/mwrlabs/Azurite) peut être utilisé par un attaquant (ou testeur d’intrusion) pour mapper les ressources de vos abonnements et identifier les configurations non sécurisées.|
|**Session de gestion suspecte utilisant un compte inactif**|L’analyse des journaux d’activité liés aux abonnements a détecté un comportement suspect. Un principal non utilisé pendant une longue période effectue maintenant des actions qui peuvent sécuriser la persistance d’un attaquant.|
|**Session de gestion suspecte utilisant PowerShell**|L’analyse des journaux d’activité liés aux abonnements a détecté un comportement suspect. Un principal qui n’utilise pas régulièrement PowerShell pour gérer l’environnement des abonnements utilise maintenant PowerShell, et effectue des actions qui peuvent sécuriser la persistance d’un attaquant.|
|**Utilisation de techniques de persistance Azure avancées**|L’analyse des journaux d’activité liés aux abonnements a détecté un comportement suspect. Des rôles personnalisés ont reçu des entités d’identité légitimées. Cela pourrait permettre à l’attaquant de gagner l’environnement d’un client Azure de façon durable.|
|**Activité à partir de pays peu fréquents**|Une activité s’est produite à partir d’un emplacement qui n’a pas été récemment ou qui n’a jamais été visité par un utilisateur de l’organisation.<br/>Cette détection prend en compte les emplacements d’activité précédents pour déterminer les emplacements nouveaux et peu fréquents. Le moteur de détection d’anomalies stocke des informations sur les emplacements précédents utilisés par les utilisateurs de l’organisation. 
|**Activité depuis des adresses IP anonymes**|Une activité utilisateur depuis une adresse IP qui a été identifiée comme adresse IP de proxy anonyme a été détectée. <br/>Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant. Cette détection utilise un algorithme Machine Learning qui réduit les faux positifs, tels que les adresses IP mal étiquetées qui sont largement utilisées par d’autres utilisateurs de l’organisation.|
|**Voyage impossible détecté**|Deux activités utilisateur (dans une ou plusieurs sessions) se sont produites, provenant d’emplacements géographiquement distants. Cela se produit dans une période plus courte que celle qu’il aurait fallu à l’utilisateur pour se déplacer du premier emplacement vers le second. Cela indique qu’un autre utilisateur utilise les mêmes informations d’identification. <br/>Cette détection utilise un algorithme Machine Learning qui ignore les faux positifs évidents contribuant aux conditions de voyage impossibles, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. La détection présente une période d’apprentissage initiale de 7 jours, lui servant à assimiler le modèle d’activité d’un nouvel utilisateur.|

>[!NOTE]
> Plusieurs des analyses précédentes sont alimentées par Microsoft Cloud App Security. Pour tirer parti de ces analyses, vous devez activer une licence Cloud App Security. Si vous avez une licence Cloud App Security, ces alertes sont activées par défaut. Pour les désactiver :
>
> 1. Dans le panneau **Security Center**, sélectionnez **Stratégie de sécurité**. Pour l’abonnement que vous souhaitez modifier, sélectionnez **Modifier les paramètres**.
> 2. Sélectionnez **Détection des menaces**.
> 3. Sous **Activer les intégrations**, décochez la case **Autoriser Microsoft Cloud App Security à accéder à mes données** et sélectionnez **Enregistrer**.

>[!NOTE]
>Security Center stocke les données de client liées à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Security Center dans la zone géographique de la ressource, il stocke les données aux États-Unis. Quand Cloud App Security est activé, ces informations sont stockées selon les règles d’emplacement géographique de Cloud App Security. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault <a name="azure-keyvault"></a>

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

Azure Security Center inclut la protection native avancée Azure contre les menaces pour Azure Key Vault qui fournit une couche supplémentaire de renseignements de sécurité. Security Center détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes tiers de supervision de la sécurité.  

Quand des activités anormales se produisent, Security Center affiche des alertes et les envoie éventuellement par e-mail aux administrateurs d’abonnement. Ces alertes fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

> [!NOTE]
> Ce service n’est pas disponible actuellement dans les régions Azure Government et de cloud souverain.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Accès à partir d’un nœud de sortie TOR dans un coffre de clés Key Vault**|L’accès au coffre de clés Key Vault a été effecté par une personne qui utilise le système d’anonymisation IP pour masquer son emplacement. Les utilisateurs malveillants essaient souvent de masquer leur emplacement lorsqu’ils tentent d’accéder de façon non autorisée aux ressources connectées à Internet.|
|**Modification de stratégie suspecte et requête secrète dans un coffre de clés Key Vault**|Des opérations destinés à répertorier et/ou obtenir des clés secrètes ont eu lieu après une modification apportée à la stratégie Key Vault. En outre, ce modèle d’opération n’est généralement pas exécuté par l’utilisateur sur ce coffre. Cela indique clairement que le coffre de clés Azure Key Vault est compromis et que les clés secrètes ont été volées par un acteur malveillant.|
|**Liste des clés et requête suspectes auprès d’un coffre de clés Key Vault**|Une opération de liste secrète a été suivie de nombreuses opérations d’extraction de secret. En outre, ce modèle d’opération n’est généralement pas exécuté par l’utilisateur sur ce coffre. Cela indique qu’un utilisateur peut vider les secrets stockés dans le coffre de clés Key Vault à des fins malveillantes.|
|**Une paire utilisateur-application inhabituelle a accédé à un coffre de clés Key Vault**|Un jumelage utilisateur-application a accédé au coffre de clés Key Vault alors qu’il n’y accède pas normalement. Il peut s’agir d’une tentative d’accès légitime (par exemple, après une mise à jour d’infrastructure ou de code). Cela peut également indiquer que votre infrastructure est compromise et qu’un acteur malveillant tente d’accéder à votre coffre de clés Key Vault.|
|**Une application inhabituelle a accédé à un coffre de clés Key Vault**|Une application a accédé au coffre de clés Key Vault alors qu’elle n’y accède pas normalement. Il peut s’agir d’une tentative d’accès légitime (par exemple, après une mise à jour d’infrastructure ou de code). Cela peut également indiquer que votre infrastructure est compromise et qu’un acteur malveillant tente d’accéder à votre coffre de clés Key Vault.|
|**Un utilisateur inhabituel a accédé à un coffre de clés Key Vault**|Un utilisateur a accédé au coffre de clés Key Vault alors qu’il n’y accède pas normalement. Il peut s’agir d’une tentative d’accès légitime (par exemple, un nouvel utilisateur ayant besoin d’un accès a rejoint l’organisation). Cela peut également indiquer que votre infrastructure est compromise et qu’un acteur malveillant tente d’accéder à votre coffre de clés Key Vault.|
|**Modèle d’opération inhabituelle dans un coffre de clés Key Vault**|Un ensemble inhabituel d’opérations Key Vault a été effectué par rapport aux données d’historique. L’activité Key Vault n’évolue généralement pas au fil du temps. Il peut s’agir d’une modification légitime de l’activité. Votre infrastructure peut également être compromise, ce qui nécessite des investigations supplémentaires.|
|**Volume élevé d’opérations dans un coffre de clés Key Vault**|Une plus grande quantité d’opérations Key Vault a été effectuée par rapport aux données d’historique. L’activité Key Vault n’évolue généralement pas au fil du temps. Il peut s’agir d’une modification légitime de l’activité. Votre infrastructure peut également être compromise, ce qui nécessite des investigations supplémentaires.|
|**L’utilisateur a accédé à un grand nombre de coffres de clés**|Le nombre de coffres auxquels accède un utilisateur ou une application a changé par rapport aux données d’historique. L’activité Key Vault n’évolue généralement pas au fil du temps. Il peut s’agir d’une modification légitime de l’activité. Votre infrastructure peut également être compromise, ce qui nécessite des investigations supplémentaires.|