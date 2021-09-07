---
title: Niveaux tarifaires - Azure Database pour MySQL
description: Découvrez les différents niveaux tarifaires d’Azure Database pour MySQL, ce qui englobe les générations de calcul, les types de stockage, la taille de stockage, les vCores, la mémoire et les périodes de conservation des sauvegardes.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: c9e726e53d40ac90aec6bbbaaf41399698b11209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525174"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Niveaux tarifaires Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Vous pouvez créer un serveur Azure Database pour MySQL dans un des trois différents niveaux tarifaires : De base, Usage général et À mémoire optimisée. Les niveaux tarifaires diffèrent par la quantité de calcul dans vCores qui peut être configurée, la mémoire par vCore et la technologie de stockage utilisée pour stocker les données. Toutes les ressources sont approvisionnées au niveau du serveur MySQL. Un serveur peut avoir une ou plusieurs bases de données.

| Attribut   | **De base** | **Usage général** | **Mémoire optimisée** |
|:---|:----------|:--------------------|:---------------------|
| Génération de calcul | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Mémoire par vCore | 2 Go | 5 Go | 10 Go |
| Taille de stockage | 5 Go à 1 To | 5 Go à 16 To | 5 Go à 16 To |
| Période de rétention de sauvegarde de bases de données | 7 à 35 jours | 7 à 35 jours | 7 à 35 jours |

Pour choisir un niveau tarifaire, utilisez le tableau suivant comme point de départ.

| Niveau tarifaire | Charges de travail cibles |
|:-------------|:-----------------|
| De base | Charges de travail qui nécessitent des performances légères en termes de calcul et d’E/S. Il s’agit, par exemple, de serveurs utilisés pour le développement ou le test, ou pour des applications à petite échelle rarement utilisées. |
| Usage général | La plupart des charges de travail professionnelles qui nécessitent une capacité de calcul et de mémoire équilibrée avec un débit d’E/S extensible. Il s’agit, par exemple, de serveurs destinés à l’hébergement d’applications web et mobiles, ainsi que d’autres applications d’entreprise.|
| Mémoire optimisée | Charges de travail de base de données haute performance qui nécessitent des performances en mémoire suffisantes pour un traitement plus rapide des transactions et une simultanéité plus élevée. Il s’agit, par exemple, de serveurs destinés au traitement de données en temps réel et à des applications transactionnelles ou analytiques haute performance.|

Après avoir créé un serveur, le nombre de vCores, la génération du matériel et le niveau tarifaire peuvent être augmentés ou diminués (excepté à vers et à partir de la version De base) en quelques secondes. Vous pouvez également augmenter ou diminuer de manière indépendante la quantité de stockage et la période de rétention des sauvegardes sans interruption de l’application. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Pour plus d’informations, consultez la section [Ressources de mise à l’échelle](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Générations de calcul et vCores

Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. Les régions Chine Est 1, Chine Nord 1, US DoD Centre et US DoD Est utilisent des processeurs logiques Gen 4 basés sur des processeurs Intel E5-2673 v3 (Haswell) à 2,4 GHz. Toutes les autres régions utilisent des processeurs logiques Gen 5 basés sur des processeurs Intel E5-2673 v4 (Broadwell) à 2,3 GHz.

## <a name="storage"></a>Stockage

Le stockage que vous approvisionnez est la quantité de stockage disponible pour votre serveur Azure Database pour MySQL. Le stockage est utilisé pour les fichiers de base de données, les fichiers temporaires, les journaux d’activité de transaction, et les journaux d’activité du serveur MySQL. La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur votre serveur.

Azure Database pour MySQL : un serveur unique prend en charge le stockage principal suivant pour les serveurs. 

| Type de stockage   | De base | Usage général v1 | Usage général v2 |
|:---|:----------|:--------------------|:---------------------|
| Taille de stockage | 5 Go à 1 To | 5 Go à 4 To | 5 Go à 16 To |
| Taille d’incrément de stockage | 1 Go | 1 Go | 1 Go |
| E/S par seconde | Variable |3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 6000 E/S par seconde | 3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 20 000 IOPS |

>[!NOTE]
> Le niveau De base n’offre pas de garantie d’E/S par seconde. Dans le stockage Usage général, les IOPS sont mises à l’échelle avec la taille de stockage approvisionnée selon un ratio de 3:1.

### <a name="basic-storage"></a>Stockage de base 
Le stockage De base est le stockage back-end qui prend en charge les serveurs de niveau tarifaire De base. Le stockage De base tire parti du stockage Azure standard dans le serveur principal où les IOPS approvisionnées ne sont pas garanties et la latence est variable. Le niveau De base est idéal pour les charges de travail qui nécessitent un calcul léger, un faible coût et des performances d’E/S pour le développement ou des applications rarement utilisées à petite échelle.

### <a name="general-purpose-storage"></a>Stockage à usage général 
Le stockage à usage général est le stockage back-end qui prend en charge les serveurs de niveau Usage général et Mémoire optimisée. Dans le stockage à usage général, les E/S par seconde sont mises à l’échelle avec la taille de stockage approvisionnée selon un ratio de 3:1. Il existe deux générations de stockage à usage général, comme décrit ci-dessous :

#### <a name="general-purpose-storage-v1-supports-up-to-4-tb"></a>Stockage à usage général v1 (prend en charge jusqu’à 4 To)
Le stockage à usage général v1 est basé sur la technologie de stockage héritée, qui peut prendre en charge jusqu’à 4 To de stockage et 6 000 E/S par seconde par serveur. Le stockage à usage général v1 est optimisé pour tirer parti de la mémoire des nœuds de calcul exécutant le moteur MySQL pour la mise en cache locale et les sauvegardes. Le processus de sauvegarde sur le stockage à usage général v1 lit les données et les fichiers journaux dans la mémoire des nœuds de calcul et les copie dans le stockage de sauvegarde cible pour une rétention allant jusqu’à 35 jours. Par conséquent, la consommation de mémoire et d’E/S de stockage pendant les sauvegardes est relativement supérieure. 

Toutes les régions Azure prennent en charge le stockage à usage général v1

Pour le stockage à usage général ou un serveur à mémoire optimisée sur le stockage à usage général v1, nous vous recommandons :

*   Planifier la comptabilisation des niveaux SKU de calcul pour une mémoire en excès de 10-30 % pour la mise en cache du stockage et les tampons de sauvegarde 
*   Approvisionner des E/S par seconde 10% supérieures à ce qui est requis par la charge de travail de base de données pour tenir compte des E/S de sauvegarde 
*   Une migration vers un stockage à usage général v2 décrit ci-dessous qui prend en charge jusqu’à 16 To de stockage si l’infrastructure de stockage sous-jacente est disponible dans vos régions Azure favorites partagées ci-dessous. 

#### <a name="general-purpose-storage-v2-supports-up-to-16-tb-storage"></a>Stockage à usage général v2 (prend en charge jusqu’à 16 To de stockage)
Le stockage à usage général v2 est basé sur l’infrastructure de stockage la plus récente, qui peut prendre en charge jusqu’à 16 To et 20 000 E/S par seconde. Dans un sous-ensemble de régions Azure où l’infrastructure est disponible, tous les serveurs récemment approvisionnés sont sur le stockage à usage général v2 par défaut. Le stockage à usage général v2 ne consomme pas de mémoire à partir du nœud de calcul de MySQL et fournit de meilleures latences d’E/S prévisibles par rapport au stockage v1 à usage général. Les sauvegardes sur les serveurs de stockage v2 à usage général sont basées sur des instantanés, sans surcharge d’E/S supplémentaire. Sur le stockage v2 à usage général, les performances du serveur MySQL sont supposées plus élevées par rapport au stockage à usage général v1 pour les mêmes stockage et E/S par seconde approvisionnés. Il n’existe aucun coût supplémentaire pour le stockage à usage général qui prend en charge jusqu’à 16 To de stockage. Pour obtenir de l’aide sur la migration vers un stockage de 16 To, veuillez ouvrir un ticket de support à partir du portail Azure.

Le stockage à usage général v2 est pris en charge dans les régions Azure suivantes : 

| Région | Disponibilité du stockage à usage général v2 | 
| --- | --- | 
| Australie Est | :heavy_check_mark: | 
| Sud-Est de l’Australie | :heavy_check_mark: | 
| Brésil Sud | :heavy_check_mark: | 
| Centre du Canada | :heavy_check_mark: |
| Est du Canada | :heavy_check_mark: |
| USA Centre | :heavy_check_mark: | 
| USA Est | :heavy_check_mark: | 
| USA Est 2 | :heavy_check_mark: |
| Asie Est | :heavy_check_mark: | 
| Japon Est | :heavy_check_mark: | 
| OuJapon Est | :heavy_check_mark: | 
| Centre de la Corée | :heavy_check_mark: |
| Corée du Sud | :heavy_check_mark: |
| Europe Nord | :heavy_check_mark: | 
| Centre-Nord des États-Unis | :heavy_check_mark: | 
| États-Unis - partie centrale méridionale | :heavy_check_mark: | 
| Asie Sud-Est | :heavy_check_mark: | 
| Sud du Royaume-Uni | :heavy_check_mark: | 
| Ouest du Royaume-Uni | :heavy_check_mark: | 
| Centre-USA Ouest | :heavy_check_mark: | 
| USA Ouest | :heavy_check_mark: | 
| USA Ouest 2 | :heavy_check_mark: | 
| Europe Ouest | :heavy_check_mark: | 
| Inde Centre* | :heavy_check_mark: | 
| France Centre* | :heavy_check_mark: | 
| Émirats arabes unis Nord* | :heavy_check_mark: | 
| Afrique du Sud Nord* | :heavy_check_mark: |

> [!Note] 
> *Régions où Azure Database pour MySQL dispose d’un stockage à usage général V2 en préversion publique <br /> *Pour ces régions Azure, vous aurez la possibilité de créer un serveur à la fois dans le stockage à usage général v1 et v2. Pour les serveurs créés avec le stockage à usage général v2 dans la version préliminaire publique, les limites suivantes sont respectées : <br /> 
> * La sauvegarde géo-redondante ne sera pas prise en charge.<br /> 
> * Le serveur de réplica doit se trouver dans les régions qui prennent en charge le stockage à usage général v2. <br /> 
    

### <a name="how-can-i-determine-which-storage-type-my-server-is-running-on"></a>Comment puis-je déterminer le type de stockage sur lequel mon serveur s’exécute ?

Vous pouvez trouver le type de stockage de votre serveur en cliquant sur le panneau Niveau tarifaire dans le portail. 
* Si le serveur est configuré à l’aide du SKU de base, le type de stockage est stockage de base.
* Si le serveur est approvisionné à l’aide du stockage à usage général ou d’un SKU à mémoire optimisée, le type de stockage est stockage à usage général.
   *  Si le stockage maximal pouvant être approvisionné sur votre serveur va jusqu’à 4 To, le type de stockage est stockage à usage général v1.
   *  Si le stockage maximal pouvant être approvisionné sur votre serveur va jusqu’à 16 To, le type de stockage est stockage à usage général v2.

### <a name="can-i-move-from-general-purpose-storage-v1-to-general-purpose-storage-v2-if-yes-how-and-is-there-any-additional-cost"></a>Puis-je passer du stockage à usage général v1 au stockage à usage général v2 ? Si oui, comment et y a-t-il des frais supplémentaires ?
Oui, la migration vers le stockage à usage général v2 à partir de v1 est prise en charge si l’infrastructure de stockage sous-jacente est disponible dans la région Azure du serveur source. La migration et le stockage v2 sont disponibles sans frais supplémentaires.

### <a name="can-i-grow-storage-size-after-server-is-provisioned"></a>Puis-je augmenter la taille de stockage une fois que le serveur est approvisionné ?
Vous pouvez ajouter de la capacité de stockage supplémentaire pendant et après la création du serveur et autoriser le système à faire évoluer le stockage automatiquement en fonction de la consommation de votre charge de travail. 

>[!IMPORTANT]
> Le stockage peut seulement monter en puissance.

### <a name="monitoring-io-consumption"></a>Surveillance de la consommation d’E/S
Vous pouvez surveiller votre consommation d’E/S dans le Portail Azure ou à l’aide des commandes Azure CLI. Les métriques pertinentes à surveiller sont [limite de stockage, pourcentage de stockage, stockage utilisé et pourcentage d’E/S](concepts-monitoring.md). Les métriques de surveillance du serveur MySQL avec stockage à usage général v1 signalent la mémoire et les e/s consommées par le moteur MySQL, mais peuvent ne pas capturer la mémoire et la consommation d’e/s de la couche de stockage qui est une limitation.

### <a name="reaching-the-storage-limit"></a>Atteindre la limite de stockage

Les serveurs avec moins de 100 Go de stockage approvisionnés sont marqués en lecture seule si l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionnée. Les serveurs avec plus de 100 Go de stockage approvisionnés sont marqués en lecture seule lorsque l’espace de stockage libre est inférieur à 5 Go.

Par exemple, si vous avez approvisionné 110 Go de stockage et que l’utilisation réelle dépasse 105 Go, le serveur est marqué en lecture seule. Sinon, si vous avez provisionné 5 Go de stockage, le serveur est marqué en lecture seule quand le stockage disponible est inférieur à 256 Mo.

Pendant que le service tente de marquer le serveur en lecture seule, toutes les nouvelles demandes de transactions d’écriture sont bloquées et les transactions actives existantes continuent de s’exécuter. Une fois que le serveur est marqué en lecture seule, toutes les opérations d’écriture et validations de transaction ultérieures échouent. Les requêtes de lecture continueront de fonctionner sans interruption. Après avoir augmenté le stockage provisionné, le serveur sera prêt à accepter de nouvelles transactions d’écriture.

Nous vous recommandons d’activer la croissance automatique du stockage ou de configurer une alerte pour vous avertir lorsque votre serveur de stockage est proche du seuil, afin d’éviter la mise en lecture seule. Pour plus d’informations, consultez la documentation sur [comment configurer une alerte](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Croissance automatique du stockage

La croissance automatique du stockage permet à votre serveur de disposer en permanence d’un espace de stockage suffisant et de ne pas passer en lecture seule. Si la croissance automatique du stockage est activée, le stockage évolue automatiquement sans affecter la charge de travail. Pour les serveurs avec moins de 100 Go de stockage approvisionnés, la taille de stockage approvisionnée augmente de 5 Go lorsque l’espace de stockage libre est inférieur à 10 % de la taille de stockage approvisionnée. Pour les serveurs avec plus de 100 Go de stockage approvisionnés, la taille de stockage approvisionnée augmente de 5 % lorsque l’espace de stockage libre est inférieur à 10 Go de taille de stockage approvisionnée. Les limites de stockage maximales indiquées ci-dessus s’appliquent.

Par exemple, si vous avez approvisionné 1000 Go de stockage et que l’utilisation réelle dépasse 990 Go, la taille de stockage du serveur passe à 1050 Go. Sinon, si vous avez configuré 10 Go de stockage, la taille de stockage passe à 15 Go lorsque moins de 1 Go de stockage est libre.

N’oubliez pas que le stockage peut seulement monter en puissance.

## <a name="backup-storage"></a>Stockage de sauvegarde 

Azure Database pour MySQL fournit jusqu’à 100 % du stockage de votre serveur approvisionné en stockage de sauvegarde sans coût supplémentaire. Le stockage de sauvegarde que vous utilisez au-delà de cette quantité est facturé en Go par mois. Par exemple, si vous configurez un serveur avec 250 Go de stockage, vous disposez de 250 Go de stockage supplémentaire pour les sauvegardes de serveur sans frais. Le stockage pour les sauvegardes dépassant 250 Go est facturé conformément au [modèle de tarification](https://azure.microsoft.com/pricing/details/mysql/). Pour comprendre les facteurs influençant l’utilisation du stockage de sauvegarde, la surveillance et le contrôle du coût de stockage de sauvegarde, vous pouvez consulter la [documentation relative à la sauvegarde](concepts-backup.md).

## <a name="scale-resources"></a>Mettre les ressources à l’échelle

Après avoir créé votre serveur, vous pouvez modifier de manière indépendante les vCores, la génération du matériel, le niveau tarifaire (excepté à partir de la version De base), la quantité de stockage et la période de rétention de sauvegarde. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Le nombre de vCores peut être augmenté ou diminué. La période de rétention de sauvegarde peut être augmentée ou diminuée et va de 7 à 35 jours. La taille de stockage ne peut être qu’augmentée. La mise à l’échelle des ressources peut être effectuée par le biais du portail ou d’Azure CLI. Pour obtenir un exemple de mise à l’échelle à l’aide d’Azure CLI, consultez [Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL à l’aide d’Azure CLI](scripts/sample-scale-server.md).

Lorsque vous modifiez le nombre de vCores, la génération du matériel ou le niveau tarifaire, une copie du serveur d’origine est créée avec la nouvelle allocation du calcul. Une fois que le nouveau serveur est opérationnel, les connexions sont basculées vers le nouveau serveur. Pendant le moment durant lequel le système bascule vers le nouveau serveur, aucune nouvelle connexion ne peut être établie, et toutes les transactions non validées sont restaurées. Ce temps d’arrêt pendant la mise à l’échelle peut durer environ entre 60 et 120 secondes. Le temps d’arrêt pendant la mise à l’échelle dépend du temps de récupération de la base de données, ce qui peut entraîner un temps de mise en ligne de la base de données plus long si vous avez des activités transactionnelles importantes sur le serveur au moment de la mise à l’échelle. Pour éviter un temps de redémarrage plus long, il est recommandé d’effectuer les opérations de mise à l’échelle pendant des périodes de faible activité transactionnelle sur le serveur.

La mise à l’échelle du stockage et la modification de la période de rétention de sauvegarde sont des opérations en ligne. Aucune interruption de service n’a lieu et votre application n’est pas affectée. Comme les E/S par seconde augmentent avec la taille du stockage approvisionné, vous pouvez augmenter le nombre de E/S par seconde disponibles pour votre serveur en mettant à l’échelle l’espace de stockage.

## <a name="pricing"></a>Tarifs

Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/mysql/). Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) affiche le coût mensuel dans l’onglet **Niveau tarifaire** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour MySQL**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Créer un serveur MySQL dans le portail](howto-create-manage-server-portal.md).
- En savoir plus sur les [limites de service](concepts-limits.md).
- Apprendre à [effectuer un scale-out avec des réplicas en lecture](howto-read-replicas-portal.md).
