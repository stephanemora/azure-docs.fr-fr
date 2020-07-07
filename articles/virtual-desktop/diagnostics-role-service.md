---
title: Problèmes de diagnostic de Windows Virtual Desktop – Azure
description: Comment utiliser la fonctionnalité de diagnostic de Windows Virtual Desktop pour diagnostiquer des problèmes.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ead16c655d4790e81931371e67da8106dabf83e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200543"
---
# <a name="identify-and-diagnose-issues"></a>Identifier et diagnostiquer les problèmes

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop offre une fonctionnalité de diagnostic qui permet à l’administrateur d’identifier les problèmes via une seule interface. Pour en savoir plus sur les fonctionnalités de diagnostic de Windows Virtual Desktop, consultez [Utiliser Log Analytics pour la fonctionnalité de diagnostic](diagnostics-log-analytics.md).

Les connexions qui n’atteignent pas Windows Virtual Desktop ne figureront pas dans les résultats de diagnostic, car le service de rôle de diagnostics fait partie de Windows Virtual Desktop. Des problèmes de connexion à Windows Virtual Desktop peuvent survenir lorsque l’utilisateur rencontre des problèmes de connectivité au réseau.

## <a name="common-error-scenarios"></a>Scénarios d’erreur courants

Les scénarios d’erreur sont classés en interne au service et en externe à Windows Virtual Desktop.

* Problème interne : spécifie des scénarios qui ne peuvent pas être atténués par le client et qui doivent être résolus en tant que problèmes de support. Lorsque vous formulez des commentaires via [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), incluez l’ID de corrélation, puis spécifiez une période approximative de survenance du problème.
* Problème externe : a trait à scénarios qui peuvent être atténués par le client. Il s’agit de problèmes externes à Windows Virtual Desktop.

Le tableau suivant répertorie les erreurs courantes que pourraient rencontrer vos administrateurs.

>[!NOTE]
>Cette liste inclut les erreurs les plus courantes et est mise à jour régulièrement. Pour être sûr de disposer des informations les plus à jour, veillez à consulter cet article au moins une fois par mois.

## <a name="management-errors"></a>Erreurs de gestion

|Message d’erreur|Solution suggérée|
|---|---|
|La création de la clé d’inscription a échoué. |Impossible de créer le jeton d’inscription. Réessayez de le créer avec un délai d’expiration plus court (entre 1 heure et 1 mois). |
|La suppression de la clé d’inscription a échoué.|Impossible de supprimer le jeton d’inscription. Réessayez de le supprimer. Si cela ne fonctionne toujours pas, utilisez PowerShell pour vérifier si le jeton est toujours présent. Si c’est le cas, supprimez-le avec PowerShell.|
|Le changement du mode maintenance de l’hôte de session a échoué |Impossible de modifier le mode maintenance sur la machine virtuelle. Vérifiez l’état de la machine virtuelle. Si la machine virtuelle est indisponible, il n’est pas possible de modifier le mode maintenance.|
|La déconnexion de sessions utilisateur a échoué |Impossible de déconnecter l’utilisateur de la machine virtuelle. Vérifiez l’état de la machine virtuelle. Si la machine virtuelle est indisponible, il n’est pas possible de déconnecter la session utilisateur. Si la machine virtuelle est disponible, vérifiez l’état de la session utilisateur pour voir si elle est déconnectée. |
|La fermeture de session de tous les utilisateurs de l’hôte de session a échoué |Impossible de déconnecter les utilisateurs de la machine virtuelle. Vérifiez l’état de la machine virtuelle. S’il n’est pas disponible, il n’est pas possible de déconnecter les utilisateurs. Vérifiez l’état de la session utilisateur pour voir s’ils sont déconnectés. Vous pouvez forcer la déconnexion avec PowerShell. |
|La dissociation de l’utilisateur du groupe d’applications a échoué|Impossible d’annuler la publication d’un groupe d’applications pour un utilisateur. Vérifiez si l’utilisateur est disponible sur Azure AD. Vérifiez si l’utilisateur fait partie d’un groupe d’utilisateurs sur lequel le groupe d’applications est publié. |
|Une erreur s’est produite lors de la récupération des emplacements disponibles |Vérifiez l’emplacement de la machine virtuelle utilisée dans l’Assistant Création d’un pool d’hôtes. Si l’image n’est pas disponible dans cet emplacement, ajoutez une image à cet emplacement ou choisissez un autre emplacement de machine virtuelle. |

### <a name="external-connection-error-codes"></a>Codes d’erreur de connexion externe

|Code numérique|Code d'erreur|Solution suggérée|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L’hôte de la session n’est pas correctement joint à Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Les connexions ont échoué car l’hôte de la session est indisponible. Vérifiez l’intégrité de l’hôte de la session.|
|-2146233088|ConnectionFailedClientDisconnect|Si vous rencontrez souvent cette erreur, assurez-vous que l’ordinateur est connecté au réseau.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La session à laquelle l’utilisateur hôte a tenté de se connecter n’est pas viable. Déboguez la machine virtuelle.|
|-2146233088|ConnectionFailedUserNotAuthorized|L’utilisateur n’est pas autorisé à accéder à l’application publiée ou au bureau. Cette erreur peut apparaître après la suppression de ressources publiées par l’administrateur. Demandez à l’utilisateur d’actualiser le flux dans l’application de bureau à distance.|
|2|FileNotFound|L’application à laquelle l’utilisateur tente d’accéder est incorrectement installée ou son chemin d’accès est invalide.<br><br>Lors de la publication de nouvelles applications alors que l’utilisateur dispose d’une session active, celui-ci ne peut pas accéder à cette application. Vous devez arrêter et redémarrer la session pour que l’utilisateur puisse accéder à l’application. |
|3|InvalidCredentials|Le nom d’utilisateur ou le mot de passe saisi par l’utilisateur ne correspond à aucun nom d’utilisateur ou mot de passe existant. Vérifiez que vous n’avez fait aucune faute de frappe et réessayez.|
|8|ConnectionBroken|La connexion entre le client et passerelle ou serveur a été interrompue. Aucune action n’est nécessaire, sauf si cela se produit de manière inattendue.|
|14|UnexpectedNetworkDisconnect|La connexion au réseau a été perdue. Demandez à l’utilisateur de se reconnecter.|
|24|ReverseConnectFailed|La machine virtuelle hôte n’a aucune une ligne directe vers la passerelle Bureau à distance. Vérifiez que l’adresse IP de la passerelle peut être résolue.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les rôles dans Windows Virtual Desktop, consultez [Environnement Windows Virtual Desktop](environment-setup.md).

Pour obtenir une liste des cmdlets PowerShell disponibles pour Windows Virtual Desktop, consultez le document de [référence PowerShell](/powershell/windows-virtual-desktop/overview).
