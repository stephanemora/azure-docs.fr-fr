---
ms.assetid: ''
title: Suppression réversible d’Azure Key Vault | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/19/2019
ms.openlocfilehash: f222b37e8ca6efcfe28146ee948511d887f547a4
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339140"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Vue d’ensemble de la suppression réversible d’Azure Key Vault

La fonctionnalité de suppression réversible de Key Vault permet la récupération des coffres et des objets de coffres supprimés. Nous aborderons en particulier les scénarios suivants :

- Prise en charge de la suppression récupérable d’un coffre de clés
- Prise en charge de la suppression récupérable d’objets Key Vault (par exemple, clés, secrets et certificats)

## <a name="supporting-interfaces"></a>Prise en charge des interfaces

La fonctionnalité de suppression réversible est initialement disponible via le [REST](/rest/api/keyvault/), [CLI](key-vault-soft-delete-cli.md), [PowerShell](key-vault-soft-delete-powershell.md) et [.NET /C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) interfaces.

## <a name="scenarios"></a>Scénarios

Les coffres Azure Key Vault désignent des ressources suivies, gérées par Azure Resource Manager. Azure Resource Manager spécifie également un comportement bien défini pour la suppression, qui suppose qu’une opération DELETE correctement exécutée sur une ressource ne permet plus d’accéder à cette ressource. La fonctionnalité de suppression réversible permet la récupération de l’objet supprimé en cas de suppression accidentelle ou intentionnelle.

1. Dans un scénario type, un utilisateur peut avoir supprimé par mégarde un coffre Key Vault ou un objet Key Vault ; si la restauration du coffre ou de l’objet en question a été programmée pendant une période prédéterminée, l’utilisateur peut annuler la suppression et récupérer ses données.

2. Dans un autre scénario, un utilisateur non autorisé peut tenter de supprimer un coffre Key Vault ou un objet Key Vault, par exemple une clé se trouvant à l’intérieur d’un coffre, pour provoquer une interruption de service. Le fait de séparer la suppression du coffre Key Vault ou de l’objet Key Vault de la suppression effective des données sous-jacentes peut renforcer la sécurité, par exemple en limitant les autorisations de suppression de données à un autre rôle approuvé. Cette approche suppose d’appliquer un quorum à une opération donnée qui pourrait, dans d’autres circonstances, se traduire par une perte de données immédiate.

### <a name="soft-delete-behavior"></a>Comportement de la suppression réversible

Avec cette fonctionnalité, l’opération SUPPRIMER effectuée sur un coffre de clés ou un objet de coffre de clés constitue une suppression réversible, qui conserve efficacement les ressources pendant une période de conservation donnée (90 jours), tout en donnant l’impression que l’objet est supprimé. Le service fournit en outre un mécanisme de récupération de l’objet supprimé, qui a essentiellement pour effet d’annuler la suppression. 

La suppression réversible est un comportement facultatif de Key Vault et **n’est pas activée par défaut** dans cette version. Il peut être activé [CLI](key-vault-soft-delete-cli.md) ou [Powershell](key-vault-soft-delete-powershell.md).

### <a name="purge-protection"></a>Protection de purge 

Lorsque la purge protection se fait sur un coffre ou un objet dans un état supprimé ne peut pas être purgé jusqu'à ce que la période de rétention de 90 jours écoulée. Ces objets et les coffres peuvent encore être récupérés, garantissant aux clients que la stratégie de rétention sera suivie. 

Protection de purge est un comportement facultatif de Key Vault et **n’est ne pas activée par défaut**. Il peut être activé [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) ou [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

### <a name="permitted-purge"></a>Vidage autorisé

Il est possible de supprimer ou vider définitivement un coffre Key Vault en exécutant une commande POST sur la ressource de proxy. Cette opération nécessite des privilèges spéciaux. En général, seul le propriétaire de l’abonnement peut vider un coffre. L’opération POST déclenche la suppression immédiate et irrécupérable de ce coffre, 

Les exceptions sont :
- Lorsque l’abonnement Azure a été marqué comme *impossible à supprimer*. Dans ce cas, seul le service peut alors effectuer la suppression, dans le cadre d’un processus planifié. 
- Lorsque la--enable--protection de purge indicateur est activé sur le coffre lui-même. Dans ce cas, Key Vault attend pendant 90 jours à partir du moment où l’objet de secret d’origine a été marqué pour suppression pour le supprimer définitivement.

### <a name="key-vault-recovery"></a>Récupération d’un Key Vault

En cas de suppression d’un coffre de clés, le service crée une ressource de proxy sous l’abonnement, en y ajoutant suffisamment de métadonnées pour permettre la récupération. La ressource de proxy est un objet stocké, disponible au même emplacement que le Key Vault supprimé. 

### <a name="key-vault-object-recovery"></a>Récupération d’objets Key Vault

Lors de la suppression d’un objet de coffre de clés, comme une clé, le service place l’objet dans un état supprimé, rendant inaccessible aux opérations d’extraction. Dans cet état, l’objet de coffre de clés peut être seulement listé, restauré ou supprimé de façon forcée ou permanente. 

Dans le même temps, Key Vault planifiera la suppression des données sous-jacentes correspondant au Key Vault ou à l’objet Key Vault supprimé afin qu’elle soit exécutée après un intervalle de rétention prédéterminé. L’enregistrement DNS correspondant au coffre est également conservé pendant la durée de l’intervalle de rétention.

### <a name="soft-delete-retention-period"></a>Période de rétention de la suppression réversible

Les ressources ayant fait l’objet d’une suppression réversible sont conservées pendant une durée fixée à 90 jours. Au cours de la période de rétention de la suppression réversible :

- Vous pouvez répertorier tous les coffres Key Vault et les objets Key Vault à l’état de suppression réversible pour votre abonnement, et accéder à des informations concernant leur suppression et leur récupération.
    - Seuls les utilisateurs disposant d’autorisations spéciales peuvent répertorier des coffres supprimés. Nous recommandons à nos utilisateurs de créer un rôle personnalisé avec ces autorisations spéciales pour le traitement des coffres supprimés.
- Il est impossible de créer un Key Vault du même nom au même emplacement. En conséquence, un objet Key Vault ne peut pas être créé dans un coffre donné si celui-ci contient un objet portant le même nom et se trouvant à l’état supprimé 
- Seul un utilisateur disposant de privilèges spécifiques peut restaurer un coffre Key Vault ou un objet Key Vault en exécutant une commande de récupération sur la ressource de proxy correspondante.
    - L’utilisateur membre du rôle personnalisé qui est autorisé à créer un coffre sous le groupe de ressources peut restaurer le coffre.
- Seul un utilisateur disposant de privilèges spécifiques peut forcer la suppression d’un coffre Key Vault ou d’un objet Key Vault en exécutant une commande de suppression sur la ressource de proxy correspondante.

Sauf en cas de restauration d’un coffre de clés ou d’un objet de coffre de clés, à la fin de l’intervalle de rétention, le service vide le coffre de clés ou l’objet de coffre de clés qui a fait l’objet d’une suppression réversible, ainsi que son contenu. La suppression de la ressource ne peut pas être replanifiée.

### <a name="billing-implications"></a>Implications de facturation

En général, lorsqu’un objet (un coffre de clés, une clé ou un secret) est dans un état supprimé, seules deux opérations sont possibles : « vider » et « récupérer ». Toutes les autres opérations échoueront. Par conséquent, même si l’objet existe, aucune opération ne peut être effectuée et, par conséquent, aucune utilisation n’a lieu. Il n’y aura donc aucune facture. Il y a toutefois des exceptions :

- les actions « vider » et « récupérer » seront comptabilisée dans les opérations de coffre de clés normales et facturées.
- Si l’objet est une clé HSM, les frais de 'Clé protégée par HSM' par version de clé et par mois seront appliqués si une version de la clé a été utilisée au cours des 30 derniers jours. Après cela, dans la mesure où l’objet est dans un état supprimé, il ne peut faire l’objet d’aucune opération et, par conséquent, aucun frais ne s’applique.

## <a name="next-steps"></a>Étapes suivantes

Les deux guides ci-après présentent les principaux scénarios d’usage de la suppression réversible.

- [Utilisation de la suppression réversible Key Vault avec l’interface CLI](key-vault-soft-delete-powershell.md) 
- [Utilisation de la suppression réversible Key Vault avec l’interface CLI](key-vault-soft-delete-cli.md)

