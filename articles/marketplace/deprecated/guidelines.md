---
title: Instructions pour les éditeurs de la Place de marché Azure et AppSource | Azure
description: Instructions pour la Place de marché Azure et AppSource destinées aux éditeurs d’applications et de services
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: f41aeb75253c803eac03b856d1e1ed0edb74a7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281525"
---
# <a name="guidelines"></a>Consignes  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Instructions pour la Place de marché Azure  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Instructions pour la création d’un ID Microsoft dans le but de gérer un compte Place de marché  
Si plusieurs personnes nécessite l’accès au même ID Microsoft que celui utilisé pour créer votre compte Place de marché, vous devez suivre ces instructions qui vous aideront à créer un compte d’entreprise. 

>[!IMPORTANT]
>Pour autoriser plusieurs utilisateurs à accéder à votre compte Centre de développement Microsoft, Microsoft vous recommande d’utiliser Azure Active Directory (Azure AD) pour affecter des rôles aux utilisateurs individuels. Chaque utilisateur peut accéder au compte en se connectant avec des informations d’identification Azure AD individuelles. Créez votre ID Microsoft à l’aide d’une adresse e-mail dans un domaine enregistré auprès de votre société. Microsoft recommande que l’adresse e-mail ne soit pas assignée à une personne. par exemple `windowsapps@fabrikam.com`.  
>*   Pour plus d’informations, consultez la section [Problème : ID Microsoft dans un domaine fédéré Azure AD](#issue-microsoft-id-in-an-azure-ad-federated-domain).  

*   Limitez l’accès à l’ID Microsoft au plus petit nombre possible de développeurs. 
*   Configurez une liste de distribution électronique d’entreprise incluant tous les utilisateurs qui doivent accéder à votre compte Centre de développement. Ajoutez l’adresse e-mail de la liste de distribution à vos informations de sécurité. La liste de distribution permet à tous les employés sur la liste de recevoir les codes de sécurité si nécessaire, et gérer les informations de sécurité de votre ID Microsoft. Si la création d’une liste de distribution n’est pas possible, alors le détenteur du compte de messagerie doit être disponible pour accéder aux codes de sécurité et les partager quand cela est nécessaire.  
    *   Par exemple, il est invité à spécifier les codes de sécurité quand de nouvelles informations de sécurité sont ajoutées à l’ID Microsoft ou quand l’accès à celui-ci se fait à partir d’un nouvel appareil.  
*   Ajoutez un numéro de téléphone d’entreprise qui ne nécessite pas d’extension et qui reste accessible par les membres de l’équipe.  
*   En général, vous devez demander aux développeurs d’utiliser des appareils approuvés pour se connecter à votre compte Centre de développement. Les membres principaux de l’équipe doivent avoir accès aux appareils de confiance. L’utilisation d’appareils de confiance pour l’accès réduit la nécessité d’envoyer des codes de sécurité quand une personne accède au compte Centre de développement.  
*   Si vous devez accorder l’accès au compte Centre de développement à partir d’un ordinateur non approuvé, vous devez limiter l’accès à pas plus de cinq développeurs. Dans l’idéal, vos développeurs doivent accéder au compte à partir d’ordinateurs qui partagent le même emplacement géographique et le même emplacement réseau.  
*   Passez en revue et vérifiez fréquemment vos informations de sécurité.  
    *   Pour afficher vos informations de sécurité, consultez la page Paramètres de sécurité à l’adresse [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Votre compte Centre de développement doit être principalement accessible à partir d’ordinateurs approuvés. Il est essentiel que vous y accédiez à partir d’ordinateurs approuvés, car il existe une limite du nombre de codes générés par le compte Centre de développement par semaine. L’utilisation d’ordinateurs approuvés permet également d’obtenir l’expérience utilisateur la plus sécurisée et cohérente. 
*   Pour plus d’informations sur les instructions et la sécurité relatives au compte Centre de développement, consultez la page Ouvrir un compte de développeur à l’adresse [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problème : ID Microsoft dans un domaine fédéré Azure AD  
Votre compte d’entreprise peut être fédéré via Azure Active Directory (Azure AD). Si vous essayez de créer un ID Microsoft à l’aide d’une adresse e-mail d’entreprise qui est fédérée avec Azure AD, vous recevrez une erreur. Si vous recevez une erreur, vous devez vérifier auprès de votre équipe informatique pour confirmer que votre compte est fédéré via Azure AD. Un e-mail fédéré Azure AD est un problème connu et Microsoft travaille à sa résolution.  
*   Pour plus d’informations sur Azure AD, consultez la page Documentation Azure Active Directory à l’adresse [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft vous recommande une solution de contournement. Procédez comme suit pour créer une nouvelle adresse e-mail dans le domaine `outlook.com` et créer une règle de transfert pour vos communications.  
1.  Accédez à la page Créer un compte, puis cliquez sur le lien Obtenir une nouvelle adresse e-mail. 
    *   Pour vous inscrire pour obtenir votre ID Microsoft, consultez la page Créer un compte à l’adresse [signup.live.com/signup](https://signup.live.com/signup).  
2.  Créez une adresse e-mail et entrez un mot de passe. Un nouvel ID Microsoft et une boîte aux lettres électronique sont créés dans le domaine `outlook.com`. Poursuivez le processus d’inscription jusqu’à la création du compte.  

    >[!IMPORTANT]
    >Vous devez utiliser une adresse e-mail ou une liste de distribution qui est inscrite en tant qu’ID Microsoft pour vous inscrire dans le Centre de développement. Microsoft vous recommande d’utiliser une liste de distribution pour ne pas avoir besoin de faire toujours appel à une personne spécifique. Si votre adresse e-mail ou liste de distribution n’est pas inscrite, alors vous devez l’inscrire maintenant.    

    >[!Important]
    >Si votre adresse e-mail se trouve dans le domaine d’entreprise `Microsoft`, vous ne pouvez pas l’utiliser pour l’inscription dans le Centre de développement.  

3.  Après avoir créé l’ID Microsoft avec l’adresse e-mail Outlook, connectez-vous à votre boîte aux lettres Outlook. Créez une règle de transfert des e-mails. La règle de transfert des e-mails doit déplacer tous les e-mails reçus dans la boîte aux lettres Outlook vers l’adresse e-mail dans votre domaine que vous avez créé pour gérer votre compte Place de marché.  
    *   Pour vous connecter à votre boîte aux lettres Outlook, consultez la page Outlook à l’adresse [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Pour plus d’informations sur les règles de transfert, consultez la page Utiliser des règles dans Outlook Web App pour transférer automatiquement des messages vers un autre compte à l’adresse [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  La règle de transfert envoie l’ensemble des e-mails et des communications reçus dans le compte e-mail Outlook à l’adresse e-mail dans un domaine enregistré auprès de votre entreprise. Votre adresse e-mail `outlook.com` doit être utilisée pour l’authentification dans le Centre de développement et le Portail Cloud Partner.  

## <a name="next-steps"></a>Étapes suivantes

*   Consultez la page [Guide de l’éditeur Place de marché Azure et AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). 
 
---
