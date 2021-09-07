---
title: Chiffrement double d’infrastructure - Azure Database pour MySQL
description: Découvrez comment utiliser le chiffrement double d’infrastructure pour ajouter une deuxième couche de chiffrement avec des clés gérées par le service.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 9b6d87c3bfabf3884d9a90966994eea002a45dd8
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674432"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database pour MySQL Chiffrement double d’infrastructure

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database pour MySQL utilise le stockage [chiffrement des données au repos](concepts-security.md#at-rest) pour les données utilisant des clés gérées par Microsoft. Les données, y compris les sauvegardes, sont chiffrées sur le disque et ce chiffrement est toujours activé et ne peut pas être désactivé. Le chiffrement utilise le module de chiffrement validé FIPS 140-2 et un chiffrement AES 256 bits pour le chiffrement de stockage Azure.

Le chiffrement double d’infrastructure ajoute une deuxième couche de chiffrement avec des clés gérées par le service. Il utilise le module de cryptage validé FIPS 140-2, mais avec un algorithme de chiffrement différent. Cela fournit une couche supplémentaire de protection pour vos données au repos. La clé utilisée dans le chiffrement double d’infrastructure est également gérée par le service Azure Database pour MySQL. Le chiffrement double d’infrastructure n’est pas activé par défaut, car la couche supplémentaire de chiffrement peut avoir un impact sur les performances.

> [!NOTE]
> À l’instar du chiffrement des données au repos, cette fonctionnalité est prise en charge seulement sur le stockage « Stockage v2 universel (prise en charge jusqu’à 16 to) » disponible dans les niveaux tarifaires Usage général et Mémoire optimisée. Pour plus d’informations, consultez [Concepts du stockage](concepts-pricing-tiers.md#storage). Pour connaître les autres limitations, consultez la section [Limitation](concepts-infrastructure-double-encryption.md#limitations).

Le chiffrement de couche d’infrastructure présente l’avantage d’être implémenté au niveau de la couche la plus proche du dispositif de stockage ou des câbles réseau. Azure Database pour MySQL implémente les deux couches de chiffrement à l’aide de clés gérées par le service. Bien que toujours techniquement dans la couche de service, il est très proche du matériel qui stocke les données au repos. Vous pouvez également activer le chiffrement des données au repos à l’aide de [clé gérée par le client](concepts-data-encryption-mysql.md) pour le serveur MySQL approvisionné. 

L’implémentation aux couches d’infrastructure prend également en charge des clés différentes. L’infrastructure doit être consciente des différents clusters de machines et de réseaux. Par conséquent, différentes clés sont utilisées pour réduire le rayon d’explosion des attaques d’infrastructure et une grande variété de défaillances matérielles et réseau. 

> [!NOTE]
> L’utilisation du chiffrement double d’infrastructure aura un impact de 5 à 10 % sur le débit de votre serveur Azure Database pour MySQL en raison du processus de chiffrement supplémentaire.

## <a name="benefits"></a>Avantages

Le chiffrement double d’infrastructure pour Azure Database pour MySQL offre les avantages suivants :

1. **Diversité supplémentaire de l’implémentation du chiffrement** - Le passage planifié au chiffrement basé sur le matériel permet de diversifier davantage les implémentations en fournissant une implémentation basée sur le matériel en plus de celle basée sur le logiciel.
2. **Erreurs d’implémentation**- Deux couches de chiffrement au niveau de l’infrastructure protègent des erreurs de mise en cache ou de gestion de la mémoire dans des couches supérieures qui exposent des données en texte clair. En outre, les deux couches protègent également des erreurs dans l’implémentation du chiffrement en général.

Cette combinaison offre une protection renforcée contre les menaces courantes et les faiblesses exploitées pour attaquer le chiffrement.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Scénarios pris en charge avec le chiffrement double d’infrastructure

Les fonctionnalités de chiffrement fournies par Azure Database pour MySQL peuvent être utilisées ensemble. Vous trouverez ci-dessous un résumé des différents scénarios que vous pouvez utiliser :

|  ##   | Chiffrement par défaut | Chiffrement double d’infrastructure | Chiffrement à l’aide de clés gérées par le client  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Oui*              | *Non*                             | *Non*                                         |
| 2     | *Oui*              | *Oui*                            | *Non*                                         |
| 3     | *Oui*              | *Non*                             | *Oui*                                        |
| 4     | *Oui*              | *Oui*                            | *Oui*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Les scénarios 2 et 4 peuvent introduire une baisse de 5 à 10 % du débit en fonction du type de charge de travail pour le serveur Azure Database pour MySQL en raison de la couche supplémentaire de chiffrement d’infrastructure.
> - La configuration du double chiffrement de l’infrastructure pour Azure Database pour MySQL est uniquement autorisée pendant la création du serveur. Une fois que le serveur est approvisionné, vous ne pouvez pas modifier le chiffrement de stockage. Toutefois, vous pouvez toujours activer le chiffrement des données à l’aide de clés gérées par le client pour le serveur créé avec/sans le chiffrement double de l’infrastructure.

## <a name="limitations"></a>Limites

Pour Azure Database pour MySQL, la prise en charge du chiffrement double d’infrastructure a quelques limitations :

* La prise en charge de cette fonctionnalité est limitée aux niveaux tarifaires **Usage général** et **À mémoire optimisée**.
* Cette fonctionnalité est prise en charge seulement dans les régions et les serveurs qui prennent en charge le stockage v2 universel (jusqu’à 16 To). Pour obtenir la liste des régions Azure qui prennent en charge le stockage jusqu’à 16 To, reportez-vous à la section consacrée au stockage dans la documentation [ici](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Pour tous les nouveaux serveurs MySQL créés dans les [régions Azure](concepts-pricing-tiers.md#storage) prenant en charge le stockage v2 universel, la prise en charge du chiffrement avec des clés CMK est **disponible**. En théorie, le serveur de Limite de restauration dans le temps ou le réplica de lecture ne sont pas signalés comme des ressources « nouvelles ».
    > - Pour vérifier si votre serveur provisionné prend en charge le stockage v2 universel, vous pouvez accéder au volet des niveaux tarifaires dans le portail et voir la taille de stockage maximale prise en charge par votre serveur provisionné. Si vous pouvez déplacer le curseur jusqu’à 4 To, c’est que votre serveur est sur un stockage v1 universel et qu’il ne prend pas en charge le chiffrement avec des clés gérées par le client. Toutefois, les données sont chiffrées à l’aide de clés managées par le service à tout moment. Posez vos questions en contactant AskAzureDBforMySQL@service.microsoft.com.



## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer le double chiffrement de l’infrastructure pour Azure Database pour MySQL](howto-double-encryption.md).
