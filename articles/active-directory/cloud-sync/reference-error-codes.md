---
title: Codes d’erreur et descriptions de la synchronisation cloud Azure AD Connect
description: article de référence pour les codes d’erreur de synchronisation cloud
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650938"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Codes d’erreur et descriptions de la synchronisation cloud Azure AD Connect
La liste suivante répertorie les codes d’erreur et leur description.


## <a name="error-codes"></a>Codes d’erreur

|Code d'erreur|Détails|Scénario|Résolution|
|-----|-----|-----|-----|
|TimeOut|Message d’erreur : Nous avons détecté une erreur relative au délai d’expiration de la requête lors de la prise de contact avec l’agent local et de la synchronisation de votre configuration. Pour plus d’informations sur les autres problèmes liés à votre agent de synchronisation cloud, consultez notre aide à la résolution des problèmes.|Le délai d’attente de la requête adressée à HIS a expiré. La valeur actuelle du délai d’attente est de 10 minutes.|Consultez nos [instructions pour la résolution des problèmes](how-to-troubleshoot.md).|
|HybridSynchronizationActiveDirectoryInternalServerError|Message d’erreur : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support et fournissez l’identifiant de travail suivant : AD2AADProvisioning.30b500eaf9c643b2b78804e80c1421fe.5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Informations supplémentaires : Le traitement de la requête HTTP a entraîné une exception. |Impossible de traiter les paramètres reçus dans la requête SCIM en une demande de recherche.|Pour plus d’informations, consultez la réponse HTTP renvoyée par la propriété « Response » de cette exception.|
|HybridIdentityServiceNoAgentsAssigned|Message d’erreur : Nous ne trouvons pas d’agent actif pour le domaine que vous essayez de synchroniser. Vérifiez si les agents ont été supprimés. Si c’est le cas, réinstallez l’agent.|Aucun agent n’est en cours d’exécution. Les agents ont probablement été supprimés. Inscrivez un nouvel agent.|Dans ce cas, vous ne verrez aucun agent affecté au domaine dans le portail.|
|HybridIdentityServiceNoActiveAgents|Message d’erreur : Nous ne trouvons pas d’agent actif pour le domaine que vous essayez de synchroniser. Vérifiez si l’agent fonctionne en accédant au serveur où l’agent est installé, puis vérifiez si « Agent de synchronisation cloud Microsoft Azure AD » sous Services est fonctionne.|Les agents ne sont pas à l’écoute du point de terminaison ServiceBus. [L’agent se trouve derrière un pare-feu qui n’autorise pas les connexions à Service Bus](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server).|
|HybridIdentityServiceInvalidResource|Message d’erreur : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support et fournissez l’identifiant de travail suivant : AD2AADProvisioning.3a2a0d8418f34f54a03da5b70b1f7b0c.d583d090-9cd3-4d0a-aee6-8d666658c3e9. Informations supplémentaires : Il semble qu’il y ait un problème avec votre configuration de la synchronisation cloud. Réinscrivez votre agent de synchronisation cloud sur votre domaine AD local et redémarrez la configuration à partir du portail Azure.|Le nom de la ressource doit être défini de façon à ce que HIS sache quel agent contacter.|Réinscrivez votre agent de synchronisation cloud sur votre domaine AD local et redémarrez la configuration à partir du portail Azure.|
|HybridIdentityServiceAgentSignalingError|Message d’erreur : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support et fournissez l’identifiant de travail suivant : AD2AADProvisioning.92d2e8750f37407fa2301c9e52ad7e9b.efb835ef-62e8-42e3-b495-18d5272eb3f9. Informations supplémentaires : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration).|Service Bus n’est pas en mesure d’envoyer un message à l’agent. Peut être une panne dans Service Bus, ou l’agent ne répond pas.|Si le problème persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration).|
|AzureDirectoryServiceServerBusy|Message d’erreur : Une erreur est survenue. Code d’erreur : 81. Description de l’erreur : Azure Active Directory est actuellement occupé. Cette opération fera automatiquement l’objet d’une nouvelle tentative. Si le problème persiste pendant plus de 24 heures, contactez l’équipe du support technique. ID de suivi : 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f. Nom du serveur :|Azure Active Directory est actuellement occupé.|Si le problème persiste pendant plus de 24 heures, contactez l’équipe du support technique.|
|AzureActiveDirectoryInvalidCredential|Message d’erreur : Nous avons détecté un problème avec le compte de service utilisé pour exécuter la synchronisation cloud Azure AD Connect. Vous pouvez réparer le compte de service cloud en suivant les instructions fournies [ici](./how-to-troubleshoot.md). Si l’erreur persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration). Détails d’erreur supplémentaires : CredentialsInvalid AADSTS50034 : Le compte d’utilisateur {EmailHidden} n’existe pas dans le répertoire skydrive365.onmicrosoft.com. Pour se connecter à cette application, le compte doit être ajouté au répertoire. ID de suivi : 14b63033-3bc9-4bd4-b871-5eb4b3500200. ID de corrélation : 57d93ed1-be4d-483c-997c-a3b6f03deb00. Timestamp : 2021-01-12 21:08:29Z |Cette erreur est renvoyée lorsque le compte de service de synchronisation ADToAADSyncServiceAccount n’existe pas dans le locataire. Cela peut être dû à une suppression accidentelle du compte.|Utilisez [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) pour corriger le compte de service.|
|AzureActiveDirectoryExpiredCredentials|Message d’erreur : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration). Détails d’erreur supplémentaires : CredentialsExpired AADSTS50055 : Le mot de passe a expiré. ID de suivi : 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00. ID de corrélation : 1c69b196-1c3a-4381-9187-c84747807155. Timestamp : 2021-01-12 20:59:31Z | Response status code does not indicate success: 401 (Non autorisé).|Les informations d’identification du compte de service AAD Sync ont expiré.|Vous pouvez réparer le compte de service cloud en suivant les instructions fournies à la page https://go.microsoft.com/fwlink/?linkid=2150988. Si l’erreur persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration).  Détails d’erreur supplémentaires : Les informations d’identification d’administrateur de votre locataire Azure Active Directory ont été échangées pour un jeton OAuth qui a expiré depuis.|
|AzureActiveDirectoryAuthenticationFailed|Message d’erreur : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support et fournissez l’identifiant de travail suivant : AD2AADProvisioning.60b943e88f234db2b887f8cb91dee87c.707be0d2-c6a9-405d-a3b9-de87761dc3ac. Informations supplémentaires : Nous n’avons pas été en mesure de traiter cette demande à ce stade. Si le problème persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration). Détails d’erreur supplémentaires : UnexpectedError.|Erreur inconnue.|Si le problème persiste, contactez le support avec l’ID de travail (à partir du volet d’état de votre configuration).|

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)