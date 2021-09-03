---
title: Diagnostic des problèmes d’Azure Virtual Desktop – Azure
description: Guide pratique pour utiliser la fonctionnalité de diagnostic d’Azure Virtual Desktop afin de diagnostiquer des problèmes.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0154bb61dcfbcf8024f09052d7a199ac63f71d8e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412538"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Identification et diagnostic des problèmes d’Azure Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Azure Virtual Desktop offre une fonctionnalité de diagnostic qui permet à l’administrateur d’identifier les problèmes sur une seule interface. Pour plus d’informations sur les fonctionnalités de diagnostic d’Azure Virtual Desktop, consultez [Utilisation de Log Analytics pour la fonctionnalité de diagnostic](diagnostics-log-analytics.md).

Les connexions qui n’atteignent pas Azure Virtual Desktop ne figurent pas dans les résultats de diagnostic, car le service de rôle de diagnostic fait partie d’Azure Virtual Desktop. Des problèmes de connexion à Azure Virtual Desktop peuvent survenir lorsque l’utilisateur final rencontre des problèmes de connectivité au réseau.

## <a name="common-error-scenarios"></a>Scénarios d’erreur courants

La table WVDErrors effectue le suivi des erreurs pour tous les types d’activités. La colonne appelée « ServiceError » fournit un indicateur supplémentaire : « True » ou « False ». Cet indicateur vous signale si l’erreur est liée au service.

* Si la valeur est « True », l’équipe du service a peut-être déjà étudié ce problème. Si l’expérience utilisateur s’en trouve affectée et que l’erreur s’affiche souvent, nous vous recommandons d’envoyer un ticket de support pour Azure Virtual Desktop.
* Si la valeur est « False », il s’agit peut-être d’une mauvaise configuration que vous pouvez corriger. Le message d’erreur peut vous indiquer par où commencer.

Le tableau suivant répertorie les erreurs courantes que pourraient rencontrer vos administrateurs.

>[!NOTE]
>Cette liste inclut les erreurs les plus courantes et est mise à jour régulièrement. Pour être sûr de disposer des informations les plus à jour, veillez à consulter cet article au moins une fois par mois.

### <a name="connection-error-codes"></a>Codes d’erreur de connexion

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

Pour en savoir plus sur les rôles dans Azure Virtual Desktop, consultez [Environnement Azure Virtual Desktop](environment-setup.md).

Pour obtenir une liste des cmdlets PowerShell disponibles pour Azure Virtual Desktop, consultez le document de [référence PowerShell](/powershell/windows-virtual-desktop/overview).
