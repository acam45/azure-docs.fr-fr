---
title: Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide de PowerShell.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: kumud
ms.openlocfilehash: 9dfd477678148f3e4bb80bafe61e4867dab54c8e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739530"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide de PowerShell

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend redondant interzone qui utilise une adresse IP publique Standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
 La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande suivante :

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Créer une adresse IP publique Standard 
Créer une adresse IP publique Standard à l’aide de la commande suivante :

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Créer une configuration d’adresse IP frontend pour le site web

Créez une configuration d’adresse IP frontend à l’aide de la commande suivante :

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Créer le pool d’adresses backend

Créez un pool d’adresses backend à l’aide de la commande suivante :

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Créer une sonde d’équilibreur de charge sur le port 80

Créez une sonde d’intégrité d’équilibreur de charge pour le port 80 à l’aide de la commande suivante :

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge
 Créez une règle d’équilibreur de charge à l’aide de la commande suivante :

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Créer un équilibrage de charge
Créez une instance de Load Balancer Standard à l’aide de la commande suivante :

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md).



