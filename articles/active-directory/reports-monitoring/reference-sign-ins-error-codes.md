---
title: Codes d’erreur sur le portail Azure Active Directory | Microsoft Docs
description: Informations de référence des codes d’erreur des rapports d’activité des connexions.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7a025835275169b260dfd1f91b65341b5ba02ff
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294097"
---
# <a name="sign-in-activity-report-error-codes"></a>Codes d’erreur du rapport d’activité de connexion 

Les informations indiquées dans le [rapport sur les connexions des utilisateurs](concept-sign-ins.md) vous permettent de trouver des réponses aux questions suivantes :

- Qui s’est connecté à mon application ?
- Quelles applications ont fait l’objet d’une connexion ?
- Quelles connexions ont échoué et pourquoi ?

Lorsqu’une connexion échoue, vous verrez un code d’erreur correspondant à l’échec. Cet article répertorie les codes d’erreur et leurs descriptions, ainsi qu’une ligne de conduite suggérée, le cas échéant. 

## <a name="how-can-i-display-failed-sign-ins"></a>Comment afficher les connexions en échec ? 

Dans le menu du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory** ou recherchez et sélectionnez **Azure Active Directory** dans n’importe quelle page.

![Sélectionner Azure Active Directory](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

Sous **Surveillance**, sélectionnez **Connexions** pour ouvrir le [Rapport de connexions](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Activité de connexion](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Activité de connexion")

Filtrez le rapport pour afficher tous les échecs de connexion en sélectionnant **Échec** à partir de la zone de liste déroulante **État de la connexion**.

![Activité de connexion](./media/reference-sign-ins-error-codes/06.png "Activité de connexion")

Sélectionnez un élément dans la liste filtrée pour ouvrir le panneau **Détails de l’activité : connexions**. Cette vue vous fournit des informations supplémentaires sur l’événement de connexion ayant échoué, notamment le **code d’erreur de la connexion** et la **raison de l’échec**.

![Activité de connexion](./media/reference-sign-ins-error-codes/05.png "Activité de connexion")

Vous pouvez également accéder par programme aux données de connexion à l’aide de [l’API de création de rapports](concept-reporting-api.md).

## <a name="error-codes"></a>Codes d’erreur


|Error|Description|
|---|---|
|16000|C’est un détail d’implémentation interne et non un état d’erreur. Vous pouvez ignorer sans problème cette référence.|
|20001|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|20012|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|20033|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|40008|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|40009|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|40014|Il existe un problème avec votre fournisseur d’identité fédérée. Contactez votre IDP pour résoudre ce problème.|
|50000|Il existe un problème avec notre service de connexion. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) pour résoudre ce problème.|
|50001|Le nom de principal du service est introuvable dans ce locataire. Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ou si le principal de la ressource est introuvable dans le répertoire ou non valide.|
|50002|La connexion a échoué en raison de l’accès proxy restreint sur le locataire. Si s’agit de votre propre stratégie de locataire, vous pouvez modifier les paramètres de locataire restreints pour résoudre ce problème.|
|50003|La connexion a échoué en raison d’une clé de signature ou d’un certificat manquant. Il n’existe peut-être aucune clé de signature configurée dans l’application. Découvrez les procédures de résolution décrites à la rubrique [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Si le problème persiste, contactez le propriétaire de l’application ou l’administrateur de l’application.|
|50005|L’utilisateur a essayé de se connecter à un appareil à partir d’une plateforme qui n’est pas prise en charge via la stratégie d’accès conditionnel.|
|50006| La vérification de la signature a échoué en raison d’une signature non valide. Découvrez la procédure de résolution décrite à la rubrique [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Si le problème persiste, contactez le propriétaire de l’application ou l’administrateur de l’application.|
|50007|Le certificat de chiffrement de partenaire pour cette application est introuvable. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) auprès de Microsoft pour résoudre ce problème.|
|50008|L’assertion SAML est manquante ou configurée de façon incorrecte dans le jeton. Contactez votre fournisseur de fédération.|
|50010|La validation de l’URI d’audience pour l’application a échoué, car aucune audience de jeton n’a été configurée. Contactez le propriétaire de l’application pour résoudre le problème.|
|50011|L’adresse de réponse est manquante, configurée de façon incorrecte ou elle ne correspond pas aux adresses de réponse configurées pour l’application. Essayez la résolution décrite à la rubrique [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Si le problème persiste, contactez le propriétaire de l’application ou l’administrateur de l’application.|
|50012| Il s’agit d’un message d’erreur générique qui indique que l’authentification a échoué. Cela peut se produire lorsque les informations d’identification ou les revendications d’une requête sont manquantes ou non valides. Vérifiez que la requête est envoyée avec les bonnes informations d’identification et revendications. |
|50013|Assertion non valide pour plusieurs raisons. Par exemple, l’émetteur du jeton ne correspond pas à la version d’API dans l’intervalle de temps valide, le jeton a expiré ou a un format incorrect, ou le jeton d’actualisation dans l’assertion n’est pas un jeton d’actualisation principal.|
|50017|La validation de la certification a échoué pour les raisons suivantes :<ul><li>Le certificat d’émission est introuvable dans la liste de certificats approuvés.</li><li>L’élément CrlSegment attendu est introuvable.</li><li>Le certificat d’émission est introuvable dans la liste de certificats approuvés.</li><li>Le point de distribution CRL delta est configuré sans point de distribution CRL correspondant.</li><li>Impossible de récupérer les segments CRL valides en raison d’un problème de délai d’expiration.</li><li>Impossible de télécharger la liste CRL.</li></ul>Contactez l’administrateur du locataire.|
|50020|L’utilisateur n’est pas autorisé pour l’une des raisons suivantes :<ul><li>L’utilisateur tente de se connecter avec un compte MSA, avec le point de terminaison v1</li><li>L’utilisateur ne se trouve pas dans le locataire.</li></ul> Contactez le propriétaire de l’application.|
|50027|Jeton JWT non valide pour l’une des raisons suivantes :<ul><li>Aucune (sous-)revendication nonce</li><li>Non-concordance de l’identificateur du sujet</li><li>Revendication en double pour idToken</li><li>Émetteur inattendu</li><li>Audience inattendue</li><li>Intervalle de temps non valide </li><li>format de jeton incorrect</li><li>Échec du jeton d’ID externe de l’émetteur lors de la vérification de la signature.</li></ul>Contactez le propriétaire de l’application.|
|50029|URI non valide. Le nom du domaine contient des caractères non valides. Contactez l’administrateur du locataire.|
|50034|L’utilisateur n’existe pas dans le répertoire. Contactez l’administrateur du locataire.|
|50042|La valeur salt nécessaire pour générer un identificateur par paire est manquante dans le principal. Contactez l’administrateur du locataire.|
|50048|L’objet ne correspond pas à la revendication d’émetteur dans l’assertion du client. Contactez l’administrateur du locataire.|
|50050|Le format de la requête est incorrect. Contactez le propriétaire de l’application.|
|50053|Le compte est verrouillé, car l’utilisateur a essayé de se connecter un trop grand nombre de fois avec un ID d’utilisateur ou un mot de passe incorrect.|
|50055|Mot de passe non valide, mot de passe arrivé à expiration entré.|
|50056|Le mot de passe est nul ou non valide. Le mot de passe pour cet utilisateur n’existe pas dans le magasin.|
|50057|Le compte d’utilisateur est désactivé. Le compte a été désactivé par un administrateur.|
|50058|L’application a essayé d’effectuer une connexion en mode silencieux, et cela n’a pas fonctionné pour l’utilisateur. L’application a besoin de démarrer un flux interactif donnant aux utilisateurs une option de connexion. Contactez le propriétaire de l’application.|
|50059|L’utilisateur n’existe pas dans le répertoire. Contactez l’administrateur du locataire.|
|50061|La requête de déconnexion n’est pas valide. Contactez le propriétaire de l’application.|
|50072|L’utilisateur doit s’inscrire pour l’authentification à deux facteurs (interactive).|
|50074|L’utilisateur n’a pas réussi la vérification de l’authentification multifacteur.|
|50076|L’utilisateur n’a pas réussi le test de l’authentification multifacteur (non interactif).|
|50078|L’authentification multifacteur présentée est arrivée à expiration. Vous devez actualiser votre authentification multifacteur pour y accéder.|
|50079|L’utilisateur doit s’inscrire pour l’authentification à deux facteurs (connexions non interactives).|
|50085|Le jeton d’actualisation a besoin d’une connexion IDP sociale. Demandez à l’utilisateur d’essayer à nouveau de se connecter avec son nom d’utilisateur et son mot de passe.|
|50089|Échec du jeton de flux. L’authentification a échoué. Demandez à l’utilisateur d’essayer à nouveau de se connecter avec son nom d’utilisateur et son mot de passe|
|50097|Authentification de l’appareil requise. Cela peut se produire si les revendications DeviceId ou DeviceAltSecId sont **nulles**, ou il n’existe aucun appareil correspondant à l’identificateur.|
|50099|La signature JWT n’est pas valide. Contactez le propriétaire de l’application.|
|50105|L’utilisateur connecté n’est pas affecté à un rôle pour l’application concernée. Affectez l’utilisateur à l’application. Pour plus d’informations : [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|L’objet de domaine de fédération requis n’existe pas. Contactez l’administrateur du locataire.|
|50120|Problème avec l’en-tête JWT. Contactez l’administrateur du locataire.|
|50124|La transformation des revendications contient un paramètre d’entrée non valide. Contactez l’administrateur du locataire pour mettre à jour la stratégie.|
|50125|La connexion a été interrompue en raison d’une réinitialisation de mot de passe ou d’une entrée d’inscription de mot de passe.|
|50126|Nom d’utilisateur ou mot de passe non valide, ou nom d’utilisateur ou mot de passe local non valide.|
|50127|L’utilisateur a besoin d’installer une application de répartiteur pour accéder à ce contenu.|
|50128|Nom de domaine non valide. Informations d’identification de locataire introuvables dans la requête ou déduites des informations d’identification fournies.|
|50129|L’appareil n’est pas rattaché à l’espace de travail. Un **rattachement à l’espace de travail** est nécessaire pour inscrire l’appareil.|
|50130|La valeur de revendication ne peut pas être interprétée en tant que méthode d’authentification connue.|
|50131|Utilisation dans différentes erreurs d’accès conditionnel. Par exemple, État d’appareil Windows incorrect, requête bloquée en raison d’une activité suspecte, d’une stratégie d’accès et de décisions de stratégie de sécurité.|
|50132|Les informations d’identification ont été révoquées pour les raisons suivantes :<ul><li>Artefact d’authentification unique non valide ou expiré</li><li>Session pas assez récente pour l’application</li><li>Une requête de connexion en mode silencieux a été envoyée, mais la session de l’utilisateur auprès d’Azure AD n’est pas valide ou a expiré.</li></ul>|
|50133|La session n’est pas valide en raison de l’expiration ou de la modification récente du mot de passe.|
|50135|La modification du mot de passe est nécessaire en raison du risque du compte.|
|50136|Redirection de session MSA vers une application ; une seule session MSA détectée. |
|50140|Cette erreur s’est produite suite à l’interruption de la fonction « Maintenir la connexion » lors de la connexion de l’utilisateur. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) avec l’ID de corrélation, l’ID de requête et le code d’erreur pour obtenir plus de détails. |
|50143|Incompatibilité de session : la session n’est pas valide, car le client de l’utilisateur ne correspond pas à l’indication de domaine en raison d’une différence entre les ressources. Pour plus d’informations,  [ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) en précisant l’ID de corrélation, l’ID de demande et le code d’erreur.|
|50144|Le mot de passe Active Directory de l’utilisateur est arrivé à expiration. Générez un nouveau mot de passe pour l’utilisateur ou demandez à l’utilisateur final d’utiliser l’outil de réinitialisation en libre service.|
|50146|Cette application doit être configurée avec une clé de signature spécifique. Elle n’est configuré avec aucune clé, ou la clé a expiré ou n’est pas encore valide. Contactez le propriétaire de l’application.|
|50148|L’élément code_verifier ne correspond pas à l’élément code_challenge fourni dans la requête d’autorisation pour PKCE. Contactez le développeur de l’application. |
|50155|L’authentification de l’appareil a échoué pour cet utilisateur.|
|50158|La vérification de sécurité externe n’a pas abouti.|
|50161|Les revendications envoyées par un fournisseur externe ne sont pas suffisantes, ou il manque une revendication demandée à un fournisseur externe.|
|50166|L’envoi de la requête au fournisseur de revendications a échoué.|
|50169|Le domaine n’est pas un domaine configuré de l’espace de noms de service actuel.|
|50172|Le fournisseur de revendications externe n’est pas approuvé. Contactez l’administrateur du locataire|
|50173|Un jeton d’authentification récent est nécessaire. Demandez à l’utilisateur de se connecter à nouveau à l’aide de nouvelles informations d’identification.|
|50177|La vérification externe n’est pas prise en charge pour les utilisateurs PassThrough.|
|50178|Le contrôle de session n’est pas pris en charge pour les utilisateurs PassThrough.|
|50180|L’authentification Windows intégrée est nécessaire. Activez le locataire pour l’authentification unique transparente.|
|50181|Défaillance liée au mot de passe à usage unique lors de la connexion. |
|50201|Ce message d’invite s’affiche lors de la connexion quand des informations supplémentaires doivent être fournies à l’utilisateur.|
|51001|L’indicateur de domaine n’est pas présent avec identificateur de sécurité local - UPN local.|
|51004|Le compte d’utilisateur n’existe pas dans le répertoire.|
|51006|L’authentification Windows intégrée est nécessaire. L’utilisateur s’est connecté à l’aide d’un jeton de session qui ne se trouve pas dans la revendication. Demandez à l’utilisateur de se connecter à nouveau.|
|52004|L’utilisateur n’a pas donné son consentement pour l’accès aux ressources de LinkedIn. |
|53000|Une stratégie d’accès conditionnel nécessite un appareil conforme, or l’appareil n’est pas conforme. Demandez à l’utilisateur d’inscrire ses appareils auprès d’un fournisseur approuvé de gestion des appareils mobiles, comme Intune.|
|53001|Une stratégie d’accès conditionnel nécessite un appareil de jonction de domaine, or l’appareil n’est pas conforme. Demandez à l’utilisateur d’utiliser un appareil de jonction de domaine.|
|53002|L’application utilisée n’est pas une application approuvée pour l’accès conditionnel. L’utilisateur doit utiliser une des options de la liste des applications approuvées afin d’obtenir l’accès.|
|53003|L’accès a été bloqué en raison de stratégies d’accès conditionnel.|
|53004|L’utilisateur doit terminer le processus d’inscription de l’authentification multifacteur pour accéder à ce contenu. L’utilisateur doit s’inscrire à l’authentification multifacteur.|
|65 001|L’application X n’est pas autorisée à accéder à l’application Y, ou l’autorisation a été révoquée. Ou l’utilisateur ou l’administrateur n’ont pas accepté d’utiliser l’application avec ID X. Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource. Ou l’utilisateur ou l’administrateur n’ont pas accepté d’utiliser l’application avec ID X. Envoyez une demande d’autorisation à l’administrateur du locataire pour agir au nom de l’application : Y pour la ressource : Z.|
|65004|L’utilisateur a refusé de donner son consentement pour accéder à l’application. Demandez à l’utilisateur de réessayer de se connecter et de donner son consentement à l’application.|
|65005|La liste d’accès aux ressources requise par l’application ne contient pas d’applications détectables par la ressource ; l’application cliente a demandé un accès à la ressource qui n’était pas spécifié dans sa liste d’accès aux ressources requise ; le service Graph a renvoyé une requête incorrecte ou la ressource est introuvable. Si l’application prend en charge SAML, vous avez peut-être configuré l’application avec un identificateur incorrect (entité). Testez la résolution décrite pour SAML en utilisant le lien ci-dessous : [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Octroi non valide pour l’une des raisons suivantes :<ul><li>L’assertion SAML 2.0 demandée a une méthode de confirmation d’objet non valide.</li><li>Le flux OnBehalfOf de l’application n’est pas pris en charge sur V2.</li><li>Le jeton d’actualisation principal n’est pas signé avec la clé de session.</li><li>Le jeton d’actualisation externe n’est pas valide.</li><li>L’octroi d’accès a été obtenu pour un autre locataire.</li></ul>|
|70001|L’application nommée X est introuvable dans le locataire nommé Y. Cela peut se produire si l’application associée à l’identificateur X n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être configuré de manière incorrecte la valeur d’identificateur de l’application ou envoyé votre requête d’authentification à un locataire incorrect.|
|70002|L’application a renvoyé des informations d’identification client non valides. Contactez le propriétaire de l’application.|
|70003|L’application a renvoyé un type d’octroi non pris en charge. Contactez le propriétaire de l’application.|
|70004|L’application a renvoyé un URI de redirection non valide. L’adresse de redirection spécifiée par le client ne correspond à aucune adresse configurée ni à aucune adresse de la liste d’approbation OIDC. Contactez le propriétaire de l’application.|
|70005|L’application a renvoyé un type de réponse non pris en charge pour les raisons suivantes :<ul><li>Le type de réponse « jeton » n’est pas activé pour l’application.</li><li>Le type de réponse « id_token » requiert la portée « OpenID ». La réponse contient une valeur de paramètre OAuth non prise en charge dans l’élément wctx codé.</li></ul>Contactez le propriétaire de l’application.|
|70007|L’application a renvoyé une valeur non prise en charge pour « response_mode » lors de la demande d’un jeton. Contactez le propriétaire de l’application.|
|70008|Le code d’autorisation ou le jeton d’actualisation fourni a expiré et a été révoqué. Demandez à l’utilisateur de réessayer de se connecter.|
|70011|La portée demandée par l’application n’est pas valide. Contactez le propriétaire de l’application.|
|70012|Une erreur de serveur s’est produite lors de l’authentification d’un utilisateur de compte de service administré (consommateur). Retentez la connexion, et si le problème persiste, [ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Le code de vérification n’est pas valide, car l’utilisateur a saisi un code utilisateur incorrect pour le flux de code d’appareil. L’autorisation n’est pas approuvée.|
|70019|Le code de vérification a expiré. Demandez à l’utilisateur de réessayer de se connecter.|
|70037|La réponse donnée à la question de vérification est incorrecte. La session d’authentification à distance a été refusée.|
|70043|La gestion des sessions d'accès conditionnel Azure force l'expiration de la session|
|70044|La gestion des sessions d'accès conditionnel Azure force l'expiration de la session|
|75001|Une erreur s’est produite lors de la liaison de message SAML.|
|75003|L’application a renvoyé une erreur relative à une liaison non prise en charge (impossible d’envoyer une réponse de protocole SAML via des liaisons autres que HTTP POST). Contactez le propriétaire de l’application.|
|75005|Azure AD ne prend pas en charge les demandes SAML envoyées par l’application pour l’authentification unique. Contactez le propriétaire de l’application.|
|75008|La requête provenant de l’application a été refusée, car la requête SAML avait une destination inattendue. Contactez le propriétaire de l’application.|
|75011|La méthode d’authentification de l’utilisateur auprès du service ne correspond pas à la méthode d’authentification demandée. Contactez le propriétaire de l’application.|
|75016|Dans la requête d’authentification SAML2, NameIdPolicy n’est pas valide. Contactez le propriétaire de l’application.|
|80001|L’Agent d’authentification ne peut pas se connecter à Active Directory. Assurez-vous que l’agent d’authentification est installé sur un ordinateur de jonction de domaine ayant une visibilité sur un contrôleur de domaine qui peut répondre à la requête de connexion de l’utilisateur.|
|80002|Erreur interne. La requête de validation du mot de passe est arrivée à expiration. Nous n’avons pas pu envoyer la requête d’authentification au service d’identité hybride interne. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) pour plus d’informations sur l’erreur.|
|80003|Réponse non valide reçue par l’Agent d’authentification. Une erreur inconnue s’est produite lors de la tentative d’authentification auprès de l’instance Active Directory locale. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) pour plus d’informations sur l’erreur.|
|80005|Agent d’authentification : Une erreur inconnue s’est produite lors du traitement de la réponse provenant de l’agent d’authentification. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) pour plus d’informations sur l’erreur.|
|80007|L’Agent d’authentification ne peut pas valider le mot de passe.|
|80010|L’Agent d’authentification ne peut pas déchiffrer le mot de passe. |
|80011|L’agent d’authentification n’a pas pu récupérer la clé de déchiffrement.|
|80012|Les utilisateurs ont essayé de se connecter en dehors des heures autorisées (spécifiées dans AD).|
|80013|La tentative d’authentification n’a pas abouti en raison du temps de décalage entre l’ordinateur exécutant l’agent d’authentification et Active Directory. Résolvez les problèmes de synchronisation.|
|80014|L’agent d’authentification a expiré. [Ouvrez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md) avec le code d’erreur, l’ID de corrélation et la date/l’heure pour obtenir plus de détails sur cette erreur.|
|81001|Le ticket Kerberos de l’utilisateur est trop volumineux. Cela peut se produire si l’utilisateur appartient à trop de groupes, auquel cas le ticket Kerberos contient trop d’appartenances aux groupes. Réduisez les appartenances à des groupes de l’utilisateur, puis réessayez.|
|81005|Le package d’authentification n’est pas pris en charge.|
|81007|Le locataire n’est pas activé pour l’authentification unique transparente.|
|81012|Ce n’est pas un état d’erreur. Cela indique que l’utilisateur qui tente de se connecter à Azure AD est différent de l’utilisateur connecté à l’appareil. Vous pouvez ignorer sans problème ce code dans les journaux d’activité.|
|90010|La requête n’est pas prise en charge pour diverses raisons. Par exemple, la requête est effectuée à l’aide d’une méthode de requête non prise en charge (seule la méthode POST est prise en charge) ou l’algorithme de signature de jeton demandé n’est pas pris en charge. Contactez le développeur de l’application.|
|90014| Un champ obligatoire du message de protocole n’a pas été renseigné. Contactez le propriétaire de l’application. Si vous êtes le propriétaire de l’application, vérifiez que vous disposez de tous les paramètres nécessaires pour la requête de connexion. |
|90051| Jeton de délégation non valide. L’ID cloud national non valide ({cloudId}) a été spécifié.|
|90072| Le compte doit d’abord être ajouté comme utilisateur externe dans le locataire. Déconnectez-vous, puis connectez-vous avec un autre compte Azure AD.|
|90094| L’application a demandé des autorisations pour lesquelles l’utilisateur connecté n’est pas autorisé à donner son consentement, et celui-ci a été bloqué. |
|90095| L’application a demandé des autorisations pour lesquelles l’utilisateur connecté n’est pas autorisé à donner son consentement, et l’utilisateur a affiché le formulaire de [demande de consentement d’administrateur](../manage-apps/configure-admin-consent-workflow.md). |
|500011| Le principal de ressource nommé <site address> est introuvable dans le locataire nommé <tenant ID>. Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être envoyé votre demande d’authentification au locataire incorrect.|
|500021| Le locataire est restreint par le proxy de l’entreprise. L’accès aux ressources est refusé.|
|500121| Échec de l’authentification lors d’une requête d’authentification forte.|
|500133| L’intervalle de temps de l’assertion n’est pas valide. Vérifiez que le jeton d’accès n’est pas arrivé à expiration avant de l’utiliser pour l’assertion de l’utilisateur, ou demandez-en un nouveau.|
|530021|L’application ne répond pas aux conditions d’accès conditionnel des applications approuvées.|
|530032|Bloqué par la stratégie de sécurité.| 
|700016|L’application associée à l’identificateur « {appIdentifier} » est introuvable dans le répertoire « {tenantName} ». Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être envoyé votre requête d’authentification au locataire incorrect.|
|900432|Le client confidentiel n’est pas pris en charge dans une requête intercloud.|
|5000811|Impossible de vérifier la signature du jeton SAML. L’identificateur de clé de signature ne correspond à aucune clé inscrite valide.|
|7000215|Une clé secrète client non valide a été fournie.|
|7000218|Le corps de la requête doit contenir le paramètre « client_assertion » ou « client_secret ».|


## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble des rapports de connexion](concept-sign-ins.md)
* [Accès par programmation aux rapports Azure AD](concept-reporting-api.md)
