---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772967"
---
Si vous rencontrez des problèmes en exécutant des opérations sur les galeries d’images partagées, les définitions d’image et les versions d’image, réexécutez la commande ayant échoué en mode débogage. Le mode débogage est activé avec le commutateur **-debug** dans l’interface CLI et le commutateur **-Debug** dans PowerShell. Une fois que vous avez localisé l’erreur, suivez ce document pour corriger les erreurs.


## <a name="unable-to-create-a-shared-image-gallery"></a>Impossible de créer une galerie d’images partagées

Causes possibles :

*Le nom de la galerie n’est pas valide.*

Les caractères autorisés pour le nom de galerie sont les lettres majuscules ou minuscules, les chiffres et les points. Le nom de galerie ne peut pas contenir de tirets. Changez le nom de galerie, puis réessayez. 

*Le nom de galerie n’est pas unique dans votre abonnement.*

Choisissez un autre nom de galerie, puis réessayez.


## <a name="unable-to-create-an-image-definition"></a>Impossible de créer une définition d’image 

Causes possibles :

*Le nom de définition d’image n’est pas valide.*

Les caractères autorisés pour la définition d’image sont les lettres majuscules ou minuscules, les chiffres, les tirets et les points. Changez le nom de définition d’image, puis réessayez.

*Les propriétés obligatoires pour créer une définition d’image ne sont pas remplies.*

Les propriétés comme le nom, l’éditeur, l’offre, la référence et le type de système d’exploitation sont obligatoires. Vérifiez si toutes les propriétés sont renseignées.

Vérifiez que la valeur **OSType**, Linux ou Windows, de la définition d’image est la même que celle de l’image managée source que vous utilisez pour créer la version d’image. 


## <a name="unable-to-create-an-image-version"></a>Impossible de créer une version d’image 

Causes possibles :

*Le nom de version d’image n’est pas valide.*

Les caractères autorisés pour la version d’image sont les nombres et les points. Les nombres doivent être un entier 32 bits. Format: *MajorVersion.MinorVersion.Patch*. Changez le nom de version d’image, puis réessayez.

*L’image managée source à partir de laquelle la version d’image est créée est introuvable.* 

Vérifiez si l’image source existe et qu’elle est dans la même région que celle de la version d’image.

*L’image managée n’est pas encore provisionnée.*

Vérifiez que l’état de provisionnement de l’image managée source est **Réussi**.

*La liste des régions cibles ne doit pas inclure la région source.*

La liste des régions cibles doit inclure la région source de la version d’image. Vérifiez que vous avez inclus la région source dans la liste des régions cibles où vous voulez qu’Azure réplique votre version d’image.

*La réplication de toutes les régions cibles n’est pas effectuée.*

Utilisez l’indicateur **--expand ReplicationStatus** pour vérifier si la réplication sur toutes les régions cibles spécifiées a été effectuée. Si ce n’est pas le cas, attendez 6 heures maximum jusqu’à la fin de la tâche. En cas d’échec, réexécutez la commande pour créer et répliquer la version d’image. Si la version d’image doit être répliquée sur un grand nombre de régions cibles, effectuez la réplication par phases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Impossible de créer une machine virtuelle ou un groupe identique 

Causes possibles :

*L’utilisateur qui tente de créer une machine virtuelle ou un groupe de machines virtuelles identiques n’a pas d’accès en lecture à la version d’image.*

Contactez le propriétaire de l’abonnement et demandez-lui d’accorder l’accès en lecture à la version d’image ou aux ressources parentes (comme la galerie d’images partagées ou la définition d’image) à travers le [contrôle d'accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Version d’image introuvable.*

Vérifiez que la région dans laquelle vous essayez de créer une machine virtuelle ou un groupe de machines virtuelles identiques est incluse dans la liste des régions cibles de la version d’image. Si la région est déjà dans la liste des régions cibles, vérifiez si le travail de réplication a été effectué. Vous pouvez utiliser l’indicateur **--ReplicationStatus** pour vérifier si la réplication sur toutes les régions cibles spécifiées a été effectuée. 

*La création de la machine virtuelle ou du groupe de machines virtuelles identiques prend beaucoup de temps.*

Vérifiez que la valeur **OSType** de la version d’image à partir de laquelle vous tentez de créer la machine virtuelle ou le groupe de machines virtuelles identiques est identique à celle de l’image managée source que vous avez utilisée pour créer la version d’image **.** 

## <a name="unable-to-share-resources"></a>Impossible de partager les ressources

Le partage des ressources de galerie d’images partagées, de définition d’image et de version d’image entre abonnements est activé à l’aide du [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>La réplication est lente

Utilisez l’indicateur **--expand ReplicationStatus** pour vérifier si la réplication sur toutes les régions cibles spécifiées a été effectuée. Si ce n’est pas le cas, attendez 6 heures maximum jusqu’à la fin de la tâche. En cas d’échec, redéclenchez la commande pour créer et répliquer la version d’image. Si la version d’image doit être répliquée sur un grand nombre de régions cibles, effectuez la réplication par phases.

## <a name="azure-limits-and-quotas"></a>Limites et quotas Azure 

[Les limites et quotas Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) s’appliquent à toutes les ressources de galerie d’images partagées, définition d’image et version d’image. Vérifiez que vous êtes dans les limites de vos abonnements. 



