---
title: Est-il possible de créer une archive. xcarchive à partir de Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 6d35827b00a4ccc9bbe3e71444536425e4e1c3b1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288121"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>Est-il possible de créer une archive. xcarchive à partir de Visual Studio ?

## <a name="for-xamarin-4"></a>Pour Xamarin 4

À partir de Xamarin 4. x, il est désormais possible de créer `.xcarchive` un à partir de Windows `ArchiveOnBuild` en affectant à `true`la propriété la valeur. Par exemple, à `MSBuild` l’aide de sur la ligne de commande :

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

Le `.xcarchive` sera placé dans le `$HOME/Library/Developer/Xcode/Archives` répertoire de l’hôte de build Mac que Xcode et Xamarin Studio Rechercher pour afficher les archives précédemment générées.

Consultez ce [billet de forums Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) pour obtenir des remarques supplémentaires sur `ArchiveOnBuild` la propriété. Pour plus d’informations sur les propriétés et, `ServerUser` consultez la documentation sur les `ServerAddress` [Builds de ligne de commande Xamarin. iOS sur Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

* * *

## <a name="for-xamarin-3-and-earlier"></a>Pour Xamarin 3 et versions antérieures

L’extension Xamarin 3. x de Visual Studio ne fournit pas de mécanisme permettant `.xcarchive` de produire des archives. Cela dit, la logique utilisée pour créer `.xcarchive` des archives dans Xamarin Studio sur Mac [est décrite ici](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5). vous pourriez donc créer votre propre `.xcarchive` manuel si vous le souhaitez.

Toutefois, il est intéressant de noter que vous n’avez `.xcarchive` pas besoin d’un à envoyer à l’App Store. Vous pouvez envoyer un fichier de la Loi d’aide à la signature, à condition qu’il soit signé avec un profil de distribution App Store (pas un profil de distribution ad hoc).

En fait, vous pouvez même simplement compresser `.app` l’offre groupée (signée avec un profil de distribution App Store) et soumettre `.zip` ce fichier à l’App Store.

Dans les deux cas, vous pouvez utiliser l’application de chargeur d’application pour soumettre l’application (plutôt que Xcode).

