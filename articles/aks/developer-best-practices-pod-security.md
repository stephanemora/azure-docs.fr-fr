---
title: Meilleures pratiques du développeur - Sécurité des pods dans Azure Kubernetes Service (AKS)
description: Découvrez les meilleures pratiques du développeur pour apprendre à sécuriser des pods dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: d48a1a1c9e220690649bbf60e9909a38d575c156
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651956"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Meilleures pratiques pour la sécurité des pods dans Azure Kubernetes Service (AKS)

Lorsque vous développez et exécutez des applications dans Azure Kubernetes Service (ACS), la sécurité de vos pods est primordiale. Vos applications doivent être conçues selon le principe du moindre privilège. La sécurité des données privées est la priorité des clients. Vous ne tenez pas à ce que les informations d’identification telles que les chaînes de connexion aux bases de données, les clés, les secrets ou les certificats, soient exposées au monde extérieur, avec le risque qu’un pirate puisse les exploiter à des fins malveillantes. Ne les ajoutez pas à votre code et évitez de les incorporer dans vos images de conteneur. Vos informations risqueraient d’être exposées et cela limiterait votre capacité à changer ces informations d’identification, puisque les images de conteneur devront être reconstruites.

Cet article décrit les meilleures pratiques sur la sécurisation des pods dans AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser le contexte de sécurité des pods pour limiter l’accès aux processus et services ou l’élévation de privilèges
> * S’authentifier avec d’autres ressources Azure à l’aide d’identités de pod managées
> * Demander et récupérer des informations d’identification à partir d’un coffre numérique tel qu’Azure Key Vault

Vous pouvez également consulter les meilleures pratiques relatives à la [sécurité des clusters][best-practices-cluster-security] et à la [gestion des images conteneur][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Sécuriser l’accès du pod aux ressources

**Meilleures pratiques** : pour changer d’utilisateur ou de groupe et limiter l’accès aux services et processus de nœud sous-jacents, définissez les paramètres du contexte de sécurité des pods. Affectez le nombre minimal de privilèges requis.

Pour que vos applications s’exécutent correctement, les pods doivent s’exécuter en tant qu’utilisateur ou groupe défini, et non en tant que *racine*. Le `securityContext` pour un pod ou un conteneur vous permet de définir des paramètres tels que *runAsUser* ou *fsGroup* pour assumer les autorisations appropriées. Affectez uniquement les autorisations requises pour l’utilisateur ou le groupe et n’utilisez pas le contexte de sécurité pour assumer des autorisations supplémentaires. Lorsque vous vous connectez en tant qu’utilisateur non root, les conteneurs ne peuvent pas établir de liaison avec les ports privilégiés inférieurs à 1024. Dans ce scénario, Kubernetes Services peut être utilisé pour masquer le fait qu’une application s’exécute sur un port particulier.

Un contexte de sécurité de pod peut également permettre de définir des fonctionnalités ou autorisations supplémentaires pour accéder à des processus et services. Vous pouvez utiliser les définitions de contexte de sécurité courantes ci-dessous :

* **allowPrivilegeEscalation** définit si le pod peut assumer les privilèges *racine*. Concevez vos applications afin que ce paramètre soit toujours défini sur *false*.
* **Les fonctionnalités Linux** permettent au pod d’accéder aux processus de nœud sous-jacents. Soyez vigilant lorsque vous affectez ces fonctionnalités. Affectez le nombre minimal de privilèges nécessaire. Pour plus d’informations, consultez la page [Linux capabilities][linux-capabilities] (Fonctionnalités Linux).
* **SELinux Labels** est un module de sécurité du noyau Linux qui vous permet de définir des stratégies d’accès pour l’accès aux services, aux processus et au système de fichiers. Là encore, affectez le nombre minimal de privilèges nécessaire. Pour plus d’informations, consultez les [options SELinux dans Kubernetes][selinux-labels]

L’exemple de manifeste YAML suivant définit les paramètres du contexte de sécurité pour indiquer :

* qu’un pod est exécuté en tant qu’utilisateur portant l’ID *1000* et faisant partie du groupe avec l’ID *2000* ;
* qu’il n’est pas possible d’élever les privilèges pour utiliser `root` ;
* que les fonctionnalités Linux peuvent accéder aux interfaces réseau et à l’horloge (matérielle) en temps réel de l’hôte.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Consultez votre opérateur de cluster pour déterminer les paramètres de contexte de sécurité dont vous avez besoin. Essayez de concevoir vos applications de manière à réduire les autorisations et accès supplémentaires dont le pod a besoin. Il existe d’autres fonctionnalités de sécurité qui permettent de limiter l’accès en utilisant les paramètres AppArmor et seccomp (environnement informatique sécurisé) qui peuvent être implémentés par les opérateurs du cluster. Pour plus d’informations, consultez la page [Secure container access to resources][apparmor-seccomp] (Accès sécurisé du conteneur aux ressources).

## <a name="limit-credential-exposure"></a>Limiter l’exposition des informations d’identification

**Meilleures pratiques** -ne définissez pas d’informations d’identification dans le code de votre application. Utilisez des identités managées pour les ressources Azure pour permettre à votre pod de demander l’accès à d’autres ressources. Vous devez également utiliser un coffre-fort numérique, tel qu’Azure Key Vault, pour stocker et récupérer des clés numériques et informations d’identification.

Pour limiter le risque d’exposition d’informations d’identification dans le code de votre application, évitez d’utiliser des informations d’identification fixes ou partagées. Vous ne devez inclure aucune information d’identification ou clé directement dans votre code. Si ces informations d’identification sont exposées, l’application doit être mise à jour et redéployée. Une meilleure approche consiste à attribuer aux pods leur propre identité ainsi qu’un moyen de s’authentifier d’eux-mêmes, ou de récupérer automatiquement les informations d’identification à partir d’un coffre numérique.

Ce qui suit [AKS associés projets open source] [ aks-associated-projects] vous permettent d’authentifier automatiquement pods ou les informations d’identification de la demande et les clés à partir d’un coffre numérique :

* Identités managées pour les ressources Azure
* Pilote Azure Key Vault FlexVol

### <a name="use-pod-managed-identities"></a>Utiliser des identités de pod managées

Une identité gérée pour les ressources Azure permet à un pod de s’authentifier dans Azure auprès d’un service qui le prend en charge, tel que le service Stockage ou SQL. Le pod se voit attribuer une identité Azure qui lui permet de s’authentifier auprès d’Azure Active Directory et de recevoir un jeton numérique. Ce jeton numérique peut être présenté à d’autres services Azure qui vérifient si le pod est autorisé à accéder au service et à effectuer les actions requises. Cette approche signifie par exemple qu’aucun secret n’est nécessaire pour les chaînes de connexion à la base de données. Le worflow simplifié du système d’identités de pod managées est représenté dans le schéma suivant :

![Workflow simplifié du système d’identités de pod managées dans Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Avec une identité managée, il n’est pas nécessaire d’inclure des informations d’identification dans le code de votre application pour accéder à un service, tel que Stockage Azure. Puisque chaque pod s’authentifie avec sa propre identité, vous pouvez contrôler et réviser les accès. Si votre application se connecte auprès d’autres services Azure, utilisez des identités managées pour limiter la réutilisation d’informations d’identification et le risque d’exposition.

Pour plus d’informations sur les identités de pod, consultez [configurer un cluster AKS pour utiliser des identités de pod géré et avec vos applications][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Utiliser Azure Key Vault avec FlexVol

Les identités de pod géré fonctionnent très bien pour s’authentifier auprès de services Azure qui les prennent en charge. Pour vos propres services ou applications sans identités managées pour des ressources Azure, vous devez toujours utiliser des informations d’identification ou des clés pour vous authentifier. Vous pouvez utiliser un coffre numérique pour stocker ces informations d’identification.

Lorsque les applications ont besoin d’informations d’identification, elles communiquent avec le coffre numérique, récupèrent les dernières informations d’identification, puis se connectent au service requis. Azure Key Vault peut être utilisé comme coffre numérique. Le schéma suivant illustre de façon simplifiée le workflow utilisé pour récupérer des informations d’identification à partir d’Azure Key Vault à l’aide d’identités de pod managées :

![Workflow simplifié pour récupérer des informations d’identification dans Key Vault à l’aide d’une identité de pod managée](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Key Vault vous permet de stocker et faire tourner régulièrement les secrets, tels que les informations d’identification, les clés de compte de stockage ou les certificats. Vous pouvez intégrer Azure Key Vault à un cluster AKS à l’aide d’un FlexVolume. Le pilote FlexVolume permet au cluster AKS de récupérer en mode natif les informations d’identification dans Key Vault et de les remettre uniquement au pod demandeur, en toute sécurité. Consultez votre opérateur de cluster pour déployer le pilote Key Vault FlexVol sur les nœuds AKS. Vous pouvez utiliser une identité de pod managée pour demander l’accès à Key Vault et récupérer les informations d’identification dont vous avez besoin via le pilote FlexVolume.

## <a name="next-steps"></a>Étapes suivantes

Cet article était dédié à la sécurisation de vos pods. Pour implémenter quelques-unes de ces pratiques, consultez les articles suivants :

* [Utiliser des identités managées pour les ressources Azure avec AKS][aad-pod-identity]
* [Intégrer Azure Key Vault à AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
