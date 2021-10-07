---
title: Connexions réseau dans l’Explorateur Stockage Azure | Microsoft Docs
description: Documentation sur la connexion à votre réseau dans l’Explorateur Stockage Azure
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: 2731f4473b726e7433167fe15c9e1f29c775d9cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627878"
---
# <a name="network-connections-in-storage-explorer"></a>Connexions réseau dans l’Explorateur Stockage

Lorsqu’il n’est pas connecté à un émulateur local, l’Explorateur Stockage utilise votre réseau pour envoyer des requêtes à vos ressources stockage, ainsi qu’à d’autres services Azure et Microsoft.

## <a name="hostnames-accessed-by-storage-explorer"></a>Noms d’hôtes auxquels accède l’Explorateur Stockage

Explorateur Stockage envoie des requêtes à différents points de terminaison en cours d’utilisation. La liste suivante détaille les noms d’hôte courants auxquels Explorateur Stockage envoie des requêtes :

- Points de terminaison ARM :
  - `management.azure.com` (Azure global)
  - `management.chinacloudapi.cn` (Azure Chine)
  - `management.microsoftazure.de` (Azure Allemagne)
  - `management.usgovcloudapi.net` (Azure Gouvernement américain)
- Points de terminaison de connexion :
  - `login.microsoftonline.com` (Azure global)
  - `login.chinacloudapi.cn` (Azure Chine)
  - `login.microsoftonline.de` (Azure Allemagne)
  - `login.microsoftonline.us` (Azure Gouvernement américain)
- Points de terminaison de Graph :
  - `graph.windows.net` (Azure global)
  - `graph.chinacloudapi.cn` (Azure Chine)
  - `graph.cloudapi.de` (Azure Allemagne)
  - `graph.windows.net` (Azure Gouvernement américain)
- Points de terminaison stockage Azure :
  - `(blob|file|queue|table|dfs).core.windows.net` (Azure global)
  - `(blob|file|queue|table|dfs).core.chinacloudapi.cn` (Azure Chine)
  - `(blob|file|queue|table|dfs).core.cloudapi.de` (Azure Allemagne)
  - `(blob|file|queue|table|dfs).core.usgovcloudapi.net` (Azure Gouvernement américain)
- Mise à jour de l’Explorateur Stockage : `storageexplorerpublish.blob.core.windows.net`
- Transfert de liens Microsoft :
  - `aka.ms`
  - `go.microsoft.com`
- Tous les domaines personnalisés, liens privés ou points de terminaison spécifiques aux instances Azure Stack derrière lesquels se trouvent vos ressources
- Noms d’hôtes d’émulateur distant

## <a name="proxy-sources"></a>Sources de proxy

Explorateur Stockage dispose de différentes options d’endroits et de méthodes pour se procurer les informations nécessaires pour se connecter à votre proxy. Pour modifier l’option utilisée, accédez à **Paramètres** (icône représentant un engrenage dans la barre d’outils verticale de gauche) > **Application** > **Proxy**. Une fois que dans la section de proxy des paramètres, vous pouvez sélectionner la méthode et l’endroit à utiliser par Explorateur Stockage pour se procurer vos paramètres de proxy :
- Ne pas utiliser le proxy
- Utiliser des variables d’environnement
- Utiliser des paramètres de proxy de l’application
- Utiliser le proxy système (préversion)

### <a name="do-not-use-proxy"></a>Ne pas utiliser le proxy

Lorsque cette option est sélectionnée, Explorateur Stockage n’effectue aucune tentative de connexion à un proxy. Ne pas utiliser le proxy est l’option par défaut.

### <a name="use-environment-variables"></a>Utiliser des variables d’environnement

Lorsque cette option est sélectionnée, Explorateur Stockage recherche les informations de proxy à partir de variables d’environnement spécifiques. Ces variables sont les suivantes :
- `HTTP_PROXY`
- `HTTPS_PROXY`

Si les deux variables sont définies, Explorateur Stockage se procure les informations de proxy à partir de `HTTPS_PROXY`.

La valeur de ces variables d’environnement doit être une URL au format suivant :

`(http|https)://(username:password@)<hostname>:<port>`

Seul le protocole (`http|https`) et le nom d’hôte sont obligatoires. Si vous avez un nom d’utilisateur, vous n’avez pas besoin de fournir un mot de passe.

### <a name="use-app-proxy-settings"></a>Utiliser des paramètres de proxy de l’application

Lorsque cette option est sélectionnée, Explorateur Stockage utilise les paramètres du proxy de l’application. Il s’agit notamment des paramètres suivants :
- Protocol
- Nom d’hôte
- Port
- Utiliser/Ne pas utiliser les informations d’identification
- Informations d'identification

Tous les paramètres autres que les informations d’identification peuvent être gérés à partir des endroits suivants :
- **Paramètres** (icône représentant un engrenage dans la barre d’outils verticale de gauche) > **Application** > **Proxy** > **Utiliser les informations d’identification**.
- La boîte de dialogue Paramètres du proxy (**Édition** > **Configurer le proxy**).

Pour définir les informations d’identification, vous devez accéder à la boîte de dialogue Paramètres du proxy (**Édition** > **Configurer le proxy**).

### <a name="use-system-proxy-preview"></a>Utiliser le proxy système (préversion)

Lorsque cette option est sélectionnée, Explorateur Stockage utilise les paramètres du proxy de votre système d’exploitation. Plus précisément, des appels réseau sont effectués à l’aide de la pile de mise en réseau Chromium. La pile de mise en réseau Chromium est beaucoup plus robuste que celle de NodeJS normalement utilisée par Explorateur Stockage. Voici un extrait de la [documentation de Chromium](https://www.chromium.org/developers/design-documents/network-settings) sur tout ce qu’elle peut faire :

> La pile réseau Chromium utilise les paramètres réseau du système pour permettre aux utilisateurs et aux administrateurs de contrôler facilement les paramètres réseau de toutes les applications. Les paramètres réseau comprennent les suivants :
> - paramètres du proxy
> - paramètres SSL/TLS
> - paramètres de vérification de la révocation de certificat
> - magasins de certificats et de clés privées

Si votre serveur proxy nécessite des informations d’identification et qu’elles ne sont pas configurées dans les paramètres de votre système d’exploitation, vous devez activer l’utilisation de vos informations d’identification et les définir dans Explorateur Stockage. Vous pouvez activer ou désactiver l’utilisation des informations d’identification à partir des endroits suivants :
- **Paramètres** (icône représentant un engrenage dans la barre d’outils verticale de gauche) > **Application** > **Proxy** > **Utiliser les informations d’identification**.
- La boîte de dialogue Paramètres du proxy (**Édition** > **Configurer le proxy**).

Pour définir les informations d’identification, vous devez accéder à la boîte de dialogue Paramètres du proxy (**Édition** > **Configurer le proxy**).

Cette option est en version préliminaire, car toutes les fonctionnalités ne prennent actuellement pas en charge le proxy système. Voir les [fonctionnalités qui prennent en charge le proxy système](#features-that-support-system-proxy) pour en obtenir une liste complète. Lorsque le proxy système est activé, les fonctionnalités qui ne le prennent pas en charge n’effectuent aucune tentative de connexion à un proxy.

Si vous rencontrez un problème lors de l’utilisation d’un proxy système avec une fonctionnalité prise en charge, [ouvrez un ticket sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

## <a name="proxy-server-authentication"></a>Authentification du serveur proxy

Si vous avez configuré Explorateur Stockage de sorte qu’il se procure les paramètres de proxy à partir de **variables d’environnement** ou de **paramètres de proxy d’application**, seuls les serveurs proxy utilisant l’authentification de base sont prise en charge.

Si vous avez configuré Explorateur Stockage de sorte qu’il utilise le **proxy système**, les serveurs proxy utilisant l’une des méthodes d’authentification suivantes sont pris en charge :
- De base
- Digest
- NTLM
- Negotiate

## <a name="which-proxy-source-should-i-choose"></a>Quelle source de proxy choisir ?

Si vous n’utilisez que des [fonctionnalités qui prennent en charge le proxy système](#features-that-support-system-proxy), vous devez d’abord essayer d’utiliser le [**proxy système**](#use-system-proxy-preview). Si vous rencontrez un problème lors de l’utilisation d’un proxy système avec une fonctionnalité prise en charge, [ouvrez un ticket sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

Si vous rencontrez des fonctionnalités qui ne prennent pas en charge le proxy système, l’option [**paramètres de l’application**](#use-app-proxy-settings) est probablement la meilleure à essayer ensuite. L’expérience basée sur l’interface graphique utilisateur pour la configuration du proxy permet de réduire le risque d’entrer incorrectement vos informations de proxy. Toutefois, si vous avez déjà configuré des variables d’environnement de proxy, il peut être préférable d’utiliser les [**variables d’environnement**](#use-environment-variables).

## <a name="azcopy-proxy-usage"></a>Utilisation du proxy AzCopy

Explorateur Stockage utilise AzCopy pour la plupart des opérations de transfert de données. AzCopy est écrit à l’aide d’un ensemble de technologies différent de l’Explorateur Stockage et dispose donc d’un ensemble de fonctionnalités de proxy légèrement différent.

Si Explorateur Stockage est configuré de sorte à **ne pas utiliser le proxy** ou à utiliser le **proxy système**, AzCopy reçoit l’ordre d’utiliser ses propres fonctionnalités de proxy à détection automatique pour déterminer s’il doit envoyer des requêtes à un proxy et comment. Cependant, si vous avez configuré Explorateur Stockage de sorte qu’il se procure les paramètres de proxy à partir de **variables d’environnement** ou de **paramètres de proxy d’application**, Explorateur Stockage indique à AzCopy d’utiliser les mêmes paramètres de proxy.

Si vous ne voulez pas qu’AzCopy utilise de proxy du tout, vous pouvez désactiver l’utilisation de proxy en appuyant sur **Paramètres** (icône représentant un engrenage dans la barre d’outils verticale de gauche) > **Transferts** > **AzCopy** > **Désactiver l’utilisation du proxy AzCopy**.

Actuellement, AzCopy ne prend en charge que les serveurs proxy qui utilisent **l’authentification de base**.

## <a name="ssl-certificates"></a>Certificats SSL

Par défaut, Explorateur Stockage utilise la pile de mise en réseau NodeJS. NodeJS est fourni avec une liste prédéfinie de certificats SSL approuvés. Certaines technologies de mise en réseau, telles que les serveurs proxy ou les logiciels antivirus, injectent leurs propres certificats SSL dans le trafic réseau. Ces certificats sont souvent absents de la liste de certificats de NodeJS. NodeJS n’approuve pas les réponses qui contiennent ce type de certificat. Quand NodeJS n’approuve pas une réponse, Explorateur Stockage reçoit une erreur.

Vous avez plusieurs moyens de résoudre ces erreurs :
- Utiliser un [**proxy système**](#use-system-proxy-preview) comme source de proxy.
- Importer une copie du ou des certificats SSL à l’origine des erreurs.
- Désactiver le certificat SSL. (**non recommandé**)

## <a name="features-that-support-system-proxy"></a>Fonctionnalités prenant en charge le proxy système

La liste suivante répertorie les fonctionnalités qui prennent en charge le **proxy système** :

- Recherche et téléchargement de mises à jour
- Liste des abonnements
- Fonctionnalités de compte de stockage
  - Listing
- Fonctionnalités d’objet blob
  - Conteneurs
    - Créer
    - Listing
    - Gestion des stratégies d’accès stockées
    - Modification du niveau d’accès public
    - Bail
    - Propriétés
    - Supprimer
  - Objets blob
    - Listing
    - Statistiques
    - Annuler la suppression
  - Objets blob ADLS Gen2
    - Listing
    - Statistiques
    - Gestion des listes de contrôle d’accès (uniquement affichage et modification des entités existantes)
    - Propagation des listes de contrôle d’accès
    - Déplacer
    - Renommer
    - Créer un dossier
- Fonctionnalités de file d’attente
  - Files d’attente
    - Créer
    - Listing
    - Gestion des stratégies d’accès
    - Propriétés
    - Supprimer
    - Effacer
  - Messages de file d'attente
    - Listing
    - Déplacer
    - Ajouter
    - Dequeue (Enlever de la file d’attente)
- Fonctionnalités de partage de fichiers
  - Fichiers et dossiers
    - Nouveau dossier
    - Propriétés
- Fonctionnalités de disque
  - Liste des groupes de ressources
  - Liste des disques
  - Chargement de disques
  - Téléchargement de disques
  - Copie de disques
  - Création d’instantanés
  - Suppression de disques

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre des problèmes de proxy](./storage-explorer-troubleshooting.md#proxy-issues)
- [Résolution des problèmes de certificat](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
