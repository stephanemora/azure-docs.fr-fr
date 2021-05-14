---
title: Paramètres de configuration d’AzCopy v10 (Stockage Azure) | Microsoft Docs
description: Cet article fournit des informations de référence pour les paramètres de configuration d’AzCopy v10.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107508946"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Paramètres de configuration d’AzCopy v10 (Stockage Azure)

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article contient une liste de variables d’environnement que vous pouvez utiliser pour configurer AzCopy v10.

> [!NOTE]
> Si vous recherchez du contenu pour vous aider à bien démarrer avec AzCopy, consultez [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md).

## <a name="azcopy-v10-environment-variables"></a>Variables d’environnement d’AzCopy v10

Le tableau suivant décrit chaque variable d’environnement et fournit des liens vers du contenu qui peut vous aider à utiliser la variable.

| Variable d’environnement | Description |
|--|--|
| AWS_ACCESS_KEY_ID | Clé d’accès d’Amazon Web Services. Fournit une clé pour autoriser l’utilisation avec Amazon Web Services. [Copier des données d’Amazon S3 vers Stockage Azure Storage en utilisant AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Clé d’accès secrète d’Amazon Web Services. Fournit une clé secrète pour autoriser l’utilisation avec Amazon Web Services. [Copier des données depuis Amazon S3 vers le Stockage Azure avec AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Point de terminaison Azure Active Directory (Azure AD) à utiliser. Cette variable est utilisée seulement pour la connexion automatique. Utilisez plutôt l’indicateur de ligne de commande lors de l’appel de la commande de connexion. |
| AZCOPY_AUTO_LOGIN_TYPE | Définissez cette variable sur `DEVICE`, `MSI` ou `SPN`. Cette variable permet d’autoriser sans utiliser la commande `azcopy login`. Ce mécanisme est utile dans les cas où votre système d’exploitation ne dispose pas d’un magasin de secrets comme un *porte-clés* Linux. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_BUFFER_GB | Spécifiez la quantité maximale de mémoire système qu’AzCopy doit utiliser lors du téléchargement et du chargement de fichiers. Exprimez cette valeur en gigaoctets (Go). Consultez [Optimiser l’utilisation de la mémoire](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Par défaut, AzCopy sur Windows met en cache les recherches du serveur proxy au niveau du nom d’hôte (ne prend pas en compte le chemin d’URL). Définissez sur une valeur autre que « true » pour désactiver le cache. |
| AZCOPY_CONCURRENCY_VALUE | Cette variable spécifie le nombre de demandes pouvant être effectuées simultanément. Vous pouvez utiliser cette variable pour augmenter le débit. Si votre ordinateur dispose de moins de 5 unités centrales, la valeur de cette variable est définie sur `32`. Sinon, la valeur par défaut est égale à 16 multiplié par le nombre d’unités centrales. La valeur maximale par défaut de cette variable est `3000`, mais vous pouvez l’augmenter ou la diminuer manuellement. Consultez [Augmenter les accès concurrentiels](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Remplace le nombre (approximatif) de fichiers en cours à un moment donné, en contrôlant le nombre de fichiers pour lesquels nous lançons des transferts simultanément. |
| AZCOPY_CONCURRENT_SCAN | Contrôle le degré (maximal) de parallélisme utilisé lors de l’analyse. Affecte seulement les énumérateurs parallélisés, qui incluent Azure Files/Objets blob Azure et des systèmes de fichiers locaux. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Remplace la version de l’API de service afin pour qu’AzCopy puisse prendre en charge des environnements personnalisés comme Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | S’applique seulement quand Stockage Blob Azure est la source. L’analyse simultanée est plus rapide mais utilise l’API de liste hiérarchique, ce qui peut entraîner un coût plus élevé en matière d’E/S. Spécifiez « true » pour sacrifier les performances au profit du coût. |
| AZCOPY_JOB_PLAN_LOCATION | Remplace l’emplacement de stockage des fichiers du plan de travail (utilisés pour le suivi de l’avancement et la reprise) pour éviter de saturer un disque. |
| AZCOPY_LOG_LOCATION | Remplace l’emplacement de stockage des fichiers journaux pour éviter de saturer un disque. |
| AZCOPY_MSI_CLIENT_ID | ID de client d’une identité managée affectée par l’utilisateur. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `MSI`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_MSI_OBJECT_ID | ID d’objet de l’identité managée affectée par l’utilisateur. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `MSI`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_MSI_RESOURCE_STRING | ID de ressource de l’identité managée affectée par l’utilisateur. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_PACE_PAGE_BLOBS | Le débit des objets blob de pages doit-il être ajusté automatiquement pour correspondre aux limites du service ? La valeur par défaut est true. Définissez sur « false » pour désactiver |
| AZCOPY_PARALLEL_STAT_FILES | Fait qu’AzCopy recherche les propriétés des fichiers sur des « threads » parallèles lors de l’analyse du système de fichiers local.  Les threads sont tirés du pool défini par AZCOPY_CONCURRENT_SCAN.  Définir ceci sur true peut améliorer les performances de l’analyse sur Linux.  Non nécessaire ni recommandé sur Windows. |
| AZCOPY_SHOW_PERF_STATES | Si cette valeur est définie, la sortie à l’écran inclut le nombre de blocs par état. |
| AZCOPY_SPA_APPLICATION_ID | ID d’application de l’inscription d’application du principal de service. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `SPN`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CERT_PASSWORD | Mot de passe d’un certificat. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `SPN`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CERT_PATH | Chemin relatif ou complet d’un fichier de certificat. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `SPN`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CLIENT_SECRET | Secret client. Utilisez cela quand `AZCOPY_AUTO_LOGIN_TYPE` est défini sur `SPN`. Consultez [Autoriser sans magasin de secrets](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_TENANT_ID | ID de locataire Azure Active Directory à utiliser pour la connexion interactive de l’appareil OAuth. Cette variable est utilisée seulement pour la connexion automatique. Utilisez plutôt l’indicateur de ligne de commande lors de l’appel de la commande de connexion. |
| AZCOPY_TUNE_TO_CPU | Définissez sur false pour empêcher AzCopy de prendre en compte l’utilisation du processeur lors de l’optimisation automatique de son niveau d’accès concurrentiel (par exemple dans la commande benchmark). |
| AZCOPY_USER_AGENT_PREFIX | Ajoutez un préfixe à l’agent utilisateur d’AzCopy par défaut, qui est utilisé à des fins de télémétrie. Un espace est inséré automatiquement. |
| GOOGLE_APPLICATION_CREDENTIALS | Le chemin absolu au fichier de clés du compte de service. Fournit une clé pour autoriser l’utilisation de Google Cloud Storage. [Copier les données de Google Cloud Storage vers Stockage Azure à l’aide d’AzCopy (préversion)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Configure les paramètres de proxy pour AzCopy. Définissez cette variable sur l’adresse IP du proxy et le numéro de port du proxy. Par exemple : `xx.xxx.xx.xxx:xx`. Si vous exécutez AzCopy sur Windows, AzCopy détecte automatiquement les paramètres de proxy. Vous n’avez donc pas besoin d’utiliser ce paramètre dans Windows. Si vous choisissez d’utiliser ce paramètre dans Windows, il remplace la détection automatique. Consultez [Configurer les paramètres de proxy](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy pour AzCopy, définissez la variable d’environnement `HTTPS_PROXY`. Si vous exécutez AzCopy sur Windows, AzCopy détecte automatiquement les paramètres de proxy. Vous n’avez donc pas besoin d’utiliser ce paramètre dans Windows. Si vous choisissez d’utiliser ce paramètre dans Windows, il remplace la détection automatique.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | Dans une invite de commandes, tapez : `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> Pour PowerShell, tapez : `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Actuellement, AzCopy ne prend en charge les serveurs proxy qui requièrent une authentification avec NTLM ou Kerberos.

### <a name="bypassing-a-proxy"></a>Contournement d’un proxy

Si vous exécutez AzCopy sur Windows et souhaitez lui demander de n’utiliser aucun _proxy_ (au lieu de détecter automatiquement les paramètres), utilisez ces commandes. Avec ces paramètres, AzCopy ne recherche pas ou ne tente pas d’utiliser un proxy.

| Système d’exploitation | Environnement | Commandes  |
|--------|-----------|----------|
| **Windows** | Invite de commandes (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Sur d’autres systèmes d’exploitation, laissez simplement la variable HTTPS_PROXY non définie si vous ne souhaitez pas utiliser de proxy.

## <a name="see-also"></a>Voir aussi

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Optimiser les performances d’AzCopy v10 avec Stockage Azure](storage-use-azcopy-optimize.md)
- [Résoudre les problèmes d’AzCopy v10 dans Stockage Azure en utilisant des fichiers journaux](storage-use-azcopy-configure.md)
