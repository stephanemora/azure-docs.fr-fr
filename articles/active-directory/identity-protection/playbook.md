---
title: Manuel d’Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333926"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Manuel d’Azure Active Directory Identity Protection

Ce manuel vous aide à :

* Remplir les données dans l’environnement d’Identity Protection en simulant par des vulnérabilités et des événements à risque
* Définir des stratégies d’accès conditionnel en fonction des risques et tester l’impact de ces stratégies

## <a name="simulating-risk-events"></a>Simulation des événements à risque

Cette section vous fournit les étapes requises pour simuler des types d’événements à risque suivants (le niveau de difficulté est indiqué entre parenthèses) :

* Connexions depuis des adresses IP anonymes (facile)
* Connexions depuis des emplacements non connus (moyen)
* Voyage impossible vers des emplacements inhabituels (difficile)

Les autres événements à risque ne peuvent pas être simulés de manière sécurisée.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions depuis des adresses IP anonymes

Pour plus d’informations sur cet événement à risque, consultez la section [Connexions depuis des adresses IP anonymes](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

L’exécution de la procédure ci-après requiert l’utilisation des éléments suivants :

- [Navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en) afin de simuler des adresses IP anonymes. Vous devrez peut-être utiliser une machine virtuelle si votre organisation restreint l’utilisation du navigateur Tor.
- Compte de test non encore inscrit pour l’authentification multifacteur (MFA).

**Pour simuler une connexion depuis une adresse IP anonyme, procédez comme suit**:

1. Avec le [navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en), accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Entrez les informations d’identification du compte que vous souhaitez voir apparaître dans le rapport **Connexions depuis des adresses IP anonymes** .

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 10 à 15 minutes. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Connexions depuis des emplacements non connus

Pour plus d’informations sur cet événement à risque, consultez la section [Connexions depuis des emplacements non connus](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Pour simuler des emplacements non connus, vous devez vous connecter à partir d’un emplacement et d’un appareil depuis lesquels votre compte de test ne s’est jamais connecté.

La procédure ci-après utilise les éléments nouvellement créés suivants :

- connexion VPN pour simuler le nouvel emplacement ;
- machine virtuelle pour simuler un nouvel appareil.

L’exécution de la procédure ci-après requiert l’utilisation d’un compte d’utilisateur présentant :

- un historique de connexions d’au moins 30 jours ;
- l’option d’authentification multifacteur activée.

**Pour simuler une connexion depuis un emplacement non connu, procédez comme suit**:

1. Lorsque vous vous connectez avec votre compte de test, échouez à l’authentification MFA en ne vous soumettant pas à cette dernière.
2. En utilisant votre nouveau VPN, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com) et entrez les informations d’identification de votre compte de test.

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 10 à 15 minutes.

### <a name="impossible-travel-to-atypical-location"></a>Voyage impossible vers des emplacements inhabituels

Pour plus d’informations sur cet événement à risque, consultez la section [Voyage impossible vers des emplacements inhabituels](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

La simulation de la condition de voyage impossible est difficile, car l’algorithme utilise l’apprentissage automatique pour éliminer les faux positifs, tels que le voyage impossible depuis des appareils connus ou les connexions depuis des VPN utilisés par d’autres utilisateurs du répertoire. En outre, l’algorithme requiert un historique de connexions de 14 jours et 10 connexions de l’utilisateur avant de commencer à générer des événements à risque. En raison des modèles d’apprentissage automatique complexes et des règles ci-dessus, les étapes ci-après n’entraîneront probablement pas d’événements à risque. Il peut être judicieux de répliquer ces étapes pour plusieurs comptes Azure AD afin de publier cet événement à risque.

**Pour simuler un voyage impossible vers des emplacements inhabituels, procédez comme suit**:

1. Avec votre navigateur standard, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Entrez les informations d’identification du compte pour lequel vous souhaitez générer cet événement à risque.
3. Changez votre agent utilisateur. Vous pouvez changer l’agent utilisateur depuis les outils de développement dans Internet Explorer ou à l’aide d’un module complémentaire de sélecteur d’agents utilisateur dans Firefox ou Chrome.
4. Changez votre adresse IP. Vous pouvez changer votre adresse IP en utilisant un VPN, un module complémentaire Tor ou en créant une nouvelle machine au sein d’Azure dans un autre centre de données.
5. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com) à l’aide des mêmes informations d’identification que précédemment et quelques minutes seulement après la connexion précédente.

La connexion s’affiche dans le tableau de bord d’Identity Protection dans un délai de 2 à 4 heures.

## <a name="simulating-vulnerabilities"></a>Simulation de vulnérabilités
Les vulnérabilités sont des points faibles exploitables par une personne malveillante au sein de votre environnement Azure AD. Actuellement, Azure AD Identity Protection présente trois types de vulnérabilités tirant parti d’autres fonctionnalités d’Azure AD. Ces vulnérabilités s’affichent automatiquement dans le tableau de bord d’Identity Protection dès lors que ces fonctionnalités sont configurées.

* Azure AD [Multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="testing-security-policies"></a>Test des stratégies de sécurité

Cette section décrit les procédures de test des stratégies de sécurité en matière de risque des utilisateurs et de risque à la connexion.

### <a name="user-risk-security-policy"></a>Stratégie de sécurité en matière de risque des utilisateurs

Pour plus d’informations, consultez le [Guide pratique pour configurer la stratégie pour les risques liés à l’utilisateur](howto-user-risk-policy.md).

![Risque utilisateur](./media/playbook/02.png "Manuel")

**Pour tester une stratégie de sécurité en matière de risque des utilisateurs, procédez comme suit :**

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre locataire.
2. Accédez à **Identity Protection**. 
3. Sur la page **Azure AD Identity Protection**, cliquez sur **Stratégie d’utilisateur à risque**.
4. Dans la section **Affectations**, sélectionnez les utilisateurs (et groupes) et le niveau de risque utilisateur souhaités.

    ![Risque utilisateur](./media/playbook/03.png "Manuel")

5. Dans la section Contrôles, sélectionnez le contrôle d’accès souhaité (par exemple, « Nécessite une modification du mot de passe »).
5. Dans la section **Appliquer la stratégie**, sélectionnez **Inactif**.
6. Élevez le risque utilisateur d’un compte de test, par exemple en simulant à plusieurs reprises l’un des événements à risque.
7. Patientez quelques minutes, puis vérifiez que le niveau de risque de votre utilisateur est défini sur « Moyen ». Dans le cas contraire, simulez d’autres événements à risque pour l’utilisateur.
8. Dans la section **Appliquer la stratégie**, sélectionnez **Actif**.
9. Vous pouvez désormais tester l’accès conditionnel en fonction des risques utilisateur en vous connectant à l’aide d’un compte d’utilisateur présentant un niveau de risque élevé.

### <a name="sign-in-risk-security-policy"></a>Stratégie de sécurité en matière de risque à la connexion

Pour plus d’informations, consultez le [Guide pratique pour configurer la stratégie pour les risques liés à la connexion](howto-sign-in-risk-policy.md).

![Risque à la connexion](./media/playbook/01.png "Manuel")

**Pour tester une stratégie de risque à la connexion, procédez comme suit :**

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre locataire.
2. Accédez à **Azure AD Identity Protection**.
3. Sur la page **Azure AD Identity Protection** principale, cliquez sur **Stratégie de connexion à risque**. 
4. Dans la section **Affectations**, sélectionnez les utilisateurs (et groupes) et le niveau de risque de connexion souhaités.

    ![Risque à la connexion](./media/playbook/04.png "Manuel")

5. Dans la section **Contrôles**, sélectionnez le contrôle d’accès souhaité (par exemple, **Exiger une authentification multifacteur**). 
6. Dans la section **Appliquer la stratégie**, sélectionnez **Actif**.
7. Cliquez sur **Enregistrer**.
8. Vous pouvez désormais tester l’accès conditionnel en fonction des risques à la connexion en vous connectant à l’aide d’une session à risque (par exemple, au moyen du navigateur Tor). 

## <a name="see-also"></a>Voir aussi

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
