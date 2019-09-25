---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "61047607"
---
## <a name="firewall-configuration"></a>Configuration du pare-feu

Pour que les tests soient soumis à Xamarin Test Cloud, l’ordinateur qui envoie les tests doit être en mesure de communiquer avec les serveurs Test Cloud. Les pare-feu doivent être configurés pour autoriser le trafic réseau vers et depuis les serveurs situés dans **testcloud.xamarin.com** sur les ports 80 et 443. Ce point de terminaison est géré par DNS et l’adresse IP est sujette à modification. 

Dans certains cas, un test (ou un appareil exécutant le test) doit communiquer avec les serveurs Web protégés par un pare-feu. Dans ce scénario, le pare-feu doit être configuré pour autoriser le trafic à partir des adresses IP suivantes :

* **195.249.159.238**
* **195.249.159.239**
