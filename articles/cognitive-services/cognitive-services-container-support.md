---
title: Prise en charge des conteneurs dans Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment les conteneurs Docker peuvent faire bénéficier vos données de Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: b66d6bc93f9d2df3eccabf0f0f9edb0384bab70f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634909"
---
# <a name="container-support-in-azure-cognitive-services"></a>Prise en charge des conteneurs dans Azure Cognitive Services

La prise en charge des conteneurs dans Azure Cognitive Services permet aux développeurs d’utiliser les mêmes API riches que celles disponibles dans Azure, mais en bénéficiant de la flexibilité qu’offrent les [conteneurs Docker](https://www.docker.com/what-container). La prise en charge des conteneurs est désormais disponible en préversion pour quelques solutions Azure Cognitive Services, dont des composants de [Vision par ordinateur](Computer-vision/Home.md), [Visage](Face/Overview.md) et [Analyse de texte](text-analytics/overview.md).

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service, y compris les dépendances et la configuration, est packagé en tant qu’image conteneur. L’image conteneur peut être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et l’encombrement du système d’exploitation sous-jacent est inférieur à celui d’une machine virtuelle. Vous pouvez instancier les conteneurs à partir d’images conteneur pour les tâches à court terme et les supprimer quand vous n’avez plus besoin.

Regardez cette [courte vidéo](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services) pour une démonstration.

Les services [Vision par ordinateur](Computer-vision/Home.md), [Visage](Face/Overview.md) et [Analyse de texte](text-analytics/overview.md) sont disponibles sur [Microsoft Azure](https://azure.microsoft.com). Connectez-vous au [portail Azure](https://portal.azure.com/) pour créer et explorer les ressources Azure pour ces services.

## <a name="features-and-benefits"></a>Fonctionnalités et avantages

- **Contrôle des données** : permettre aux clients d’utiliser Cognitive Services avec un contrôle total de leurs données.  Ceci est essentiel pour les clients qui ne peut pas envoyer les données dans le cloud mais ont besoin d’accéder à la technologie de Cognitive Services. Cohérence de la prise en charge dans les environnements hybrides : entre les données, la gestion, l’identité et la sécurité.
- **Contrôle des mises à jour du modèle** : fournir aux clients une flexibilité dans le contrôle de version et la mise à jour des modèles déployés dans leurs solutions.
- **Architecture portable** : permettre la création d’une architecture d’application portable qui peut être déployée dans le cloud, localement et en périphérie. Les conteneurs peuvent également être déployés directement sur [Azure Kubernetes Service](/azure/aks/), sur [Azure Container Instances](/azure/container-instances/) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure/azure-stack/). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Débit élevé / faible latence** : fournir aux clients la possibilité de mettre à l’échelle pour des exigences de débit élevé et de faible latence en permettant l’exécution de Cognitive Services dans Azure Kubernetes Service physiquement proche de leur logique d’application et de leurs données.


## <a name="containers-in-azure-cognitive-services"></a>Conteneurs dans Azure Cognitive Services

Les conteneurs Azure Cognitive Services fournissent l’ensemble suivant de conteneurs Docker, chacun contenant un sous-ensemble de fonctionnalités à partir de services dans Azure Cognitive Services :

| de diffusion en continu | Conteneur| Description |
|---------|----------|-------------|
|[Vision par ordinateur](Computer-vision/computer-vision-how-to-install-containers.md) |**Reconnaître le texte** |Extraire un texte imprimé à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches et des cartes de visite.<br/><br/>**Important :** le conteneur Reconnaître le texte ne fonctionne qu’en anglais pour le moment.<br>[Demander l’accès](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Visage](Face/face-how-to-install-containers.md) |**Visage** |Détecter les visages humains sur des images et identifier les attributs, y compris les caractéristiques (par exemple, le nez et les yeux), le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de la détection, le conteneur Visage peut vérifier si deux visages sur la même image ou sur des images différentes sont identiques à l’aide d’un score de confiance, ou de comparer des visages par rapport à une base de données pour voir si un visage similaire ou identique existe déjà. Il peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées.<br>[Demander l’accès](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Analyse de texte](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**Extraction de phrases clés** ([image](https://go.microsoft.com/fwlink/?linkid=2018757)) |Extraire des expressions clés pour identifier les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ». |
|[Analyse de texte](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Détection de la langue** ([image](https://go.microsoft.com/fwlink/?linkid=2018759)) |Détecter la langue (parmi 120 langues maximum) dans laquelle le texte d’entrée est rédigé et générer un code de langue unique pour chaque document envoyé dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. |
|[Analyse de texte](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Analyse des sentiments** ([image](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analyser le texte brut pour obtenir des indices sur un sentiment positif ou négatif. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif. Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante. |

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilité des conteneurs dans Azure Cognitive Services

Les conteneurs Azure Cognitive Services sont accessibles publiquement via votre abonnement Azure, et les images conteneur Docker peuvent être extraites à partir de Microsoft Container Registry ou de Docker Hub. Vous pouvez utiliser la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du référentiel approprié.

> [!IMPORTANT]
> Actuellement, vous devez effectuer un processus d’inscription pour accéder aux conteneurs [Visage](Face/face-how-to-install-containers.md) et [Reconnaître le texte](Computer-vision/computer-vision-how-to-install-containers.md), dans laquelle vous remplissez et soumettez un questionnaire comportant des questions sur vous, votre entreprise et le cas d’utilisation pour lequel vous souhaitez implémenter les conteneurs. Une fois que l’accès et des informations d’identification vous sont fournis, vous pouvez alors extraire les images conteneur pour les conteneurs Visage et Reconnaître le texte à partir d’un Registre de conteneurs privé hébergé par Azure Container Registry.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser des conteneurs Azure Cognitive Services :

**Moteur Docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, Docker doit être configuré pour prendre en charge les conteneurs Linux. Les conteneurs Docker peuvent également être déployés directement sur [Azure Kubernetes Service](/azure/aks/) ou sur [Azure Container Instances](/azure/container-instances/).

Docker doit être configuré pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, tels que les registres, les référentiels, les conteneurs et les images de conteneur, ainsi qu’une maîtrise des commandes `docker` de base.  

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

Les conteneurs individuels peuvent aussi avoir leurs propres exigences, notamment sur le serveur et l’allocation de mémoire.

## <a name="developer-samples"></a>Exemples de développement

Des exemples de développement sont disponibles sur notre [référentiel Github](https://github.com/Azure-Samples/cognitive-services-containers-samples). 

## <a name="next-steps"></a>Étapes suivantes

Installer et explorer les fonctionnalités fournies par les conteneurs dans Azure Cognitive Services :

* [Installer et utiliser les conteneurs Vision par ordinateur](Computer-vision/computer-vision-how-to-install-containers.md)
* [Installer et utiliser les conteneurs Visage](Face/face-how-to-install-containers.md)
* [Installer et utiliser les conteneurs Analyse de texte](text-analytics/how-tos/text-analytics-how-to-install-containers.md)