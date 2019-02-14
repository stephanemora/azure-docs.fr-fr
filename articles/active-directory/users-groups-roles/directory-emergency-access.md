---
title: Gérer des comptes d’accès d’urgence dans Azure AD | Microsoft Docs
description: Cet article explique comment utiliser des comptes d’accès d’urgence pour éviter de vous retrouver empêché par inadvertance d’accéder à votre client Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/21/2018
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d2a47360808c7c1c36d5e82b98582d9f6144fa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176949"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gérer des comptes d’accès d’urgence dans Azure AD

Il est important d’éviter de bloquer par inadvertance votre client Azure Active Directory (Azure AD), car vous ne pouvez pas vous connecter ou activer un compte d’utilisateur individuel existant en tant qu’administrateur. Vous pouvez pallier l’impact d’un défaut d’accès administratif en créant plusieurs *comptes d’accès d’urgence* dans votre client.

Les comptes d’accès d’urgence sont hautement privilégiés et ne sont pas affectés à des personnes spécifiques. Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours » où il est impossible d’utiliser des comptes d’administration normaux. Les organisations doivent se fixer pour objectif de limiter l’utilisation du compte d’urgence au temps strictement nécessaire.

Cet article fournit des instructions pour la gestion des comptes d’accès d’urgence dans Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>Quand utiliseriez-vous un compte d’accès d’urgence ?

Une organisation peut avoir recours à un compte d’accès d’urgence dans les situations décrites ici.

- Les comptes d’utilisateurs sont fédérés, et la fédération est actuellement indisponible en raison d’un dysfonctionnement du réseau cellulaire ou d’une panne du fournisseur d’identité. Par exemple, si l’hôte du fournisseur d’identité dans votre environnement s’est arrêté de fonctionner, les utilisateurs risquent de ne pas pouvoir se connecter lors de la redirection d’Azure AD vers leur fournisseur d’identité.
- Les administrateurs sont inscrits par le biais d’Azure Multi-Factor Authentication, et tous leurs appareils individuels ou le service sont indisponibles. Les utilisateurs peuvent se retrouver dans l’incapacité de procéder à l’authentification multifacteur pour activer un rôle. Par exemple, une panne de réseau cellulaire les empêche de répondre aux appels téléphoniques ou de recevoir des SMS, les deux seuls mécanismes d’authentification à leur disposition qu’ils ont enregistrés pour leur appareil.
- La personne disposant de l’accès administrateur général le plus récent a quitté l’organisation. Azure AD empêche la suppression du dernier compte d’administrateur général, mais n’empêche pas ce compte d’être supprimé ou désactivé localement. Chacune de ces situations peut rendre impossible la récupération du compte par l’organisation.
- Un événement imprévu, comme une catastrophe naturelle, au cours duquel les téléphones mobiles ou d’autres réseaux sont indisponibles. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Créer deux comptes d’accès d’urgence basés sur le cloud

Créez plusieurs comptes d’accès d’urgence. Ces comptes doivent être des comptes uniquement cloud utilisant le domaine \*. onmicrosoft.com, et ne pas être fédérés ou synchronisés à partir d’un environnement local.

Lors de la configuration de ces comptes, les conditions suivantes doivent être remplies :

- Les comptes d’accès d’urgence ne doivent être associés à aucun utilisateur au sein de l’organisation. Veillez à ce que vos comptes d’accès d’urgence ne soient pas connectés à un matériel fourni à un employé et voyageant avec celui-ci, tel un téléphone mobile, un module de sécurité matériel, ou d’autres informations d’identification propres à l’employé. Cette précaution de sécurité couvre les cas où un employé n’est pas joignable alors que les informations d’identification doivent être fournies. Il est important de s’assurer que tous les appareils inscrits sont conservés dans un endroit sûr et connu, disposant de plusieurs moyens de communication avec Azure AD.
- Le mécanisme d’authentification utilisé pour un compte d’accès d’urgence doit être distinct de celui utilisé par d’autres comptes administratifs, y compris d’autres comptes d’accès d’urgence.  Par exemple, si votre administrateur normal se connecte via une MFA locale, Azure MFA devrait être un mécanisme différent.  En revanche, si Azure MFA est votre principal composant d’authentification pour vos comptes administratifs, envisagez une approche différente pour ces derniers, telle que l’utilisation d’un accès conditionnel avec un fournisseur de MFA tiers.
- L’appareil ou les informations d’identification ne doivent pas expirer ou faire potentiellement l’objet d’un nettoyage automatisé en raison d’une utilisation insuffisante.  
- Vous devez rendre l’attribution de rôle d’administrateur général permanente pour vos comptes d’accès d’urgence. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Exclure au moins un compte de l’authentification multifacteur par téléphone

Afin de réduire le risque d’attaques résultant d’un mot de passe compromis, Azure AD recommande de généraliser l’authentification multifacteur pour tous les utilisateurs. Ce groupe inclut les administrateurs et tous les autres utilisateurs (par exemple, les responsables financiers) dont un compte compromis aurait un impact important.

Cependant, au moins un de vos comptes d’accès d’urgence ne doit pas avoir le même mécanisme d’authentification multifacteur que vos autres comptes non dédiés à l’accès d’urgence. Cela inclut les solutions d’authentification multifacteur tierces. Si vous avez une stratégie d’accès conditionnel consistant à imposer [l’authentification multifacteur à chaque administrateur](../authentication/howto-mfa-userstates.md) pour Azure AD et d’autres applications SaaS connectées, vous devez soustraire les comptes d’accès d’urgence à cette exigence et configurer un autre mécanisme à la place. Vous devez en outre vous assurer que les comptes ne font pas l’objet d’une stratégie d’authentification multifacteur par utilisateur.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Exclure au moins un compte des stratégies d’accès conditionnel

En cas d’urgence, vous ne souhaitez pas qu’une stratégie risque de vous empêcher d’accéder pour résoudre un problème. Au moins un compte d’accès d’urgence doit être exclu de toutes les stratégies d’accès conditionnel. Si vous avez instauré une [stratégie de référence](../conditional-access/baseline-protection.md), vous devez en exclure vos comptes d’accès d’urgence.

## <a name="additional-guidance-for-hybrid-customers"></a>Instructions supplémentaires pour les clients hybrides

Une option supplémentaire pour les organisations qui utilisent Azure AD Domain Services et ADFS ou un fournisseur d’identité similaire pour fédérer sur Azure AD, consiste à configurer un compte d’accès d’urgence dont la revendication MFA pourrait être fournie par ce fournisseur d’identité.  Par exemple, le compte d’accès d’urgence pourrait s’appuyer sur un certificat et une paire de clés stockée sur une carte à puce.  Quand cet utilisateur est authentifié auprès d’AD, ADFS peut transmettre une revendication à Azure AD, indiquant que l’utilisateur a satisfait aux exigences de MFA.  Même avec cette approche, les organisations doivent toujours disposer de comptes d’accès d’urgence basés sur le cloud au cas où une fédération ne pourrait pas être établie. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Conserver les appareils et les informations d’identification en lieu sûr

Les organisations sont tenues de garantir la sécurisation des informations d’identification pour les comptes d’accès d’urgence, et une confidentialité limitée aux seules personnes autorisées à les utiliser. Certains clients utilisent une carte à puce, et d’autres des mots de passe. Un mot de passe pour un compte d’accès d’urgence se divise généralement en deux ou trois parties notées sur des feuilles de papier séparées, et stockées dans des coffres-forts ignifuges installés dans des endroits distincts et sécurisés.

Si vous utilisez des mots de passe, assurez-vous que les comptes ont des mots de passe forts qui n’expirent pas. Idéalement, les mots de passe doivent comprendre au moins 16 caractères générés de façon aléatoire.


## <a name="monitor-sign-in-and-audit-logs"></a>Surveiller les journaux de connexion et d’audit

Surveillez les [journaux d’audit et de connexion à Azure AD](../reports-monitoring/concept-sign-ins.md) afin d’y détecter d’éventuelles activités de connexion et de modification à partir de comptes d’accès d’urgence. Normalement, ces comptes ne doivent pas se connecter ou apporter des modifications. Par conséquent leur éventuelle utilisation doit être considérée comme suspecte et exiger une enquête de sécurité.

## <a name="validate-accounts-at-regular-intervals"></a>Valider les comptes à intervalles réguliers

Pour former les membres du personnel à l’utilisation et à la validation des comptes d’accès d’urgence, suivez les procédures minimales suivantes à intervalles réguliers :

- Assurez-vous que les agents de la surveillance et de la sécurité sont informés que l’activité de vérification du compte est permanente.
- Assurez-vous que le processus de secours permettant d’utiliser ces comptes est documenté et à jour.
- Assurez-vous que les administrateurs et les responsables de la sécurité susceptibles de devoir suivre ces étapes en cas d’urgence sont formés au processus.
- Mettez à jour les informations d’identification de compte, en particulier les mots de passe, pour vos comptes d’accès d’urgence, puis confirmez que ceux-ci peuvent se connecter et effectuer des tâches administratives.
- Assurez-vous que les utilisateurs n’ont pas enregistré d’authentification multifacteur ou de réinitialisation de mot de passe en libre-service (SSPR) sur un appareil individuel ou dans des informations personnelles. 
- Si les comptes sont inscrits pour une authentification multifacteur sur un appareil, en vue de servir pendant la connexion ou l’activation de rôle, vérifiez que tous les administrateurs susceptibles d’utiliser l’appareil en cas d’urgence ont accès à celui-ci. Vérifiez également que l’appareil peut communiquer via au moins deux chemins d’accès réseau qui ne partagent pas un mode d’échec commun. Par exemple, l’appareil doit être capable de communiquer sur internet via le réseau sans fil d’une installation et le réseau d’un fournisseur de données cellulaires.

Ces étapes doivent être effectuées à intervalles réguliers et pour les principaux changements :

- Au moins tous les 90 jours
- En cas de remaniement récent de l’équipe informatique, par exemple suite à un changement de poste, à un départ ou à l’arrivée d’un nouvel employé
- Lorsque les abonnements Azure AD de l’organisation changent

## <a name="next-steps"></a>Étapes suivantes

- [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](directory-admin-roles-secure.md)
- [Ajouter des utilisateurs d’AAD](../fundamentals/add-users-azure-active-directory.md) et [attribuer au nouvel utilisateur le rôle d’administrateur général](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [S’inscrire à Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) si ce n’est déjà fait
- [Comment exiger la vérification en deux étapes pour un utilisateur](../authentication/howto-mfa-userstates.md)
- [Configurer des protections supplémentaires pour les administrateurs généraux dans Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts) si vous utilisez Office 365
- [Commencer une vérification de l’accès des administrateurs généraux](../privileged-identity-management/pim-how-to-start-security-review.md) et [opérer une transition des administrateurs généraux existants vers des rôles d’administrateur plus spécifiques](directory-assign-admin-roles.md)
