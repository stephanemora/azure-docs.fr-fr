---
title: Azure Private Link pour Azure Data Factory
description: Apprenez-en davantage sur le fonctionnement d’Azure Private Link dans Azure Data Factory.
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
ms.openlocfilehash: 1d560bd4a6b826e2bf0b3e23dde563cc39863537
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322812"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link pour Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Private Link vous permet de vous connecter à différents services PaaS dans Azure par le biais d’un point de terminaison privé. Pour obtenir la liste des services PaaS prenant en charge la fonctionnalité Liaison privée, accédez à la page [Documentation sur Liaison privée](https://docs.microsoft.com/azure/private-link/). Un point de terminaison privé est une adresse IP privée au sein d’un réseau et d’un sous-réseau virtuels spécifiques.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Communication sécurisée entre le réseau client et le service Azure Data Factory
Pour protéger vos ressources Azure contre les attaques dans un réseau public ou les laisser communiquer entre elles en toute sécurité, vous pouvez configurer un réseau virtuel Azure en tant que représentation logique de votre réseau dans le cloud. Vous pouvez connecter un réseau local à votre réseau virtuel Azure en configurant VPN IPSec (site à site) ou ExpressRoute (appairage privé). Vous pouvez installer le runtime d’intégration auto-hébergé sur un ordinateur local ou une machine virtuelle dans un réseau virtuel pour exécuter des activités de copie entre un magasin de données cloud et un magasin de données dans un réseau privé, ou répartir des activités de transformation par rapport à des ressources de calcul dans un réseau local ou un réseau virtuel Azure. 

Quelques canaux de communication sont requis entre Data Factory et le réseau virtuel du client.


| **Domaine** | **Port** | **Description** |
| ---------- | -------- | --------------- |
| `pe-adf.azure.com` | 443 | Plan de contrôle. Requis par la création et la surveillance de Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. |
| `*.servicebus.windows.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour la création interactive. |
| `download.microsoft.com` | 443 | Requis par le runtime d’intégration auto-hébergé pour le téléchargement des mises à jour. |


Avec la prise en charge d’Azure Private Link pour Azure Data Factory, vous pouvez créer un point de terminaison privé (PE) dans votre réseau virtuel et activer la connexion privée à l’Azure Data Factory spécifique. Les communications avec le service de Azure Data Factory transitent par le service Azure Private Link qui fournit une connectivité privée sécurisée. Et vous n’avez pas besoin de configurer le domaine et le port ci-dessus dans le réseau virtuel ou votre pare-feu d’entreprise, qui offrent un moyen plus sûr de protéger vos ressources.  

![Architecture de liaison privée Azure Data Factory](./media/data-factory-private-link/private-link-architecture.png)

Voici les avantages de l’activation du service Liaison privée pour chacun des canaux de communication décrits ci-dessus :
- (Pris en charge) Vous pouvez créer et surveiller Azure Data Factory dans votre réseau virtuel, voire bloquer toutes les communications sortantes.
- (Pris en charge) Les communications de commande entre le runtime d’intégration auto-hébergé et le service Azure Data Factory peuvent être établies en toute sécurité dans un environnement de réseau privé. Le trafic entre le runtime d’intégration auto-hébergé et le service Azure Data Factory transite par une liaison privée. 
- (Non pris en charge pour l’instant) La création interactive à l’aide du runtime d’intégration auto-hébergé transite par Azure Private Link, par exemple, pour tester la connexion, parcourir la liste des dossiers et la liste des tables, obtenir le schéma et afficher un aperçu des données.
- (Non pris en charge pour l’instant) La nouvelle version du runtime d’intégration auto-hébergé peut être téléchargée automatiquement à partir du centre de téléchargement si vous activez la mise à jour automatique.

> [!NOTE]
> Pour les fonctionnalités non encore prises en charge, vous devez toujours configurer le domaine et le port ci-dessus dans le réseau virtuel ou le pare-feu de votre entreprise. 

> [!WARNING]
> Lorsque vous créez un service lié, assurez-vous que les informations d’identification sont stockées dans Azure Key Vault. Autrement, il ne fonctionne pas lorsque vous activez le service Liaison privée dans Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Comment configurer Azure Private Link pour Azure Data Factory
Vous pouvez créer des points de terminaison privés avec le portail Azure, PowerShell ou l’interface Azure CLI :

[Portail](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Vous pouvez également accéder à votre Azure Data Factory dans le portail Azure, et créer un point de terminaison privé (PE) :

![Créer un point de terminaison privé](./media/data-factory-private-link/create-private-endpoint.png)


Si vous souhaitez bloquer l’accès public à cette Azure Data Factory et autoriser l’accès uniquement via une Liaison privée, vous pouvez désactiver l’accès réseau d’Azure Data Factory dans le portail Azure :

![Créer un point de terminaison privé](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> La désactivation de l’accès au réseau public s’applique uniquement au runtime d’intégration auto-hébergé, non aux runtimes d’intégration Azure et SSIS.

> [!NOTE]
> Les utilisateurs peuvent toujours accéder au portail Azure Data Factory via le réseau public après la désactivation de l’accès au réseau public.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Présentation du service Azure Data Factory](introduction.md)

- [Création visuelle dans Azure Data Factory](author-visually.md)

