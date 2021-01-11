---
title: Haute disponibilité et récupération d’urgence
description: Apprenez à concevoir votre application Azure Batch afin de faire face à une panne régionale.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831004"
---
# <a name="design-your-batch-application-for-high-availability"></a>Concevoir votre application Batch à des fins de haute disponibilité

Azure Batch est disponible dans toutes les régions Azure, mais lorsqu'un compte Batch est créé, il doit être associé à une région spécifique. Toutes les opérations associées au compte Batch s'appliquent alors à cette région. Par exemple, les pools et les machines virtuelles associées à ceux-ci sont créés dans la même région que le compte Batch.

Lorsque vous concevez une application qui utilise Batch, vous devez prendre en compte la possibilité que Batch ne soit pas disponible dans une région. Même si ce cas de figure est rare, il peut arriver qu'un problème touche la région toute entière ou l'ensemble du service Batch de celle-ci, ou votre compte Batch spécifique.

Si l'application ou la solution qui utilise Batch doit toujours être disponible, elle doit être conçue de manière à pouvoir basculer vers une autre région ou à répartir la charge de travail entre deux ou plusieurs régions. Les deux approches nécessitent au moins deux comptes Batch situés dans des régions distinctes.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Plusieurs comptes Batch dans plusieurs régions

L'utilisation de plusieurs comptes Batch situés dans des régions distinctes permet à votre application de continuer à s'exécuter si un compte Batch d'une région devient indisponible. Si votre application doit être hautement disponible, il est particulièrement important d’avoir plusieurs comptes.

Dans certains cas, les applications peuvent être conçues pour utiliser intentionnellement deux régions ou plus. Par exemple, si vous avez besoin d'une très grande capacité, l'utilisation de plusieurs régions peut être nécessaire pour gérer une application à grande échelle ou pour faire face à une future croissance. Ces applications nécessitent également plusieurs comptes Batch (un par région utilisée).

## <a name="design-considerations-for-providing-failover"></a>Considérations de conception pour le basculement

Pour basculer une solution vers une autre région, tous ses composants doivent être pris en compte ; un deuxième compte Batch ne suffit pas. Par exemple, dans la plupart des applications Batch, un compte de stockage Azure est requis ; et pour obtenir des performances acceptables, le compte de stockage et le compte Batch doivent se trouver dans la même région.

Lors de la conception d'une solution capable de basculer, tenez compte des points suivants :

- Créez au préalable tous les comptes requis dans les différentes régions, comme le compte Batch et le compte de stockage. Il n’y a souvent aucun frais pour la création de comptes, et les frais s’accumulent uniquement lorsque le compte est utilisé ou lorsque des données sont stockées.
- Veillez à ce que des [quotas](batch-quota-limit.md) appropriés soient définis à l’avance sur tous les comptes afin de pouvoir allouer le nombre requis de cœurs à l’aide du compte Batch.
- Utilisez des modèles et/ou des scripts pour automatiser le déploiement de l'application dans une région.
- Maintenez à jour les données de référence et les fichiers binaires de l'application dans toutes les régions. Une région à jour pourra être mise en ligne rapidement sans avoir à attendre le téléchargement et le déploiement des fichiers. Par exemple, si une application personnalisée à installer sur des nœuds de pool est stockée et référencée à l'aide de packages d'application Batch, lorsqu'une nouvelle version de l'application est produite, elle doit être téléchargée sur chaque compte Batch et référencée par la configuration du pool (ou la nouvelle version peut devenir la version par défaut).
- Dans l’application qui appelle Batch, Stockage ou tout autre service, facilitez le basculement vers les clients ou la charge vers différentes régions.
- Envisagez fréquemment de basculer vers une autre région dans le cadre d’un fonctionnement normal. Par exemple, avec deux déploiements dans des régions distinctes, basculez une fois par mois vers l’autre région.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la création de comptes Batch avec le [portail Azure](batch-account-create-portal.md), l’interface [Azure CLI](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md) ou l’[API Gestion du service Batch](batch-management-dotnet.md).
- Découvrez en plus sur les [quotas par défaut associés à un compte Batch](batch-quota-limit.md) et sur la manière dont les quotas peuvent être augmentés.
