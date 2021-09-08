---
title: Guide du développeur Azure Germany | Microsoft Docs
description: Cet article dresse une analyse comparative des fonctionnalités et fournit des conseils pour le développement d’applications pour Azure Germany.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: e631c37123c375ac3005aceb6c51289f07d9a22d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122562087"
---
# <a name="azure-germany-developer-guide"></a>Guide du développeur Azure Germany

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

L’environnement Azure Germany est une instance de Microsoft Azure distincte du reste du réseau de Microsoft. Ce guide présente les différences que les développeurs d’applications et les administrateurs doivent comprendre pour utiliser des régions distinctes d’Azure.

## <a name="overview"></a>Vue d'ensemble
Microsoft fournit un certain nombre d'outils pour aider les développeurs à créer et déployer des applications cloud pour le service global Microsoft Azure (« Azure global ») et les services Microsoft Azure Germany. Azure Germany répond aux besoins de sécurité et de conformité des clients en matière de respect de la réglementation allemande sur la protection des données. Azure Germany offre une isolation physique et du réseau des déploiements mondiaux Azure et met à disposition un administrateur de données agissant selon la loi allemande.

Lorsque des développeurs créent et déploient des applications vers Azure Germany et non vers à Azure global, ils doivent connaître les différences entre ces deux jeux de services. Ils doivent notamment bien comprendre l’installation et la configuration de leur environnement de programmation, la configuration des points de terminaison, l’écriture des applications et leur déploiement en tant que services dans Azure Germany.

Les informations contenues dans ce guide résument ces différences. Elles complètent les informations disponibles sur le site [Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/ "Azure Allemagne")et le[Centre de documentation Azure](https://azure.microsoft.com/documentation/). 

Des informations officielles peuvent également être disponibles dans d’autres emplacements, notamment :
* [Centre de confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centre de gestion de la confidentialité Microsoft Azure") 
* [Blog Azure](https://azure.microsoft.com/blog/ "Blog Azure")
* [Blog Azure Germany](/archive/blogs/azuregermany/ "Blog Azure Germany")

## <a name="guidance-for-developers"></a>Aide pour les développeurs
La plupart du contenu technique disponible actuellement part du principe que les applications sont développées pour Azure global plutôt que pour Azure Germany. C’est pourquoi il est important de connaître les deux principales différences concernant les applications que vous développez pour l’hébergement dans Azure Allemagne :

* Il est possible que certains services et fonctionnalités liés à des régions spécifiques d’Azure global ne soient pas disponibles dans Azure Allemagne.
* La configuration des fonctionnalités dans Azure Germany peut différer de celle d’Azure global. Il est important de vérifier vos exemples de code, configurations et étapes de travail pour vous assurer que vous générez et exécutez des applications dans l'environnement des services cloud Azure Germany.

Actuellement, Allemagne Centre et Allemagne Nord-Est sont les régions disponibles dans Azure Germany. Pour obtenir la liste des régions et services disponibles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Mappage de point de terminaison
Pour en savoir plus sur le mappage des points de terminaison publics Azure global et Azure SQL Database à des points de terminaison Azure Germany spécifiques, consultez le tableau suivant :

| Name | Point de terminaison Azure Germany |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | `https://management.core.cloudapi.de/` |
| GalleryUrl                               | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl                      | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl                     | `https://management.core.cloudapi.de/` |
| PublishSettingsFileUrl                   | `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl                       | `https://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix                     | `.database.cloudapi.de` |
| StorageEndpointSuffix                    | `core.cloudapi.de` |
| ActiveDirectoryAuthority                 | `https://login.microsoftonline.de/` |
| GraphUrl                                 | `https://graph.cloudapi.de/` |
| TrafficManagerDnsSuffix                  | `azuretrafficmanager.de` |
| AzureKeyVaultDnsSuffix                   | `vault.microsoftazure.de` |
| AzureKeyVaultServiceEndpointResourceId   | `https://vault.microsoftazure.de` |
| Suffixe Service Bus                       | `servicebus.cloudapi.de` |


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Germany, consultez les ressources suivantes :

* [S’inscrire pour un essai](https://azure.microsoft.com/free/germany/)
* [Acquisition d’Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)
* [Connectez-vous](https://portal.microsoftazure.de/) si vous avez déjà un compte Azure Germany
* [Vue d’ensemble sur Azure Germany](./germany-welcome.md)
* [Blog Azure Germany](/archive/blogs/azuregermany/)
* [Conformité d'Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)