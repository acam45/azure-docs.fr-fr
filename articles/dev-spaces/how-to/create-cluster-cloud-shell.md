---
title: Comment créer un cluster Kubernetes activé pour Azure Dev Spaces à l’aide d’Azure Cloud Shell | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 10/04/2018
ms.topic: article
description: Découvrez comment créer rapidement un cluster Kubernetes activé pour Azure Dev Spaces directement depuis votre navigateur, sans rien installer.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 47c467e020a7a9253daa636352352d9a57dddf28
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978145"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Créer un cluster Kubernetes à l’aide d’Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](/azure/cloud-shell) pour créer un cluster pour Azure Dev Spaces via le bouton **Essayer** de cette page. Si vous n’êtes pas connecté, suivez les invites afin de vous connecter en utilisant un compte Azure, puis tapez les commandes à l’invite Azure Cloud Shell lorsqu’elle s’affiche.

## <a name="create-the-cluster"></a>Création du cluster

Nous devons d’abord créer le groupe de ressources. Utilisez une des régions actuellement prises en charge (EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral ou CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Créez un cluster Kubernetes avec la commande suivante :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.3 --enable-addons http_application_routing
```

La création du cluster ne prend que quelques minutes.  Une fois terminée, la sortie s’affiche au format JSON. Recherchez `provisioningState` et vérifiez qu’il s’agit bien de `Succeeded`.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des liens vers les didacticiels complets, consultez la section [Azure Dev Spaces](/azure/dev-spaces/).
