---
title: Le Service Azure Blockchain Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027914"
---
# <a name="azure-blockchain-service-consortium"></a>Le Service Azure Blockchain Consortium

À l’aide d’Azure Blockchain Service, vous pouvez créer consortium privé réseaux blockchain où chaque réseau blockchain peut être limité aux participants spécifiques dans le réseau. Seuls les participants dans le réseau de blockchain consortium privé peuvent afficher et interagir avec la blockchain. Réseaux Consortium dans Azure Blockchain Service peuvent contenir deux types de membre des rôles participant :

* **Administrateur** -privilégié participants qui peuvent prendre les actions de gestion consortium et peuvent participer aux transactions de blockchain.

* **Utilisateur** -Participants qui ne peut pas intervenir consortium gestion mais peuvent participer aux transactions de blockchain.

Les réseaux consortium peuvent être une combinaison de rôles participants et peuvent avoir un nombre arbitraire de chaque type de rôle. Il doit exister au moins un administrateur.

Le diagramme suivant illustre un réseau de consortiums plusieurs participants :

![Diagramme du réseau privé consortium](./media/consortium/network-diagram.png)

Avec la gestion du consortium dans Azure Blockchain Service, vous pouvez gérer les participants dans le réseau de consortiums. Gestion du consortium est basée sur le modèle de consensus du réseau. Dans la version préliminaire actuelle, Azure Blockchain Service fournit un modèle de consensus centralisé pour la gestion du consortium. Tout participant privilégié avec un rôle d’administrateur peut prendre les actions de gestion du consortium, comme l’ajout ou suppression des participants à partir d’un réseau.

## <a name="roles"></a>contrôleur

Participants à un consortium peuvent être individus ou organisations et vous pouvez affecter un rôle d’utilisateur ou un rôle d’administrateur. Le tableau suivant répertorie les principales différences entre les deux rôles :

| Action | Rôle utilisateur | Rôle administrateur
|--------|:----:|:------------:|
| Créer nouveau membre | Oui | Oui |
| Inviter de nouveaux membres | Non  | Oui |
| Définir ou modifier le rôle du participant membre | Non  | Oui |
| Nom d’affichage de membre de modification | Uniquement pour les membres propre | Uniquement pour les membres propre |
| Supprimer des membres | Uniquement pour les membres propre | Oui |
| Participer aux transactions de blockchain | Oui | Oui |

### <a name="user-role"></a>Rôle utilisateur

Les utilisateurs sont des participants du consortium avec les fonctions d’administrateur. Ils ne peuvent pas participer à la gestion des membres liés au consortium. Les utilisateurs peuvent modifier leur nom complet du membre et se retirer un consortium.

### <a name="administrator"></a>Administrateur

Un administrateur peut gérer des membres au sein du consortium. Un administrateur peut inviter des membres, supprimer des membres ou mettre à jour les rôles des membres au sein du consortium.
Il doit toujours y avoir au moins un administrateur au sein d’un consortium. Le dernier administrateur doit spécifier un autre participant en tant qu’un rôle d’administrateur avant de quitter un consortium.

## <a name="managing-members"></a>La gestion des membres

Seuls les administrateurs peuvent inviter d’autres participants le consortium. Les administrateurs inviter les participants à l’aide de leur ID d’abonnement Azure.

Une fois que les invités, des participants du consortium de blockchain en déployant un nouveau membre dans Azure Blockchain Service. Pour afficher et rejoindre le consortium invité, vous devez spécifier le même ID d’abonnement Azure utilisé dans l’invitation par l’administrateur réseau.

Les administrateurs peuvent supprimer n’importe quel participant du consortium, y compris les autres administrateurs. Les membres peuvent uniquement se désabonner à partir d’un consortium.

## <a name="consortium-management-smart-contract"></a>Contrat de consortium gestion intelligente

Gestion de consortium dans Azure Blockchain Service s’effectue par le biais de contrats intelligents de gestion consortium. Lorsque vous déployez un nouveau membre de blockchain, les contrats intelligents sont déployés automatiquement sur vos nœuds.

L’adresse du contrat racine consortium gestion intelligente peut être affiché dans le portail Azure. Le **RootContract adresse** est dans la section de vue d’ensemble du membre de la fonctionnalité « blockchain ».

![Adresse de RootContract](./media/consortium/rootcontract-address.png)

Vous pouvez interagir avec le contrat intelligente de gestion consortium à l’aide de la gestion du consortium [module PowerShell](manage-consortium-powershell.md), Azure portail, ou directement via le contrat intelligent à l’aide du Service de Blockchain Azure généré Ethereum compte.

## <a name="ethereum-account"></a>Compte de Ethereum

Lorsqu’un membre est créé, une clé de compte Ethereum est créée. Azure Blockchain Service utilise la clé pour créer des transactions liées à la gestion du consortium. La clé du compte Ethereum est gérée automatiquement par le Service Azure Blockchain.

Le compte de membre peut être affiché dans le portail Azure. Le compte de membre est dans la section de vue d’ensemble du membre de la fonctionnalité « blockchain ».

![Compte de membre](./media/consortium/member-account.png)

Vous pouvez réinitialiser votre compte Ethereum en cliquant sur votre compte de membre et en entrant un nouveau mot de passe. L’adresse du compte Ethereum et le mot de passe seront réinitialisés.  

## <a name="next-steps"></a>Étapes suivantes

[Comment gérer les membres dans Azure Blockchain Service à l’aide de PowerShell](manage-consortium-powershell.md)
