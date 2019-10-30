---
title: Est-il possible de créer une archive. xcarchive à partir de Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 27697223735c4074dd508d3f603ef6aa4e47b1be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031173"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>Est-il possible de créer une archive. xcarchive à partir de Visual Studio ?

## <a name="for-xamarin-4"></a>Pour Xamarin 4

À partir de Xamarin 4. x, il est désormais possible de créer une `.xcarchive` à partir de Windows en affectant à la propriété `ArchiveOnBuild` la valeur `true`. Par exemple, à l’aide de `MSBuild` sur la ligne de commande :

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

La `.xcarchive` est placée dans le répertoire `$HOME/Library/Developer/Xcode/Archives` sur l’hôte de build Mac, que Xcode et Xamarin Studio recherchent pour afficher les archives précédemment générées.

Consultez ce [billet Xamarin Forum](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) pour obtenir des remarques supplémentaires sur la propriété de `ArchiveOnBuild`. Pour plus d’informations sur les propriétés `ServerAddress` et `ServerUser`, consultez la documentation sur les [Builds de ligne de commande Xamarin. iOS sur Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

* * *

## <a name="for-xamarin-3-and-earlier"></a>Pour Xamarin 3 et versions antérieures

L’extension Xamarin 3. x de Visual Studio ne fournit pas de mécanisme permettant de produire des Archives de `.xcarchive`. Cela dit, la logique utilisée pour créer des Archives de `.xcarchive` dans Xamarin Studio sur Mac [est décrite ici](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5). vous pourriez donc probablement créer votre propre `.xcarchive` manuellement, si vous le souhaitez.

Toutefois, il est intéressant de noter que vous n’avez pas besoin d’un `.xcarchive` à envoyer à l’App Store. Vous pouvez envoyer un fichier de la Loi d’aide à la signature, à condition qu’il soit signé avec un profil de distribution App Store (pas un profil de distribution ad hoc).

En fait, il vous suffit de compresser le groupe `.app` (qui est signé avec un profil de distribution App Store) et de soumettre ce fichier `.zip` à l’App Store.

Dans les deux cas, vous pouvez utiliser l’application de chargeur d’application pour soumettre l’application (plutôt que Xcode).
