---
title: Utilisation de Xcode pour concevoir des interfaces utilisateur
description: La méthode recommandée pour créer des interfaces utilisateur iOS est maintenant directement à l’aide de XCode sur un Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b6fc5c89e9221bc84b364290a275fbd6d35c12c4
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343279"
---
# <a name="using-xcode-to-design-user-interfaces"></a>Utilisation de Xcode pour concevoir des interfaces utilisateur

_La méthode recommandée pour modifier les fichiers. Storyboard et. bec consiste à les modifier dans Xcode Interface Builder sur un Mac._

## <a name="how-to-open-a-storyboard"></a>Comment ouvrir une table de montage séquentiel 

Ouvrez un fichier d’interface utilisateur iOS dans Visual Studio pour Mac en cliquant avec le bouton droit sur un fichier de Storyboard et en sélectionnant **Xcode Interface Builder** :

[![Sélectionnez Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

Le lancement de la fenêtre Xcode doit s’afficher. Toutes les modifications enregistrées ici seront reflétées dans votre projet Visual Studio.

[![Fenêtre Xcode](images/xcode.png)](images/xcode.png#lightbox)

Vous trouverez plus d’informations sur le Interface Builder Xcode [ici](https://developer.apple.com/xcode/interface-builder/).

## <a name="known-problems"></a>Problèmes connus

Les problèmes connus sont décrits dans cette section.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>« Visual Studio n’a pas pu communiquer avec Xcode »

Dans macOS Catalina ou version ultérieure, vous pouvez rencontrer l’erreur ci-dessous :

[![Impossible de communiquer Err](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

Tout d’abord, assurez-vous que sous les préférences système de votre Mac sous **sécurité & confidentialité > Automation** que Visual Studio est listé et que **Xcode** est activé.

[![sécurité macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Si **Xcode** est coché et que le message d’erreur s’affiche toujours, vous devrez peut-être réinitialiser les autorisations de confidentialité Visual Studio pour Mac.

Pour ce faire, vous pouvez lancer une fenêtre de terminal et émettre la commande :

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Pour vous assurer que les modifications apportées ci-dessus prennent effet, réinitialisez le cinquième de votre Mac. Vous trouverez des instructions sur la procédure à suivre [ici](https://support.apple.com/HT204063).
