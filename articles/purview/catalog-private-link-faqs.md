---
title: Questions fréquentes (FAQ) sur les points de terminaison privés Azure Purview
description: Cet article répond aux questions fréquemment posées concernant les points de terminaison privés Azure Purview.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 3d7a37565b46137a95f0dd30a3e29b750bed3afe
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122535420"
---
# <a name="faq-about-azure-purview-private-endpoints"></a>Questions fréquentes sur les points de terminaison privés Azure Purview

Cet article répond aux questions fréquemment posées par les clients et les équipes de terrain sur les configurations réseau Azure Purview avec [Azure Private Link](../private-link/private-link-overview.md). Il est destiné à clarifier les questions sur les paramètres du pare-feu Azure Purview, les points de terminaison privés, la configuration DNS et les configurations associées.

Pour configurer Azure Purview à l’aide de Private Link, consultez [Utiliser des points de terminaison privés pour votre compte Azure Purview](./catalog-private-link.md).

## <a name="common-questions"></a>Questions courantes

Découvrez les réponses aux questions courantes suivantes.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-account-private-endpoint"></a>Quel est l’objectif du déploiement du point de terminaison privé du compte Azure Purview ?

Le point de terminaison privé du compte Azure Purview permet d’ajouter une couche de sécurité supplémentaire en permettant des scénarios où seuls les appels client provenant du réseau virtuel sont autorisés à accéder au compte. Ce point de terminaison privé est également un prérequis pour le point de terminaison privé du portail.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-portal-private-endpoint"></a>Quel est l’objectif du déploiement du point de terminaison privé du portail Azure Purview ?

Le point de terminaison privé du portail Azure Purview fournit une connectivité privée à Azure Purview Studio.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-ingestion-private-endpoints"></a>Quel est l’objectif du déploiement des points de terminaison d’ingestion privés Azure Purview ?

Azure Purview peut analyser des sources de données dans un environnement Azure ou local à l’aide de points de terminaison d’ingestion privés. Trois autres ressources de point de terminaison privé supplémentaires sont déployées et liées aux ressources managées Azure Purview quand des points de terminaison d’ingestion privés sont créés :

- **Blob** est lié à un compte de stockage managé Azure Purview
- **File d’attente** est liée à un compte de stockage managé Azure Purview
- **Espace de noms** est lié à l’espace de noms Event Hub managé Azure Purview

### <a name="can-i-scan-data-through-a-public-endpoint-if-a-private-endpoint-is-enabled-on-my-azure-purview-account"></a>Puis-je analyser des données via un point de terminaison public si le point de terminaison privé est activé sur mon compte Azure Purview ?

Oui. Les sources de données qui ne sont pas connectées par un point de terminaison privé peuvent être analysées en utilisant un point de terminaison public alors que Azure Purview est configuré pour utiliser un point de terminaison privé.

### <a name="can-i-scan-data-through-a-service-endpoint-if-a-private-endpoint-is-enabled"></a>Puis-je analyser des données via un point de terminaison de service si un point de terminaison privé est activé ?

Oui. Les sources de données qui ne sont pas connectées par un point de terminaison privé peuvent être analysées en utilisant un point de terminaison de service alors que Azure Purview est configuré pour utiliser un point de terminaison privé.

Assurez-vous d’activer l’option **Autoriser les services Microsoft approuvés** pour accéder aux ressources dans la configuration du point de terminaison du service de la ressource source de données dans Azure. Par exemple, si vous souhaitez analyser Stockage Blob Azure dans lequel les paramètres des pare-feu et des réseaux virtuels sont définis sur des **réseaux sélectionnés**, vous devez vous assurer que la case **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** est cochée en tant qu’exception.

### <a name="can-i-access-azure-purview-studio-from-a-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>Puis-je accéder à Azure Purview Studio à partir d’un réseau public si l’accès au réseau public est défini sur Refuser dans la mise en réseau du compte Azure Purview ?

Non. La connexion à Azure Purview à partir d’un point de terminaison où **Accès réseau public** est défini sur **Refuser** entraîne l’affichage du message d’erreur suivant :

« Non autorisé à accéder à ce compte Purview Ce compte Purview est derrière un point de terminaison privé. Accédez au compte à partir d’un client dans le même réseau virtuel (VNet) que celui configuré pour le point de terminaison privé du compte Purview. »

Dans ce cas, pour ouvrir Azure Purview Studio, utilisez soit une machine qui est déployée dans le même réseau virtuel que le point de terminaison privé du Portail Azure Purview, soit une machine virtuelle qui est connectée à votre CorpNet dans lequel la connectivité hybride est autorisée.

### <a name="is-it-possible-to-restrict-access-to-the-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-users-across-the-web"></a>Est-il possible de restreindre l’accès au compte de stockage managé Azure Purview et à l’espace de noms Event Hub (pour l’ingestion de point de terminaison privé uniquement), tout en gardant le portail accessible aux utilisateurs finaux sur le web ?

Non. Quand vous définissez **Accès au réseau public** sur **Refuser**, l’accès au compte de stockage managé Azure Purview et à l’espace de noms Event Hub est automatiquement défini pour l’ingestion de point de terminaison privé uniquement. Quand vous définissez **Accès au réseau public** sur **Autoriser**, l’accès au compte de stockage managé Azure Purview et à l’espace de noms Event Hub est automatiquement défini pour **Tous les réseaux**. Il n’est pas possible de modifier manuellement l’ingestion de point de terminaison privé pour le compte de stockage managé ou l’espace de noms Event Hub.

### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-namespace-can-be-publicly-accessible"></a>Si l’accès au réseau public est défini sur Autoriser, cela signifie-t-il que le compte de stockage managé et l’espace de noms Event Hub sont accessibles publiquement ?

Non. En tant que ressources protégées, l’accès au compte de stockage managé Azure Purview et à l’espace de noms Event Hub est limité à Azure Purview uniquement. Ces ressources sont déployées avec une affectation de refus à tous les principaux, ce qui empêche les applications, les utilisateurs ou les groupes d’y accéder.

Pour en savoir plus sur l’affectation de refus Azure, consultez la rubrique [Comprendre les affectations de refus Azure](../role-based-access-control/deny-assignments.md).

### <a name="what-are-the-supported-authentication-types-when-you-use-a-private-endpoint"></a>Quel sont les types d’authentification pris en charge lorsque vous utilisez un point de terminaison privé ?

Azure Key Vault ou principal de service.

### <a name="what-private-dns-zones-are-required-for-azure-purview-for-a-private-endpoint"></a>Quelles sont les zones DNS privées requises pour Azure Purview pour un point de terminaison privé ?

Pour les points de terminaison privés _compte_ et _portail_ Azure Purview :

- `privatelink.purview.azure.com`

Pour les points de terminaison d’_ingestion_ privés Azure Purview :

- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`

### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-i-deploy-azure-purview-private-endpoints"></a>Dois-je utiliser un réseau virtuel dédié et un sous-réseau dédié lorsque je déploie des points de terminaison privés Azure Purview ?

Non. Cependant, `PrivateEndpointNetworkPolicies` doit être désactivé dans le sous-réseau de destination avant que vous ne déployiez les points de terminaison privés. Envisagez de déployer Azure Purview sur un réseau virtuel qui dispose d’une connectivité réseau vers les réseaux virtuels des sources de données via VNet Peering et de l’accès au réseau local si vous envisagez d’analyser des sources de données entre différents emplacements.

Découvrez comment [Désactiver les stratégies réseau pour les points de terminaison privés](../private-link/disable-private-endpoint-network-policy.md).

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>Puis-je déployer des points de terminaison privés Azure Purview et utiliser des zones DNS privées existantes dans mon abonnement pour inscrire les enregistrements A ?

Oui. Vos zones DNS de points de terminaison privés peuvent être centralisées dans un abonnement de gestion des données ou de hub pour toutes les zones DNS internes requises pour Azure Purview et tous les enregistrements de source de données. Nous recommandons cette méthode pour autoriser Azure Purview à résoudre les sources de données en utilisant les adresses IP internes de leurs points de terminaison privés.

Vous devez également configurer un [lien de réseau virtuel](../dns/private-dns-virtual-network-links.md) pour les réseaux virtuels de la zone DNS privée existante.

### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-a-private-endpoint"></a>Puis-je utiliser le runtime d’intégration Azure pour analyser des sources de données via un point de terminaison privé ?

Non. Vous devez déployer et inscrire un runtime d’intégration auto-hébergé pour analyser les données à l’aide d’une connectivité privée. Azure Key Vault ou le principal de service doit être utilisé comme méthode d’authentification pour les sources de données.

### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-you-use-a-private-endpoint"></a>Quelles sont les exigences pour les ports de sortie et le pare-feu pour les machines virtuelles avec runtime d’intégration auto-hébergé pour Azure Purview lorsque vous utilisez un point de terminaison privé ?

Les machines virtuelles dans lesquelles le runtime d’intégration auto-hébergé est déployé doivent disposer d’un accès sortant aux points de terminaison Azure et à une adresse IP privée Azure Purview via le port 443.

### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-a-private-endpoint-is-enabled"></a>Dois-je activer l’accès Internet sortant à partir de la machine virtuelle exécutant le runtime d’intégration auto-hébergé si le point de terminaison privé est activé ?

Non. Toutefois, la machine virtuelle exécutant le runtime d’intégration auto-hébergé doit généralement pouvoir se connecter à votre instance Azure Purview par une adresse IP interne via le port 443. Utilisez des outils de résolution des problèmes courants pour la résolution de noms et le test de connectivité, tels que nslookup.exe et Test-NetConnection.

### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>Pourquoi le message d’erreur suivant s’affiche-t-il quand j’essaie de lancer Azure Purview Studio à partir de ma machine ?

« Ce compte Purview est derrière un point de terminaison privé. Accédez au compte à partir d’un client dans le même réseau virtuel (VNet) que celui configuré pour le point de terminaison privé du compte Purview. »

Il est probable que votre compte Azure Purview est déployé à l’aide d’Azure Private Link et que l’accès public est désactivé sur votre compte Azure Purview. Vous devez donc parcourir Azure Purview Studio à partir d’une machine virtuelle qui dispose d’une connectivité réseau interne à Azure Purview.

Si vous vous connectez à partir d’une machine virtuelle derrière un réseau hybride ou à l’aide d’une machine de rebond connectée à votre réseau virtuel, utilisez les outils de dépannage courants pour la résolution de noms et le test de connectivité, tels que nslookup.exe et Test-NetConnection.

1. Vérifiez si vous pouvez résoudre les adresses suivantes à l’aide des adresses IP privées de votre compte Azure Purview :

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

1. Vérifiez la connectivité réseau à votre compte Azure Purview à l’aide de la commande PowerShell suivante :

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

1. Vérifiez votre configuration DNS entre différents emplacements si vous utilisez votre propre infrastructure de résolution DNS.

Pour plus d’informations sur les paramètres DNS pour points de terminaison privés, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Étapes suivantes

Pour configurer Azure Purview à l’aide de Private Link, consultez [Utiliser des points de terminaison privés pour votre compte Azure Purview](./catalog-private-link.md).
