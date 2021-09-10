---
title: Présentation de la sécurité
description: Informations relatives à la sécurité des serveurs avec Azure Arc.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 84f3b7cae576f1bedc6de57f94623936cbb0a51c
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214243"
---
# <a name="azure-arc-for-servers-security-overview"></a>Vue d’ensemble de la sécurité Azure Arc pour serveurs

Cet article décrit la configuration et les considérations relatives à la sécurité que vous devez évaluer avant de déployer des serveurs avec Azure Arc dans votre entreprise.

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

Chaque serveur compatible avec Azure Arc possède une identité gérée dans le cadre d'un groupe de ressources au sein d'une souscription Azure. Cette identité représente le serveur s’exécutant sur site ou dans un autre environnement Cloud. L’accès à cette ressource est contrôlé par le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md) standard. À partir de la page [**Contrôle d’accès (IAM)** ](../../role-based-access-control/role-assignments-portal.md) du portail Azure, vous pouvez vérifier qui a accès à votre serveur avec Azure Arc.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Contrôle d’accès au serveur avec Azure Arc" border="false" lightbox="./media/security-overview/access-control-page.png":::

Les utilisateurs et les applications bénéficiant de l’accès à la ressource par le biais du rôle d’administrateur ou de [contributeur](../../role-based-access-control/built-in-roles.md#contributor) peuvent apporter des modifications à la ressource, y compris le déploiement ou la suppression d’[extensions](manage-vm-extensions.md) sur la machine. Les extensions peuvent inclure des scripts arbitraires qui s’exécutent dans un contexte privilégié ; par conséquent, vous devez considérer tout contributeur sur la ressource Azure comme un administrateur indirect du serveur.

Le rôle **Intégration d’Azure Connected Machine** est disponible pour l’intégration à grande échelle et il peut uniquement lire ou créer de nouveaux serveurs avec Arc dans Azure. Il ne peut pas servir à supprimer des serveurs déjà inscrits, ou à gérer des extensions. En tant que bonne pratique, nous vous recommandons d’attribuer ce rôle uniquement au principal du service Azure Active Directory (Azure AD) utilisé pour l’intégration des machines à grande échelle.

En tant que membres du rôle **Administrateur des ressources Azure Connected Machine**, les utilisateurs peuvent lire, modifier, réintégrer et supprimer une machine. Ce rôle est conçu pour prendre en charge la gestion des serveurs avec Arc, mais aucune autre ressource dans le groupe de ressources ou l’abonnement.

## <a name="agent-security-and-permissions"></a>Sécurité et autorisations de l’agent

Pour gérer l’agent Azure Connected Machine (azcmagent) sur Windows, votre compte d’utilisateur doit être membre du groupe Administrateurs local. Sur Linux, vous devez disposer d’autorisations d’accès racine.

L’agent Azure Connected Machine est composé de trois services, qui s’exécutent sur votre machine.

* Le service Hybrid Instance Metadata Service (himds) est responsable de toutes les fonctionnalités principales d’Arc. Entre autres, l’envoi de pulsations à Azure, l’exposition d’un service de métadonnées d’instance locale, pour que d’autres applications découvrent l’ID de ressource Azure de la machine et récupèrent des jetons Azure AD en vue de l’authentification auprès d’autres services Azure. Ce service s’exécute en tant que compte de service virtuel non privilégié sur Windows, et est exécuté avec l’utilisateur **himds** sur Linux.

* Le service de configuration d’invité (GCService) est responsable de l’évaluation d’Azure Policy sur la machine.

* Le service d’extension de configuration d’invité (ExtensionService) est responsable de l’installation, de la mise à jour et de la suppression d’extensions (agents, scripts ou autres logiciels) sur la machine.

Les services de configuration et d’extension d’invités s’exécutent en tant que Système local sur Windows, et en tant que racine sur Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Utilisation d’une identité managée sur les serveurs avec Arc

Par défaut, l’identité affectée par le système Azure Active Directory qui est utilisée par Arc peut uniquement servir pour mettre à jour l’état du serveur avec Arc dans Azure. Par exemple, l’état des pulsations *la dernière fois*. Vous pouvez éventuellement affecter des rôles supplémentaires à l’identité si une application sur votre serveur utilise l’identité attribuée par le système pour accéder aux autres services Azure. Pour en savoir plus sur la configuration d'une identité gérée attribuée par le système pour accéder aux ressources Azure, voir [Authentification contre les ressources Azure avec des serveurs compatibles avec Arc](managed-identity-authentication.md). 

Alors que n’importe quelle application s’exécutant sur la machine peut accéder à Hybrid Instance Metadata Service, seules les applications autorisées peuvent demander un jeton Azure AD pour l’identité attribuée par le système. Lors de la première tentative d’accès à l’URI de jeton, le service génère un objet blob de chiffrement, créé de manière aléatoire dans un emplacement sur le système de fichiers, que seuls les appelants approuvés peuvent lire. L’appelant doit ensuite lire le fichier (en prouvant qu’il dispose de l’autorisation appropriée) et faire une nouvelle tentative de demande, avec le contenu du fichier dans l’en-tête d’autorisation, pour parvenir à récupérer un jeton Azure AD.

* Sur Windows, l’appelant doit être membre du groupe local **Administrateurs**, ou du groupe **Applications d’extension de l’agent hybride** pour lire l’objet blob.

* Sur Linux, l’appelant doit être membre du groupe **himds** pour lire l’objet blob.

Pour en savoir plus sur l'utilisation d'une identité gérée avec des serveurs compatibles avec Arc pour s'authentifier et accéder aux ressources Azure, consultez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/4hfwxwhWcP4]

## <a name="using-disk-encryption"></a>Utilisation du chiffrement de disque

L’agent Azure Connected Machine utilise l’authentification par clé publique pour communiquer avec le service Azure. Après avoir intégré un serveur à Azure Arc, une clé privée est enregistrée sur le disque et utilisée chaque fois que l’agent communique avec Azure. En cas de vol, la clé privée peut être utilisée sur un autre serveur pour communiquer avec le service et faire comme s’il s’agissait du serveur d’origine. Entre autres actions possibles, l’obtention de l’accès à l’identité attribuée par le système et à toutes les ressources auxquelles l’identité a accès. Le fichier de clé privée est protégé afin d’autoriser uniquement au compte **himds** l’accès pour le lire. Pour empêcher les attaques hors connexion, nous vous recommandons vivement d’utiliser le chiffrement de disque complet (par exemple, BitLocker, dm-crypt, etc.) sur le volume du système d’exploitation de votre serveur.

## <a name="next-steps"></a>Étapes suivantes

* Avant d’évaluer ou d’activer des serveurs avec Azure Arc sur plusieurs machines hybrides, consultez [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour en savoir plus sur les exigences et les détails techniques relatifs à l’agent, ainsi que sur les méthodes de déploiement.

* Consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md) pour planifier le déploiement de serveurs avec Azure Arc à n’importe quelle échelle et implémenter la gestion et la supervision centralisées.
