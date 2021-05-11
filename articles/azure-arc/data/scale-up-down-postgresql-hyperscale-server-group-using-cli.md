---
title: Mettre à l’échelle un groupe de serveurs Azure Database pour PostgreSQL Hyperscale à l’aide de l’interface de ligne de commande (azdata ou kubectl)
description: Mettre à l’échelle un groupe de serveurs Azure Database pour PostgreSQL Hyperscale à l’aide de l’interface de ligne de commande (azdata ou kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: efad54e9e6d69f4b1b9c8cef0d93e0286c6c8203
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286801"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Mettre à l’échelle un groupe de serveurs Azure Database pour PostgreSQL Hyperscale à l’aide de l’interface de ligne de commande (azdata ou kubectl)



Il est parfois nécessaire de modifier les caractéristiques ou la définition d’un groupe de serveurs. Exemple :

- Mettre à l’échelle le nombre de vCores que chaque coordinateur ou nœud Worker utilise
- Mettre à l’échelle la mémoire que chaque nœud coordinateur ou Worker utilise

Ce guide explique comment mettre à l’échelle vCore et/ou la mémoire.

La mise à l’échelle des paramètres de vCore ou de mémoire de votre groupe de serveurs signifie que vous avez la possibilité de définir une valeur minimale et/ou maximale pour chacun des paramètres de vCore et de mémoire. Si vous souhaitez configurer votre groupe de serveurs pour utiliser un nombre particulier de vCores ou une quantité spécifique de mémoire, vous devez définir la même valeur pour les paramètres minimaux et les paramètres maximaux.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Afficher la définition actuelle du groupe de serveurs

Pour afficher la définition actuelle de votre groupe de serveurs et voir les paramètres de vCore et de mémoire actuels, exécutez l’une des commandes suivantes :

### <a name="cli-with-azdata"></a>Interface de ligne de commande avec azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>Interface de ligne de commande avec kubectl

```console
kubectl describe postgresql/<server group name> -n <namespace name>
```

Celle-ci retourne la configuration de votre groupe de serveurs. Si vous avez créé le groupe de serveurs avec les paramètres par défaut, vous devez voir la définition suivante :

```json
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
...
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpréter la définition du groupe de serveurs

Dans la définition d’un groupe de serveurs, la section contenant les paramètres du nombre minimal ou maximal de vCores par nœud et de mémoire minimale ou maximale par nœud est la section **« planification »** . Dans cette section, les paramètres maximaux sont conservés dans une sous-section appelée **« limites »** , et les paramètres minimaux dans la sous-section intitulée **« requêtes »** .

Si vous définissez des paramètres minimaux différents des paramètres maximaux, la configuration garantit que les ressources demandées sont allouées au groupe de serveurs si nécessaire. Les limites que vous définissez ne seront pas dépassées.

Les ressources (vCores et mémoire) qui seront réellement utilisées par votre groupe de serveurs ne dépassent pas les paramètres maximaux et dépendent des charges de travail et des ressources disponibles sur le cluster. Si ne limitez pas les paramètres à une valeur max, votre groupe de serveurs peut utiliser toutes les ressources que le cluster Kubernetes alloue aux nœuds Kubernetes sur lesquels votre groupe de serveurs est planifié.

Ces paramètres de vCore et de mémoire s’appliquent à chaque rôle des instances Postgres constituant le groupe de serveurs PostgreSQL Hyperscale : le coordinateur et les Workers. Vous pouvez définir des requêtes et des limites par rôle. Vous pouvez définir des paramètres de requêtes et de limites qui sont différents pour chaque rôle. Ils peuvent également être similaires, en fonction de vos besoins.

Dans une configuration par défaut, seule la mémoire minimale est définie sur 256Mi, car il s’agit de la quantité minimale de mémoire recommandée pour exécuter PostgreSQL Hyperscale.

> [!NOTE]
> Le fait de définir un minimum ne signifie pas que le groupe de serveurs utilisera nécessairement ce minimum. Cela signifie que si le groupe de serveurs en a besoin, il est garanti qu’au moins ce minimum est alloué. Supposons, par exemple, que nous définissions `--minCpu 2`. Cela ne signifie pas que le groupe de serveurs utilisera au moins 2 vCores à tout moment. Au lieu de cela, le groupe de serveurs peut commencer à utiliser moins de 2 vCores s’il n’en a pas besoin, avec la garantie de se voir alloué au moins 2 vCores s’il en a besoin plus tard. Cela implique que le cluster Kubernetes alloue des ressources à d’autres charges de travail de façon à pouvoir allouer 2 vCores au groupe de serveurs s’il en a besoin. De plus, effectuer un scale-up et un scale-down n’est pas une opération en ligne, car un redémarrage des pods kubernetes est nécessaire.

>[!NOTE]
>Avant de modifier la configuration de votre système, veillez à vous familiariser avec le modèle de ressource Kubernetes [ici](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-and-down-the-server-group"></a>Effectuer un scale-up et un scale-down sur le groupe de serveurs

Procéder à un scale-up revient à augmenter les valeurs des paramètres de vCore et/ou de mémoire de votre groupe de serveurs.
Procéder à un scale-down revient à diminuer les valeurs des paramètres de vCore et/ou de mémoire de votre groupe de serveurs.

Les paramètres que vous allez définir doivent être pris en compte dans la configuration que vous définissez pour votre cluster Kubernetes. Veillez à ne pas définir de valeurs que votre cluster Kubernetes ne sera pas en mesure de satisfaire. Des erreurs ou un comportement imprévisible, comme l’indisponibilité de l’instance de la base de données, peuvent en découler. Par exemple, si l’état de votre groupe de serveurs reste défini sur _mise à jour_ pendant un temps prolongé après la modification de la configuration, cela peut indiquer que vous définissez les paramètres ci-dessous sur des valeurs que votre cluster Kubernetes ne peut pas satisfaire. Si tel est le cas, annulez la modification ou lisez _troubleshooting_section.

Quels paramètres devez-vous définir ?
- Pour définir le nombre minimal de vCores, définissez `--cores-request`.
- Pour définir le nombre maximal de vCores, définissez `--cores-limit`.
- Pour définir la mémoire minimale, définissez `--memory-request`.
- Pour définir la mémoire maximale, définissez `--memory-limit`.

Comment indiquer le rôle auquel le paramètre s’applique ?
- Pour configurer le paramètre du rôle coordinateur, spécifiez `coordinator=<value>`.
- Pour configurer le paramètre du rôle Worker (le paramètre spécifié est défini sur la même valeur pour tous les Workers), spécifiez `worker=<value>`.


> [!CAUTION]
> Avec Kubernetes, la configuration d’un paramètre de limite sans configurer le paramètre de requête correspondant force la valeur de la requête à être de la même valeur que la limite. Une indisponibilité de votre groupe de serveurs risque d’en découler, car ses pods peuvent ne pas être replanifiés si aucun nœud Kubernetes n’est disponible avec suffisamment de ressources. Ainsi, pour éviter cette situation, les exemples ci-dessous montrent comment définir ces deux paramètres, de requête et de limite.


**La syntaxe générale est la suivante :**

```console
azdata arc postgres server edit -n <servergroup name> --memory-limit/memory-request/cores-request/cores-limit <coordinator=val1,worker=val2>
```

La valeur que vous indiquez pour le paramètre de mémoire est un nombre suivi d’une unité de volume. Par exemple, pour indiquer 1 Go, vous devez indiquer 1024 Mi ou 1 Gi.
Pour indiquer un nombre de cœurs, vous devez simplement transmettre un nombre sans unité. 

### <a name="examples-using-the-azdata-cli"></a>Exemples utilisant l’interface CLI azdata





**Configurez le rôle de coordinateur pour qu’il ne dépasse pas 2 cœurs, et le rôle de Worker pour ne pas dépasser 4 cœurs :**
```console
 azdata arc postgres server edit -n postgres01 --cores-request coordinator=1, --cores-limit coordinator=2
 azdata arc postgres server edit -n postgres01 --cores-request worker=1, --cores-limit worker=4
```

ou
```console
azdata arc postgres server edit -n postgres01 --cores-request coordinator=1,worker=1 --cores-limit coordinator=4,worker=4
```

> [!NOTE]
> Pour plus d’informations sur ces paramètres, exécutez `azdata arc postgres server edit --help`.

### <a name="example-using-kubernetes-native-tools-like-kubectl"></a>Exemple utilisant des outils natifs Kubernetes comme `kubectl`

Exécutez la commande suivante : 
```console
kubectl edit postgresql/<servergroup name> -n <namespace name>
```

Vous accédez à l’éditeur `vi` dans lequel vous pouvez naviguer et modifier la configuration. Utilisez la commande suivante pour mapper le paramètre souhaité au nom du champ dans la spécification :

> [!CAUTION]
> Voici un exemple fourni pour illustrer la modification de la configuration. Avant de mettre à jour la configuration, veillez à définir les paramètres sur des valeurs que le cluster Kubernetes peut honorer.

Par exemple, si vous souhaitez définir les paramètres suivants pour les rôles de coordinateur et de Worker sur les valeurs suivantes :
- Nombre minimal de vCores = `2` 
- Nombre maximal de vCores = `4`
- Mémoire minimale = `512Mb`
- Mémoire maximale = `1Gb` 

Vous devez configurer la définition de votre groupe de serveurs, afin qu’elle corresponde à la configuration ci-dessous :

```json
  scheduling:
    default:
      resources:
        requests:
          memory: 256Mi
    roles:
      coordinator:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 512Mi
      worker:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 512Mi
```

Si vous ne connaissez pas l’éditeur `vi`, consultez la description des commandes dont vous pouvez avoir besoin [ici](https://www.computerhope.com/unix/uvi.htm) :
- Mode d’édition : `i`
- Se déplacer avec les flèches
- Fin de l’édition : `esc`
- Quitter sans enregistrer : `:qa!`
- Quitter après l’enregistrement : `:qw!`


## <a name="reset-to-default-values"></a>Réinitialiser les valeurs par défaut
Pour réinitialiser les valeurs par défaut des paramètres limites/demandes de cœur/mémoire, modifiez-les et transmettez une chaîne au lieu d’une valeur réelle. Par exemple, si vous souhaitez réinitialiser le paramètre de limite de cœurs, exécutez les commandes suivantes :

```console
azdata arc postgres server edit -n postgres01 --cores-request coordinator='',worker=''
azdata arc postgres server edit -n postgres01 --cores-limit coordinator='',worker=''
```

ou 
```console
azdata arc postgres server edit -n postgres01 --cores-request coordinator='',worker='' --cores-limit coordinator='',worker=''
```

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer un scale-out de votre groupe de serveurs Azure Database pour PostgreSQL Hyperscale](scale-out-postgresql-hyperscale-server-group.md)
- [Configuration de stockage et concepts de stockage Kubernetes](storage-configuration.md)
- [Modèle de ressources Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
