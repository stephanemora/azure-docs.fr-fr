---
title: 'Azure Active Directory Domain Services : Résoudre les problèmes d’alertes | Microsoft Docs'
description: Dépannage des alertes pour les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: ed0ff8cde39f2660c2149e43399d937184620773
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246703"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services : dépannage des alertes
Cet article fournit des guides de dépannage pour les alertes que vous pouvez rencontrer sur votre domaine géré.


Choisissez les étapes de résolution qui correspondent à l’ID ou au message d’erreur de l’alerte.

| **ID de l'alerte** | **Message d'alerte** | **Résolution :** |
| --- | --- | :--- |
| AADDS001 | *LDAP sécurisé sur Internet est activé pour le domaine géré. Toutefois, l’accès au port 636 n’est pas verrouillé à l’aide d’un Groupe de sécurité réseau (NSG). Cela peut exposer les comptes d’utilisateur sur le domaine géré aux attaques par force brute.* | [Configuration de LDAP sécurisé incorrecte](alert-ldaps.md) |
| AADDS100 | *L’annuaire Azure AD associé à votre domaine géré a peut-être été supprimé. Le domaine géré n’est plus dans une configuration prise en charge. Microsoft ne peut pas surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [Répertoire manquant](#aadds100-missing-directory) |
| AADDS101 | *Les services de domaine Azure AD ne peuvent pas être activés dans un annuaire Azure AD B2C.* | [Azure AD B2C est en cours d’exécution dans ce répertoire](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Un principal de service requis pour que les services de domaine Azure AD fonctionnent correctement a été supprimé de votre annuaire Azure AD. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [Principal de service manquant](alert-service-principal.md) |
| AADDS103 | *La plage d’adresses IP pour le réseau virtuel dans lequel vous avez activé les services de domaine Azure AD est dans une plage d’adresses IP publiques. Les services de domaine Azure AD doivent être activés dans un réseau virtuel avec une plage d’adresses IP privées. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [L’adresse est dans une plage d’adresses IP publiques](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft ne peut pas atteindre les contrôleurs de domaine pour ce domaine géré. Cela peut se produire si un groupe de sécurité réseau (NSG) configuré sur votre réseau virtuel bloque l’accès à un domaine géré. Une autre raison possible est s’il existe un itinéraire défini par l’utilisateur qui bloque le trafic entrant à partir d’Internet.* | [Erreur réseau](alert-nsg.md) |
| AADDS105 | *Le principal du service avec l’application ID « d87dcbc6-a371-462e-88e3-28ad15ec4e64 » a été supprimé puis recréé. Cette nouvelle création laisse des autorisations incohérentes sur les ressources Azure AD Domain Services nécessaires pour traiter votre domaine managé. La synchronisation des mots de passe dans votre domaine managé pourrait en être affectée.* | [La synchronisation du mot de passe est obsolète](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Votre abonnement Azure associé à votre domaine managé a été supprimé.  Azure AD Domain Services nécessite un abonnement actif pour continuer à fonctionner correctement.* | [L'abonnement Azure est introuvable](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Votre abonnement Azure associé à votre domaine managé n’est pas actif.  Azure AD Domain Services nécessite un abonnement actif pour continuer à fonctionner correctement.* | [L’abonnement Azure est désactivé](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *L’abonnement utilisé par Azure AD Domain Services a été déplacé dans un autre répertoire. Azure AD Domain Services nécessite un abonnement actif dans le même répertoire pour continuer à fonctionner correctement.* | [Annuaires déplacés d’abonnements](#aadds108-subscription-moved-directories) |
| AADDS109 | *Une ressource utilisée pour votre domaine managé a été supprimée. Cette ressource est requise pour qu’Azure AD Domain Services fonctionne correctement.* | [Une ressource a été supprimée](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Le sous-réseau sélectionné pour le déploiement d’Azure AD Domain Services est plein et n’a pas l’espace nécessaire pour le contrôleur de domaine supplémentaire qui doit être créé.* | [Le sous-réseau est plein](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Un principal de service qu’Azure AD Domain Services utilise pour gérer votre domaine n’est pas autorisé à gérer les ressources de l’abonnement Azure. Le principal du service doit obtenir les autorisations nécessaires pour gérer votre domaine managé.* | [Principal du service non autorisé](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Nous avons détecté que le sous-réseau du réseau virtuel dans ce domaine n’a peut-être pas suffisamment d’adresses IP. Azure AD Domain Services a besoin d’au moins deux adresses IP disponibles au sein du sous-réseau où il est activé. Nous vous recommandons d’avoir au moins 3 à 5 adresses IP auxiliaires au sein de ce sous-réseau. Cela peut se produire si d’autres machines virtuelles sont déployées au sein du sous-réseau, épuisant ainsi le nombre d’adresses IP disponibles, ou s’il existe une restriction sur le nombre d’adresses IP disponibles dans le sous-réseau.* | [Nombre insuffisant d’adresses IP](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Les ressources utilisées par Azure AD Domain Services ont été détectées dans un état inattendu et ne peuvent pas être récupérées.* | [Des ressources sont irrécupérables](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Le sous-réseau sélectionné pour le déploiement d’Azure AD Domain Services n’est pas valide et ne peut pas être utilisé.* | [Sous-réseau non valide](#aadds114-subnet-invalid) |
| AADDS115 | *Il est impossible d’opérer sur une ou plusieurs des ressources réseau utilisées par le domaine managé, car l’étendue cible a été verrouillée.* | [Des ressources sont verrouillées](#aadds115-resources-are-locked) |
| AADDS116 | *En raison d’une ou de plusieurs restrictions de stratégie, il est impossible d’opérer sur une ou plusieurs des ressources réseau utilisées par le domaine managé.* | [Des ressources sont inutilisables](#aadds116-resources-are-unusable) |
| AADDS500 | *La dernière synchronisation du domaine managé avec Azure AD a eu lieu le [date]. Les utilisateurs sont peut-être dans l’impossibilité de se connecter au domaine managé, ou les appartenances aux groupes ne sont peut-être pas synchronisées avec Azure AD.* | [Il n’y a pas eu de synchronisation depuis un certain temps.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *La dernière sauvegarde du domaine managé a eu lieu le [date].* | [Il n’y a pas eu de sauvegarde depuis un certain temps.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Le certificat LDAP sécurisé pour le domaine managé expirera le [date].* | [Expiration du certificat LDAP sécurisé](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Le domaine managé est suspendu, car l’abonnement Azure associé au domaine n’est pas actif.* | [Suspension en raison de l’abonnement désactivé](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Le domaine managé est suspendu en raison d’une configuration non valide. Le service n’a pas pu gérer, corriger ou mettre à jour les contrôleurs du domaine managé depuis un certain temps.* | [Suspension en raison d’une configuration non valide](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100 : Annuaire manquant
**Message d'alerte :**

*L’annuaire Azure AD associé à votre domaine géré a peut-être été supprimé. Le domaine géré n’est plus dans une configuration prise en charge. Microsoft ne peut pas surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

**Résolution :**

Cette erreur est généralement causée par un déplacement incorrect de votre abonnement Azure vers un nouvel annuaire Azure AD, et par la suppression de l’ancien répertoire Azure AD qui est toujours associé à des services de domaine Azure AD.

Cette erreur est irrécupérable. Pour résoudre ce problème, vous devez [supprimer votre domaine géré existant](delete-aadds.md) puis le recréer dans votre nouveau répertoire. Si vous avez des difficultés à le supprimer, contactez l’équipe produit des services de domaine Azure Active Directory [pour obtenir de l’aide](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101 : Azure AD B2C est en cours d’exécution dans cet annuaire
**Message d'alerte :**

*Les services de domaine Azure AD ne peuvent pas être activés dans un annuaire Azure AD B2C.*

**Résolution :**

>[!NOTE]
>Pour pouvoir continuer à utiliser les services de domaine Azure AD, vous devez recréer votre instance de services de domaine Azure AD dans un répertoire hors Azure AD B2C.

Pour restaurer votre service, procédez comme suit :

1. [Supprimez votre domaine géré](delete-aadds.md) de votre annuaire Azure AD existant.
2. Créez un répertoire qui n’est pas un répertoire Azure AD B2C.
3. Suivez le guide de [Prise en main](create-instance.md) pour recréer un domaine géré.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103 : L’adresse est dans une plage d’adresses IP publiques

**Message d'alerte :**

*La plage d’adresses IP pour le réseau virtuel dans lequel vous avez activé les services de domaine Azure AD est dans une plage d’adresses IP publiques. Les services de domaine Azure AD doivent être activés dans un réseau virtuel avec une plage d’adresses IP privées. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

**Résolution :**

> [!NOTE]
> Pour résoudre ce problème, vous devez supprimer votre domaine géré existant et le créer de nouveau dans un réseau virtuel avec une plage d’adresses IP privées. Ce processus cause une interruption.

Avant de commencer, lisez la section **Espace d’adressage IPv4** de [cet article](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

À l’intérieur du réseau virtuel, les machines peuvent effectuer des requêtes sur les ressources Azure qui se trouvent dans la même plage d’adresses IP que celles configurées pour le sous-réseau. Toutefois, étant donné que le réseau virtuel est configuré pour cette plage, ces requêtes sont routées au sein du réseau virtuel et n’atteignent pas les ressources web prévues. Cette configuration risque d’entraîner des erreurs imprévisibles avec Azure AD Domain Services.

**Si vous avez la plage d’adresses IP dans l’Internet configuré dans votre réseau virtuel, vous pouvez ignorer cette alerte. Il est toutefois impossible pour Azure AD Domain Services de respecter les termes du contrat [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) avec cette configuration, car elle peut entraîner des erreurs imprévisibles.**


1. [Supprimez votre domaine géré](delete-aadds.md) de votre annuaire.
2. Corrigez la plage d’adresses IP pour le sous-réseau
   1. Accédez à la [page Réseaux virtuels sur le portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Sélectionnez le réseau virtuel que vous envisagez d’utiliser pour les services de domaine Azure AD.
   3. Cliquez sur **Espace d’adressage** sous Paramètres
   4. Mettez à jour la plage d’adresses en cliquant sur la plage d’adresses existante et en la modifiant ou en ajoutant une plage d’adresses supplémentaire. Assurez-vous que la nouvelle plage d’adresses est dans une plage d’adresses IP privées. Enregistrez vos modifications.
   5. Cliquez sur **Sous-réseaux** dans la navigation de gauche.
   6. Cliquez sur le sous-réseau que vous souhaitez modifier dans la table.
   7. Mettez à jour la plage d’adresses et enregistrez vos modifications.
3. Suivez [le guide Prise en main des services de domaine Azure AD](create-instance.md) pour recréer votre domaine géré. Veillez à sélectionner un réseau virtuel avec une plage d’adresses IP privées.
4. Pour joindre vos machines virtuelles à votre nouveau domaine, suivez [ce guide](join-windows-vm.md).
8. Pour vérifier que l’alerte est résolue, vérifiez l’intégrité de votre domaine dans deux heures.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106 : Votre abonnement Azure est introuvable

**Message d'alerte :**

*Votre abonnement Azure associé à votre domaine managé a été supprimé.  Azure AD Domain Services nécessite un abonnement actif pour continuer à fonctionner correctement.*

**Résolution :**

Azure AD Domain Services requiert un abonnement pour fonctionner et ne peut pas être déplacé vers un autre abonnement. Étant donné que l’abonnement Azure auquel votre domaine managé était associé a été supprimé, vous devez recréer un abonnement Azure et Azure AD Domain Services.

1. Crée un abonnement Azure
2. [Supprimez votre domaine géré](delete-aadds.md) de votre annuaire Azure AD existant.
3. Suivez le guide de [Prise en main](create-instance.md) pour recréer un domaine géré.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107 : Votre abonnement Azure est désactivé

**Message d'alerte :**

*Votre abonnement Azure associé à votre domaine managé n’est pas actif.  Azure AD Domain Services nécessite un abonnement actif pour continuer à fonctionner correctement.*

**Résolution :**


1. [Renouvelez votre abonnement Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Une fois l’abonnement renouvelé, Azure AD Domain Services reçoit une notification d’Azure pour réactiver votre domaine managé.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108 : Annuaires déplacés d’abonnements

**Message d'alerte :**

*L’abonnement utilisé par Azure AD Domain Services a été déplacé dans un autre répertoire. Azure AD Domain Services nécessite un abonnement actif dans le même répertoire pour continuer à fonctionner correctement.*

**Résolution :**

Vous pouvez soit déplacer l’abonnement associé à Azure AD Domain Services vers l’annuaire précédent, soit [supprimer votre domaine managé](delete-aadds.md) de l’annuaire existant et le recréer dans l’annuaire choisi (soit avec un nouvel abonnement, soit modifier l’annuaire dans lequel se trouve votre instance Azure AD Domain Services).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109 : Les ressources pour votre domaine managé sont introuvables

**Message d'alerte :**

*Une ressource utilisée pour votre domaine managé a été supprimée. Cette ressource est requise pour qu’Azure AD Domain Services fonctionne correctement.*

**Résolution :**

Azure AD Domain Services crée des ressources spécifiques lors du déploiement afin de fonctionner correctement, y compris des adresses IP publiques, des cartes réseau et un équilibreur de charge. Si certains de ces éléments sont supprimés, cela place votre domaine managé dans un état non pris en charge et empêche votre domaine d’être géré. Cette alerte intervient lorsqu’une personne qui est en mesure de modifier les ressources Azure AD Domain Services supprime une ressource nécessaire. Les étapes suivantes décrivent comment restaurer votre domaine managé.

1. Accédez à la page d’intégrité d’Azure AD Domain Services.
   1.    Accédez à la [page Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
   2.    Dans le volet de navigation gauche, cliquez sur **Intégrité**.
2. Vérifiez si l’alerte a moins de 4 heures.
   1.    Dans la page d’intégrité, cliquez sur l’alerte avec l’ID **AADDS109**
   2.    L’alerte a un horodatage correspondant au moment où elle a été trouvée en premier. Si cet horodatage remonte à moins de 4 heures, il est possible qu’Azure AD Domain Services puisse recréer la ressource supprimée.
3. Si l’alerte a plus de 4 heures, le domaine managé est dans un état irrécupérable. Vous devez supprimer et recréer Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110 : Le sous-réseau associé à votre domaine managé est plein

**Message d'alerte :**

*Le sous-réseau sélectionné pour le déploiement d’Azure AD Domain Services est plein et n’a pas l’espace nécessaire pour le contrôleur de domaine supplémentaire qui doit être créé.*

**Résolution :**

Cette erreur est irrécupérable. Pour résoudre ce problème, vous devez [supprimer votre domaine managé existant](delete-aadds.md), puis [recréer votre domaine managé](create-instance.md).

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111 : Principal du service non autorisé

**Message d'alerte :**

*Un principal de service qu’Azure AD Domain Services utilise pour gérer votre domaine n’est pas autorisé à gérer les ressources de l’abonnement Azure. Le principal du service doit obtenir les autorisations nécessaires pour gérer votre domaine managé.*

**Résolution :**

Nos principaux de service ont besoin d’un accès pour pouvoir gérer et créer des ressources sur votre domaine managé. Une personne a refusé l’accès au principal du service et celui-ci ne pas gérer les ressources. Pour accorder l’accès à votre principal de service, procédez comme suit.

1. En savoir plus sur le [contrôle RBAC et la façon d’accorder un accès aux applications sur le portail Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Passez en revue l’accès du principal du service avec l’ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` et accordez l’accès qui a été refusé précédemment.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112 : Nombre insuffisant d’adresses IP dans le domaine managé

**Message d'alerte :**

*Nous avons détecté que le sous-réseau du réseau virtuel dans ce domaine n’a peut-être pas suffisamment d’adresses IP. Azure AD Domain Services a besoin d’au moins deux adresses IP disponibles au sein du sous-réseau où il est activé. Nous vous recommandons d’avoir au moins 3 à 5 adresses IP auxiliaires au sein de ce sous-réseau. Cela peut se produire si d’autres machines virtuelles sont déployées au sein du sous-réseau, épuisant ainsi le nombre d’adresses IP disponibles, ou s’il existe une restriction sur le nombre d’adresses IP disponibles dans le sous-réseau.*

**Résolution :**

1. Supprimez votre domaine managé de votre locataire.
2. Corrigez la plage d’adresses IP pour le sous-réseau
   1. Accédez à la [page Réseaux virtuels sur le portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Sélectionnez le réseau virtuel que vous envisagez d’utiliser pour les services de domaine Azure AD.
   3. Cliquez sur **Espace d’adressage** sous Paramètres
   4. Mettez à jour la plage d’adresses en cliquant sur la plage d’adresses existante et en la modifiant ou en ajoutant une plage d’adresses supplémentaire. Enregistrez vos modifications.
   5. Cliquez sur **Sous-réseaux** dans la navigation de gauche.
   6. Cliquez sur le sous-réseau que vous souhaitez modifier dans la table.
   7. Mettez à jour la plage d’adresses et enregistrez vos modifications.
3. Suivez [le guide Prise en main des services de domaine Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) pour recréer votre domaine géré. Veillez à sélectionner un réseau virtuel avec une plage d’adresses IP privées.
4. Pour joindre vos machines virtuelles à votre nouveau domaine, suivez [ce guide](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Consultez l’intégrité de votre domaine après deux heures pour vérifier que vous avez correctement suivi les étapes.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113 : Des ressources sont irrécupérables

**Message d'alerte :**

*Les ressources utilisées par Azure AD Domain Services ont été détectées dans un état inattendu et ne peuvent pas être récupérées.*

**Résolution :**

Cette erreur est irrécupérable. Pour résoudre ce problème, vous devez [supprimer votre domaine managé existant](delete-aadds.md), puis [recréer votre domaine managé](create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114 : Sous-réseau non valide

**Message d'alerte :**

*Le sous-réseau sélectionné pour le déploiement d’Azure AD Domain Services n’est pas valide et ne peut pas être utilisé.*

**Résolution :**

Cette erreur est irrécupérable. Pour résoudre ce problème, vous devez [supprimer votre domaine managé existant](delete-aadds.md), puis [recréer votre domaine managé](create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115 : Des ressources sont verrouillées

**Message d'alerte :**

*Il est impossible d’opérer sur une ou plusieurs des ressources réseau utilisées par le domaine managé, car l’étendue cible a été verrouillée.*

**Résolution :**

1.  Passez en revue les journaux d’activité des opérations Resource Manager sur les ressources réseau (cela doit vous fournir des informations sur le verrouillage qui empêche la modification).
2.  Supprimez les verrous sur les ressources afin que le principal de service Azure AD Domain Services puisse opérer sur ces dernières.

## <a name="aadds116-resources-are-unusable"></a>AADDS116 : Des ressources sont inutilisables

**Message d'alerte :**

*En raison d’une ou de plusieurs restrictions de stratégie, il est impossible d’opérer sur une ou plusieurs des ressources réseau utilisées par le domaine managé.*

**Résolution :**

1.  Passez en revue les journaux d’activité des opérations Resource Manager sur les ressources réseau pour votre domaine managé.
2.  Assouplissez les restrictions de stratégie sur les ressources afin que le principal de service AAD-DS puisse opérer sur ces dernières.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500 : La synchronisation ne parvient pas à se terminer depuis un certain temps

**Message d'alerte :**

*La dernière synchronisation du domaine managé avec Azure AD a eu lieu le [date]. Les utilisateurs sont peut-être dans l’impossibilité de se connecter au domaine managé, ou les appartenances aux groupes ne sont peut-être pas synchronisées avec Azure AD.*

**Résolution :**

[Vérifiez l’intégrité de votre domaine](check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Dans certains cas, les problèmes de configuration peuvent empêcher Microsoft de synchroniser votre domaine managé. Si vous êtes en mesure de résoudre les alertes, patienter deux heures et vérifiez si la synchronisation est terminée.

Voici quelques-unes des raisons courantes de l’arrêt de la synchronisation sur des domaines gérés :
- La connexion réseau est bloquée sur le domaine managé. Pour en savoir plus sur la vérification de votre réseau afin de détecter des problèmes, lisez [Résoudre les problèmes relatifs aux groupes de sécurité réseau](alert-nsg.md) et [Configuration réseau requise pour Azure AD Domain Services](network-considerations.md).
-  La synchronisation de mot de passe n’a jamais configurée ou terminée. Pour configurer la synchronisation de mot de passe lisez [cet article](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501 : Il n’y a pas eu de sauvegarde depuis un certain temps

**Message d'alerte :**

*La dernière sauvegarde du domaine managé a eu lieu le [date].*

**Résolution :**

[Vérifiez l’intégrité de votre domaine](check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Dans certains cas, les problèmes de configuration peuvent empêcher Microsoft de sauvegarder votre domaine managé. Si vous êtes en mesure de résoudre les alertes, patienter deux heures et vérifiez si la sauvegarde est terminée.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503 : Suspension en raison de l’abonnement désactivé

**Message d'alerte :**

*Le domaine managé est suspendu, car l’abonnement Azure associé au domaine n’est pas actif.*

**Résolution :**

> [!WARNING]
> Si votre domaine managé est suspendu pour une période prolongée, il risque d’être supprimé. Le mieux est de traiter la suspension aussi rapidement que possible. Pour en savoir plus, consultez [cet article](suspension.md).

Pour restaurer votre service, [renouvelez l’abonnement Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) associé à votre domaine managé.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504 : Suspension en raison d’une configuration non valide

**Message d'alerte :**

*Le domaine managé est suspendu en raison d’une configuration non valide. Le service n’a pas pu gérer, corriger ou mettre à jour les contrôleurs du domaine managé depuis un certain temps.*

**Résolution :**

> [!WARNING]
> Si votre domaine managé est suspendu pour une période prolongée, il risque d’être supprimé. Le mieux est de traiter la suspension aussi rapidement que possible. Pour en savoir plus, consultez [cet article](suspension.md).

[Vérifiez l’intégrité de votre domaine](check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Si vous pouvez résoudre une de ces alertes, faites-le. Ensuite, contactez le support pour réactiver votre abonnement.


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](contact-us.md).
