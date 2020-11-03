---
title: Azure Private Link pour Azure Data Factory
description: Découvrez comment fonctionne Azure Private Link dans Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: c21b4d746d763f41f4360cf93f67939bcd6dc49f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632683"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link pour Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

En utilisant Azure Private Link, vous pouvez vous connecter à différents déploiements PaaS (platform as a service) dans Azure via un point de terminaison privé. Un point de terminaison privé est une adresse IP privée au sein d’un réseau et d’un sous-réseau virtuels spécifiques. Pour obtenir la liste des déploiements PaaS prenant en charge la fonctionnalité Liaison privée, consultez la [documentation sur Liaison privée](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Sécuriser la communication entre les réseaux du client et Azure Data Factory 
Vous pouvez configurer un réseau virtuel Azure comme une représentation logique de votre réseau dans le cloud. Les avantages que vous en tirez sont les suivants :
* Vous protégez mieux vos ressources Azure contre les attaques sur les réseaux publics.
* Vous laissez les réseaux et Data Factory communiquer entre eux de façon sécurisée. 

Vous pouvez également connecter un réseau local à votre réseau virtuel en configurant une connexion VPN (site à site) IPsec (sécurité du protocole Internet) ou une connexion Azure ExpressRoute (peering privé). 

Vous pouvez également installer un runtime d’intégration auto-hébergé sur une machine locale ou une machine virtuelle dans le réseau virtuel. Cela vous permet d’effectuer les opérations suivantes :
* Exécuter des activités de copie entre un magasin de données cloud et un magasin de données situé sur un réseau privé.
* Répartir les activités de transformation selon les ressources de calcul dans un réseau local ou un réseau virtuel Azure. 

Plusieurs canaux de communication sont nécessaires entre Azure Data Factory et le réseau virtuel du client, comme indiqué dans le tableau suivant :

| Domain | Port | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Plan de contrôle nécessaire pour la création et la supervision de Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. |
| `*.servicebus.windows.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour la création interactive. |
| `download.microsoft.com` | 443 | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. |

Avec la prise en charge de Liaison privée pour Azure Data Factory, vous pouvez :
* Créer un point de terminaison privé dans votre réseau virtuel.
* Activer la connexion privée à une instance spécifique de la fabrique de données. 

Les communications avec le service Azure Data Factory transitent par Liaison privée et assurent une connectivité privée sécurisée. Vous n’avez pas besoin de configurer le domaine et le port précédents sur un réseau virtuel ou votre pare-feu d’entreprise pour offrir un moyen plus sûr de protéger vos ressources.  

![Diagramme d’une liaison privée pour Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

L’activation du service Private Link pour chacun des canaux de communication précédents offre les fonctionnalités suivantes :
- **Pris en charge**  :
   - Vous pouvez créer et superviser la fabrique de données sur votre réseau virtuel, même si vous bloquez toutes les communications sortantes.
   - Les communications de commande entre le runtime d’intégration auto-hébergé et le service Azure Data Factory peuvent être établies en toute sécurité dans un environnement de réseau privé. Le trafic entre le runtime d’intégration auto-hébergé et le service Azure Data Factory transite par une liaison privée. 
- **Non pris en charge pour l’instant**  :
   - Une création interactive qui utilise un runtime d’intégration auto-hébergé, par exemple, pour tester la connexion, parcourir la liste des dossiers et la liste des tables, obtenir le schéma et afficher un aperçu des données transite par Private Link.
   - La nouvelle version du runtime d’intégration auto-hébergé peut être téléchargée automatiquement à partir du centre de téléchargement Microsoft si vous activez la mise à jour automatique.

   > [!NOTE]
   > Pour les fonctionnalités non encore prises en charge, vous devez toujours configurer le domaine et le port précédemment mentionnés dans le réseau virtuel ou le pare-feu de votre entreprise. 

> [!WARNING]
> Quand vous créez un service lié, vérifiez que vos informations d’identification sont stockées dans un coffre de clés Azure. Sinon, les informations d’identification ne fonctionnent pas quand vous activez Private Link dans Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Configurer Azure Private Link pour Azure Data Factory
Vous pouvez créer des points de terminaison privés via le [portail Azure](../private-link/create-private-endpoint-portal.md).

Vous pouvez également accéder à votre fabrique de données Azure dans le portail Azure et créer un point de terminaison privé, comme indiqué ici :

![Capture d’écran du volet « Connexions des points de terminaison privés » pour la création d’un point de terminaison privé.](./media/data-factory-private-link/create-private-endpoint.png)


Si vous voulez bloquer l’accès public à cette fabrique de données Azure et autoriser l’accès uniquement via Private Link, désactivez l’accès réseau à Azure Data Factory dans le portail Azure, comme indiqué ici :

![Capture d’écran du volet « Accès réseau » pour la création d’un point de terminaison privé.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> La désactivation de l’accès au réseau public s’applique uniquement au runtime d’intégration auto-hébergé, pas à Azure Integration Runtime ni au runtime d’intégration SSIS (SQL Server Integration Services).

> [!NOTE]
> Vous pouvez quand même accéder au portail Azure Data Factory par le biais d’un réseau public après la désactivation de l’accès au réseau public.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Présentation du service Azure Data Factory](introduction.md)
- [Création visuelle dans Azure Data Factory](author-visually.md)