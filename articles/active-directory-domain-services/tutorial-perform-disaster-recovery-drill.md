---
title: Tutoriel – Effectuer une simulation de reprise d’activité après sinistre dans Azure AD Domain Services | Microsoft Docs
description: Découvrez comment effectuer une simulation de reprise d’activité à l’aide de jeux de réplicas dans Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: justinha
ms.openlocfilehash: 1816c4615e96a1e88c0a76d2defcb2c6d6686650
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701310"
---
# <a name="tutorial-perform-a-disaster-recovery-drill-using-replica-sets-in-azure-active-directory-domain-services"></a>Tutoriel : Effectuer une simulation de reprise d’activité à l’aide de jeux de réplicas dans Azure Active Directory Domain Services

Cette rubrique montre comment effectuer une simulation de reprise d’activité (DR) pour Azure AD Domain Services (Azure AD DS) à l’aide de jeux de réplicas.  La mise hors connexion de l’un des jeux de réplicas est simulée en modifiant les propriétés du réseau virtuel afin de bloquer l’accès client à ce dernier.  Il ne s’agit pas d’une véritable reprise d’activité car le jeu de réplicas n’est pas mis hors connexion. 

La simulation de reprise d’activité couvre les opérations suivantes : 

1. Un ordinateur client est connecté à un jeu de réplicas donné. Il peut s’authentifier auprès du domaine et effectuer des requêtes LDAP.
1. La connexion du client au jeu de réplicas va être arrêtée. Pour ce faire, l’accès au réseau est restreint.
1. Le client établit ensuite une nouvelle connexion avec l’autre jeu de réplicas. Une fois cette connexion établie, le client est capable de s’authentifier auprès du domaine et d’effectuer des requêtes LDAP. 
1. Le membre du domaine est redémarré, et un utilisateur du domaine peut ensuite se connecter à l’issue du redémarrage.
1. Les restrictions d’accès au réseau sont supprimées et le client peut se connecter au jeu de réplicas d’origine. 

## <a name="prerequisites"></a>Prérequis 

Les conditions suivantes doivent être remplies pour pouvoir effectuer la simulation de reprise d’activité : 

- Une instance Azure AD DS active est déployée avec au moins un jeu de réplicas supplémentaire en place. Le domaine doit être dans un état sain. 
- Un ordinateur client est joint au domaine hébergé Azure AD DS.  Le client doit se trouver dans son propre réseau virtuel, un peering de réseaux virtuels est activé avec les deux réseaux virtuels des jeux de réplicas, et le réseau virtuel doit avoir les adresses IP de tous les contrôleurs de domaine dans les jeux de réplicas listés dans DNS. 

## <a name="environment-validation"></a>Validation de l’environnement 

1. Connectez-vous à l’ordinateur client à l’aide d’un compte de domaine. 
1. Installez les outils RSAT Active Directory Domain Services. 
1. Ouvrez une fenêtre PowerShell avec des privilèges élevés.
1. Effectuez les vérifications de validation de domaine de base : 
   - Exécutez `nslookup [domain]` pour vérifier que la résolution DNS fonctionne correctement. 
   - Exécutez `nltest /dsgetdc:` pour retourner une réussite et indiquer quel contrôleur de domaine est actuellement utilisé.
   - Exécutez `nltest /dclist:` pour retourner la liste complète des contrôleurs de domaine dans l’annuaire. 
1. Effectuez la validation de base des contrôleurs de domaine sur chaque contrôleur de domaine inclus dans l’annuaire (vous pouvez obtenir la liste complète à partir de la sortie de nltest/dclist:) : 
   - Exécutez `nltest /sc_reset:[domain name]\[domain controller name]` pour établir une connexion sécurisée avec le contrôleur de domaine. 
   - Exécutez `Get-AdDomain` pour récupérer les paramètres de base de l’annuaire. 

## <a name="perform-the-disaster-recovery-drill"></a>Effectuer une simulation de reprise d’activité 

Vous allez effectuer ces opérations pour chaque jeu de réplicas inclus dans l’instance Azure AD DS. Ainsi, vous allez simuler une panne pour chaque jeu de réplicas. Quand des contrôleurs de domaine ne sont pas accessibles, le client bascule automatiquement vers un contrôleur de domaine accessible et cette expérience doit se dérouler sans heurts pour l’utilisateur final ou la charge de travail. Il est donc primordial que les applications et services ne pointent pas vers un contrôleur de domaine spécifique. 

1. Identifiez les contrôleurs de domaine dans le jeu de réplicas dont vous voulez simuler la mise hors connexion. 
1. Sur l’ordinateur client, connectez-vous à l’un des contrôleurs de domaine en utilisant `nltest /sc_reset:[domain]\[domain controller name]`. 
1. Dans le portail Azure, accédez au peering de réseaux virtuels du client et mettez à jour les propriétés afin que tout le trafic entre le client et le jeu de réplicas soit bloqué. 
   1. Sélectionnez le réseau appairé à mettre à jour. 
   1. Sélectionnez l’option permettant de bloquer tout le trafic réseau qui entre dans le réseau virtuel ou en sort. 
      ![Capture d’écran montrant comment bloquer le trafic dans le portail Azure](./media/tutorial-perform-disaster-recovery-drill/block-traffic.png)
1. Sur l’ordinateur client, essayez de rétablir une connexion sécurisée aux deux contrôleurs de domaine de l’étape 2 en utilisant la même commande nltest. Ces opérations doivent échouer, car la connectivité réseau a été bloquée. 
1. Exécutez `Get-AdDomain` et `Get-AdForest` pour obtenir les propriétés d’annuaire de base. Ces appels aboutissent, car ils accèdent automatiquement à l’un des contrôleurs de domaine dans l’autre jeu de réplicas. 
1. Redémarrez le client et connectez-vous avec le même compte de domaine. Celui-ci indique que l’authentification fonctionne encore comme prévu et que les connexions ne sont pas bloquées. 
1. Dans le portail Azure, accédez au peering de réseaux virtuels du client et mettez à jour les propriétés afin que tout le trafic soit débloqué. Cette opération annule les modifications apportées à l’étape 3. 
1. Sur l’ordinateur client, essayez de rétablir une connexion sécurisée aux contrôleurs de domaine de l’étape 2 en utilisant la même commande nltest. Ces opérations doivent aboutir, car la connectivité réseau a été débloquée. 

Ces opérations montrent que le domaine est toujours disponible, même si l’un des jeux de réplicas est inaccessible par le client. Effectuez cette série d’étapes pour chaque jeu de réplicas inclus dans l’instance Azure AD DS. 

## <a name="summary"></a>Résumé 

Une fois que vous aurez terminé ces étapes, vous allez voir que les membres du domaine continuent d’accéder à l’annuaire si l’un des jeux de réplicas n’est pas accessible dans Azure AD DS. Vous pouvez simuler le même comportement en bloquant tout l’accès réseau pour un jeu de réplicas au lieu d’un ordinateur client, mais nous vous le déconseillons. Cette façon de procéder ne modifie pas le comportement du point de vue du client, mais elle affecte l’intégrité de votre instance Azure AD DS tant que l’accès réseau n’est pas restauré. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Valider la connectivité du client aux contrôleurs de domaine dans un jeu de réplicas
> * Bloquer le trafic réseau entre le client et le jeu de réplicas
> * Valider la connectivité du client aux contrôleurs de domaine dans un autre jeu de réplicas

Pour plus d’informations conceptuelles, découvrez comment les jeux de réplicas fonctionnent dans Azure AD DS.

> [!div class="nextstepaction"]
> [Concepts et fonctionnalités des jeux de réplicas][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
