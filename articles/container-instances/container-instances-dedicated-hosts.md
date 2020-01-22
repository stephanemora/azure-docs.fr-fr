---
title: Déployer sur des hôtes dédiés
description: Utiliser des hôtes dédiés pour obtenir une véritable isolation au niveau de l’hôte pour vos charges de travail
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903550"
---
# <a name="deploy-on-dedicated-hosts"></a>Déployer sur des hôtes dédiés

« Dédié » est une SKU Azure Container Instances (ACI) qui fournit un environnement Compute isolé et dédié pour l’exécution sécurisée des conteneurs. L’utilisation de la référence SKU dédiée donne à chaque groupe de conteneurs un serveur physique dédié dans un centre de données Azure, ce qui garantit l’isolation complète de la charge de travail afin de répondre aux exigences de conformité et de sécurité de votre organisation. 

La référence SKU dédiée est appropriée pour les charges de travail de conteneur qui requièrent l’isolation de la charge de travail du point de vue du serveur physique.

## <a name="using-the-dedicated-sku"></a>Utilisation de la référence SKU dédiée

> [!IMPORTANT]
> L’utilisation de la référence SKU dédiée est uniquement disponible dans la dernière version de l’API (2019-12-01) qui est en cours de déploiement. Spécifiez cette version d’API dans votre modèle de déploiement. En outre, la limite par défaut d’un abonnement pour utiliser la référence SKU dédiée est 0. Si vous souhaitez utiliser cette référence SKU pour vos déploiements de conteneur de production, veuillez créer une [demande de support Azure][azure-support]

À partir de la version de l’API 2019-12-01, il existe une propriété « SKU » dans la section Propriétés du groupe de conteneurs d’un modèle de déploiement qui est nécessaire pour un déploiement ACI. Actuellement, vous pouvez utiliser cette propriété dans le cadre d’un modèle de déploiement Azure Resource Manager pour ACI. Pour en savoir plus sur le déploiement de ressources ACI à l’aide d’un modèle, consultez le [Tutoriel : Déployer un groupe de plusieurs conteneurs avec un modèle Resource Manager](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

La propriété SKU peut avoir les valeurs suivantes :
* Standard : le choix de déploiement ACI standard, qui garantit toujours la sécurité au niveau de l’hyperviseur 
* Dédié : utilisé pour l’isolation au niveau de la charge de travail avec des hôtes physiques dédiés pour le groupe de conteneurs

## <a name="modify-your-json-deployment-template"></a>Modifier votre modèle de déploiement JSON

Dans votre modèle de déploiement, où la ressource du groupe de conteneurs est spécifiée, assurez-vous du réglage suivant : `"apiVersion": "2019-12-01",`. Dans la section Propriétés de la ressource du groupe de conteneurs, définissez comme suit : `"sku": "Dedicated",`.

Voici un exemple d’extrait de code pour la section Ressources d’un modèle de déploiement pour groupe de conteneurs qui utilise la référence SKU dédiée :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Déployer votre groupe de conteneurs

Si vous avez créé et modifié le fichier de modèle de déploiement sur votre Bureau, vous pouvez le télécharger dans votre répertoire Cloud Shell en y faisant glisser le fichier. 

Créez un groupe de ressources avec la commande [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Déployez ensuite le modèle avec la commande [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Après quelques secondes, vous devriez recevoir une réponse initiale d’Azure. Une fois le déploiement terminé, toutes les données qui y sont associées et qui sont conservées par le service ACI sont chiffrées à l’aide de la clé que vous avez fournie.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
