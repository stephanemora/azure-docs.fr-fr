---
title: Comprendre les rôles d’administrateur d’entreprise dans Azure
description: Découvrez les rôles d’administrateur d’entreprise dans Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: banders
ms.openlocfilehash: 28fff2a24f703e0cff967bdde29a7b1238e197ee
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586145"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure

Pour faciliter la gestion de l’utilisation et des dépenses d’une organisation, les clients Azure disposant d’un Contrat Entreprise peuvent attribuer cinq rôles d’administrateur distincts :

- Administrateur d’entreprise
- Administrateur d’entreprise (lecture seule)<sup>1</sup>
- Administrateur de service
- Administrateur de service (lecture seule)
- Propriétaire de compte<sup>2</sup>

<sup>1</sup> Le contact de facturation du contrat EA sera sous ce rôle.

<sup>2</sup> Le contact de facturation ne peut pas être ajouté ou modifié sur le portail Azure Enterprise Portal et sera ajouté à l’inscription EA en fonction de l’utilisateur qui est configuré en tant que contact de facturation au niveau du contrat. Pour modifier le contact de facturation, une demande doit être effectuée par l’intermédiaire d’un conseiller partenaire/logiciel auprès du centre régional des opérations (ROC).

Ces rôles sont spécifiques à la gestion des Contrats Entreprise Azure et s’ajoutent aux rôles intégrés dont Azure doit contrôler l’accès aux ressources. Pour plus d’informations, consultez [Rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

Les sections suivantes décrivent les limitations et les capacités de chaque rôle.

## <a name="user-limit-for-admin-roles"></a>Limite utilisateur pour les rôles d’administrateur

|Role| Limite utilisateur|
|---|---|
|Administrateur d’entreprise|Illimité|
|Administrateur d’entreprise (lecture seule)|Illimité|
|Administrateur de service|Illimité|
|Administrateur de service (lecture seule)|Illimité|
|Propriétaire du compte|1 par compte<sup>3</sup>|

<sup>3</sup> Chaque compte nécessite un compte Microsoft unique ou un compte professionnel ou scolaire.

## <a name="organization-structure-and-permissions-by-role"></a>Structure de l’organisation et autorisations par rôle

|Tâches| Administrateur d’entreprise|Administrateur d’entreprise (lecture seule)|Administrateur de service|Administrateur de service (lecture seule)|Propriétaire du compte| Partenaire|
|---|---|---|---|---|---|---|
|Voir les administrateurs d’entreprise|✔|✔|✘|✘|✘|✔|
|Ajouter ou supprimer des administrateurs d’entreprise|✔|✘|✘|✘|✘|✘|
|Voir les contacts de notification<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Ajouter ou supprimer des contacts de notification<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Créer et gérer des services |✔|✘|✘|✘|✘|✘|
|Voir les administrateurs de service|✔|✔|✔|✔|✘|✔|
|Ajouter ou supprimer des administrateurs de service|✔|✘|✔|✘|✘|✘|
|Voir les comptes de l’inscription |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Ajouter des comptes à l’inscription et changer le propriétaire du compte|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Créer et gérer des abonnements et des autorisations d’abonnement|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Les contacts de notification reçoivent des communications par e-mail à propos du Contrat Entreprise Azure.
- <sup>5</sup> La tâche est limitée aux comptes de votre service.


## <a name="usage-and-costs-access-by-role"></a>Accès à l’utilisation et aux coûts par rôle

|Tâches| Administrateur d’entreprise|Administrateur d’entreprise (lecture seule)|Administrateur de service|Administrateur de service (lecture seule) |Propriétaire du compte| Partenaire|
|---|---|---|---|---|---|---|
|Voir le solde de crédit, notamment l’engagement financier|✔|✔|✘|✘|✘|✔|
|Voir les quotas de dépenses des services|✔|✔|✘|✘|✘|✔|
|Définir les quotas de dépenses des services|✔|✘|✘|✘|✘|✘|
|Voir la grille tarifaire du Contrat Entreprise de l’organisation|✔|✔|✘|✘|✘|✔|
|Voir les détails relatifs à l’utilisation et aux coûts|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gérer les ressources dans le portail Azure|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> L’administrateur d’entreprise doit activer la stratégie d’**affichage des frais pour l’administrateur de service** dans Enterprise Portal. L’administrateur de service peut ensuite consulter le détail des coûts pour le service.
- <sup>7</sup> L’administrateur d’entreprise doit activer la stratégie d’**affichage des frais pour le propriétaire du compte** dans Enterprise Portal. Le propriétaire du compte peut ensuite consulter le détail des coûts pour le compte.


## <a name="pricing-in-azure-portal"></a>Tarifs dans le portail Azure

Vous pouvez voir différents tarifs dans le portail Azure en fonction de votre rôle d’administrateur et de la manière dont les stratégies d’affichage des frais sont définies par l’administrateur d’entreprise. Les deux stratégies d’Enterprise Portal qui affectent les tarifs que vous voyez dans le portail Azure sont les suivantes :

- Affichage des frais pour l’administrateur de service
- Affichage des frais pour le propriétaire du compte

Pour découvrir comment définir ces stratégies, consultez [Gérer l’accès aux informations de facturation pour Azure](manage-billing-access.md).

Le tableau suivant montre la relation entre les rôles d’administrateur Contrat Entreprise, la stratégie d’affichage des frais, le rôle RBAC (contrôle d’accès en fonction du rôle) du portail Azure et les tarifs que vous voyez dans le portail Azure. L’administrateur d’entreprise voit toujours les détails relatifs à l’utilisation en fonction des tarifs Contrat Entreprise de l’organisation. Toutefois, l’administrateur de service et le propriétaire du compte voient différents tarifs en fonction de la stratégie d’affichage des frais et de leur rôle RBAC. Le rôle Administrateur de service listé dans le tableau suivant fait référence aux rôles Administrateur de service et Administrateur de service (lecture seule).

|Rôle d’administrateur Contrat Entreprise|Stratégie d’affichage des frais pour le rôle|Rôle RBAC|Affichage des tarifs|
|---|---|---|---|
|Propriétaire du compte OU Administrateur de service|✔ Activée|Propriétaire|Tarifs Contrat Entreprise de l’organisation|
|Propriétaire du compte OU Administrateur de service|✘ Désactivée|Propriétaire|Tarifs au détail|
|Propriétaire du compte OU Administrateur de service|✔ Activée |Aucun|Aucun tarif|
|Propriétaire du compte OU Administrateur de service|✘ Désactivée |Aucun|Aucun tarif|
|None|Non applicable |Propriétaire|Tarifs au détail|

Vous définissez le rôle d’administrateur Contrat Entreprise et les stratégies d’affichage des frais dans Enterprise Portal. Vous pouvez mettre à jour le rôle RBAC dans le portail Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide de RBAC et du portail Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer l’accès aux informations de facturation pour Azure](manage-billing-access.md)
- [Manage access using RBAC and the Azure portal](../../role-based-access-control/role-assignments-portal.md) (Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du Portail Azure)
- [Rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md)
